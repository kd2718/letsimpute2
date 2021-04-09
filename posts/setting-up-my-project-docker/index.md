<!--
.. title: Setting up my Project Docker
.. slug: setting-up-my-project-docker
.. date: 2021-03-27 21:46:45 UTC-07:00
.. tags: Docker
.. category: CICD, Docker
.. link: 
.. description: I try to make docker easy
.. type: text
-->

## Intro

So I was getting ready to continue my blog about [DE with Azure](link://slug/data-engineering-with-airflow-and-azure), but ended up getting COVID-19. Opps! I am OK, but it through me off
for a while. I am ready to get back on track now. I was spending a ton of time getting docker working correctly for this blog and I thought I might
as well write about what I am doing to make it all work.

This post will not go over the docker basics and assume you already know how to run a container. I wanted to go over what I discovered modifying
an existing container to better suit my needs.

<!-- TEASER_END -->

## The old Container
So I was using [puckel's airflow docker](https://hub.docker.com/r/puckel/docker-airflow) and noticed that he was still using Airflow `1.10.X`.
Airflow 2 is out now and I wanted to take advantage of this. On top of that, airflow released it's own `docker-compose.yml` file that creates a
celery executor for airflow. I had previously been using the single executor in my internal testing and found it very annoying. Most of my tasks would be blocked by some old task still hanging around. Things that should have been run in parallel were not. It was a mess.

I wanted to use the new [official airflow docker](https://hub.docker.com/r/apache/airflow) from apache. Puckel's old airflow was great, but it didn't look like it was getting updated to `2.0`. On top of that the new `docker-compose.yml` used the official apache airflow container.

Pulling the `apache/airflow:2.0.1` container from dockerhub, things looked OK. This airflow container had the required `apache-airflow[azure]` modules isntalled. Running this container did create an issue, however, when it seemed that I was missing the underlying azure libraries. Oops!

## Made Anew

So i had done some very old `Dockerfile` that I had made modifications to. However, it was a huge mess. I really didn't understand what was going on in it:


<script src="https://gist.github.com/kd2718/f774e134843c7b7304a80276efe9f03a.js"></script>

It is pretty ugly. The main issue is that I tried to copy Puckle's build script and adapt it for airflow 2.0. Good news is that it did work for my purposes, but was a total mess. I turned the above into a much more sensible version here:

<script src="https://gist.github.com/kd2718/bacbcb245afc88a9877ba6713c51691a.js"></script>


## Breaking it down

So one thing that I was not clear on is that the many of the previous options from a dockerfile persist even if you update it. You can see this because I no longer have to specify a `CMD` or `entrypoint` parameter. These would tell the docker instance what to run on startup. But just building and running the container from my file still ran the default `airflow` command.

I also cleaned up the pip install. This was one of the most important parts of making this dockerfile. I was manually installing a few azure packages so I can actually use the airflow azure provider in the container. However, I didn't pin the packages or have them in any kind of sensible order. I used `pip freeze` to get the full package requirements after installing what I needed.

At first I had a hard time trying to get the container to run `pip freeze` for me. The entry point was the airflow cli so that wasn't going to work. I tried using the entrypoint command line argument with docker, but that just gave an error.

```shell
# this gets me into the container, but doesn't let me save to my computer
docker run --entrypoint bash -it registry.gitlab.com/koryd2718/airflow2_demo:latest

# this doesn't work
$ docker run --entrypoint "bash pip feeze" -it registry.gitlab.com/koryd2718/airflow2_demo:latest

docker: Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "exec: \"bash pip feeze\": executable file not found in $PATH": unknown.
ERRO[0000] error waiting for container: context canceled 

```

After a lot of fiddeling around and reading the docs I realized I was really close on my second try. the `entrypoint` option needs to be set to bash. This tells the container what to run. Next I can use `-c` to pass in the command. This tells the container what arguments to give the entrypoint.

```shell
$ docker run --entrypoint bash -it registry.gitlab.com/koryd2718/airflow2_demo:latest -c "pip freeze" > af2_requirements.txt
```

This finally dumps the requirements list with pinned package versions so I can duplicate this later.

## Conclusion

* If you are updating an existing Docker Container, don't re-invent the wheel. Add just what you need.

* You can quickly pass information from docker to the host by taking advantage of `--entrypoint` and `-c` options while using `docker run`.

Docker had a lot to figure out. Although I had used docker many time in the past, I had not tried to customize my own container like this before. You can add just what you need to a container in a Dockerfile without needing to re-invent the wheel. Use `entrypoint` and the `-c` command to tell your docker what to run. Don't forget to use `-it` to create an interactive terminal.

Before I go, you may have noticed the `gitlab` registry appended to my container names. I have been experimenting with [gitlab](https://gitlab.com/) and they have a lot of great stuff. I hope to write about it in my next blog and share with you how to automate building and saving your containers to gitlab.