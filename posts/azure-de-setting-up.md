<!--
.. title: Azure DE - Setting Up
.. slug: azure-de-setting-up
.. date: 2021-04-19 21:27:40 UTC-07:00
.. tags: azure, airflow, DE
.. category: Azure-Airflow
.. link: 
.. description: 
.. type: text
.. status: draft
-->

## Intro

Ok, lets get started. Again, this is not a guide on how to go from zero to hero. This will not tell you that you need to install git or docker. The goal of this series to to help people that already know some of the basics to get into azure with airflow. But just in case, you should have both `git` and `docker` installed.

Recently I have been experimenting with gitlab. I am really happy with some of the easy I am able to do things on this platform. GitHub is still pretty great option. There is nothing about this project that requires either, so pick your favorite.

## Setup

I have a repo that we will reference. My plan is to have branches that serve as checkpoints. To get started run the following:

```shell
git clone git@gitlab.com:koryd2718/airflow2_demo.git
git checkout setting-up
```

This will take us to a clean branch that we can get going. First thing needed is to setup the `.env` file. docker-compose will read this file and use it to set a few things up. For now running this command:

```shell
echo "AIRFLOW_UID=$(id -u)\nAIRFLOW_GID=0" >> .env
```

This way if we create a file from inside docker we will have access to it. Note, if you are working on a production system, make sure your files have proper file permissions. For local dev this should be fine.
