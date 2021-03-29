<!--
.. title: Setting up my Project Docker
.. slug: setting-up-my-project-docker
.. date: 2021-03-27 21:46:45 UTC-07:00
.. tags: Docker
.. category: CICD
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

## The old Container
So I was using [puckel's airflow docker](https://hub.docker.com/r/puckel/docker-airflow) and noticed that he was still using Airflow `1.10.X`.
Airflow 2 is out now and I wanted to take advantage of this. On top of that, airflow released it's own `docker-compose.yml` file that creates a
celery executor for airflow. I had previously been using the single executor in my internal testing and found it very annoying. Most of my tasks would be blocked by some old task still hanging around. Things that should have been run in parallel were not. It was a mess.

I wanted to use the new [official airflow docker](https://hub.docker.com/r/apache/airflow) from apache. Puckel's old airflow was great, but it didn't look like it was getting updated to `2.0`. On top of that the new `docker-compose.yml` used the official apache airflow container.

Pulling the `apache/airflow:2.0.1` container from dockerhub, things looked OK. This airflow container had the required `apache-airflow[azure]` modules isntalled. Running this container did create an issue, however, when it seemed that I was missing the underlying azure libraries. Oops!

## Made Anew

So i had done some very old `Dockerfile` that I had made modifications to. However, it was a huge mess. I really didn't understand what was going on in it:


<script src="https://gist.github.com/kd2718/f774e134843c7b7304a80276efe9f03a.js"></script>

It is pretty ugly.