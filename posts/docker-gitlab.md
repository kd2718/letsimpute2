<!--
.. title: Docker & Gitlab
.. slug: docker-gitlab
.. date: 2021-04-01 21:25:52 UTC-07:00
.. tags: Docker, Gitlab
.. category: CICD
.. link: 
.. description: Gitlab superpowers Docker Build Times
.. type: text
-->

## Intro

Docker containers are great. Building a container to meet your needs and requirements is even better. In my [last post](link://) I mentioned I would talk more about why my containers have gitlab in the name. Here is that post. TLDR is that I am using the Gitlab container registry. 

The only competitive ISP in my area gives me a decent 200 Mbps down and 5 Mbps up. **5**!!!! It takes __hours__ to send larger files to google drive. Although it can be a headache, I feel the pain most when I am working on docker containers.

## Heavy Containers

I am no docker expert, but my containers seem to be somewhat on the large side. I think there are a lot of things I could be doing better to help with size, but in the meantime I am stuck with my nearly 2GB file. So with my 5 Mbps up that is about 50 minutes to send 1 file. This is insane. I thought there had to be a better way.

I had recently been looking more into [Gitlab](https://gitlab.com/) as an alternative to [Github](https://github.com/). Don't get me wrong, I love Github, and you should too. But I thought their docs where a little hard to parse at times. Gitlab on the other hand has some truly [amazing documentation](https://docs.gitlab.com/). Now in all fairness, I am pretty sure Github has a container registry as well. However, Gitlabs docks helped me get up and running in no time. On top of that, I can build my docker container inside of Gitlab. This takes about 8 minutes. What an improvement!!

## Setup

Start by creating a Gitlab repo. In this repo, create your `Dockerfile`. Here is a basic one:

```Dockerfile
FROM apache/airflow:2.0.1-python3.8
#LABEL maintainer="<your_name>"

RUN pip install ipython
```

This should install `ipython` for use inside this airflow container. Make sure your file is working by running `docker build . -t mytestdocker`. Check that everything is ok with `docker run -it --entrypoint bash mydockertest -c ipython`. This should dtop you into the Ipython shell.

Now we can start taking advantage of Gitlab. First, we need to initialize our project with `git init`, then create a repo on Gitlab to push it to. Once we have this setup, we can create a pipeline.

## Gitlab CI/CD Pipeline

We have our git project with a single `Dockerfile` in it. Now we can create a `.gitlab-ci.yml` file. In it, we tell gitlab how what to do to build our project. Gitlab has runners that will run the instructions for us. We have limited minutes per month, but you can get more if you register a free computer to work for you. However, then we are stuck back using my bad upload speeds again.

```yaml
build:
  image: docker:19.03.12
  stage: build
  services:
    - docker:19.03.12-dind
  variables:
    IMAGE_TAG: $CI_REGISTRY_IMAGE:latest
  only:
    refs:
      - master
    changes:
      - Dockerfile
  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
```

This file tells gitlab to use the docker:19.03.12 runner and to build our container then push it to 