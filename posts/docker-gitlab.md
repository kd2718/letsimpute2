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

So how do you 