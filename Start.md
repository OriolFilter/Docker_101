---
created: 2021-01-20T14:38:23+01:00
modified: 2021-01-22T20:11:55+01:00
---

# Start

# Introduction

## What is a docker?

A docker is a isolated*1 virtual machine.

*1 The docker is isolated by default unless it's specified in the configuration to not be isolated.

## How dockers work?

//Now that we can agree on what's a docker, we can take a further explanation.

Dockers are preconfigured systems, usually supplying a single function or service (but not necessarily) and only with the packages necessaries to accomplish his commitments, the idea behind this is trying to use the minimum of resources as possibles, leaving more usable space.

## Why use dockers?

As noted before, dockers are meant to be optimised and efficient trying to use the minimum recourses necessaries.

Also, since they are isolated virtual machines, in case someone was able to reach inside our docker and take control of it, he wouldn't be able to reach our machine unless we allowed some sort of communication between those two, which is unlikely to happen, and in case the intruder managed to stop our service (ie. web service), it would restart*1, or to get rid from the intruder we could just restart the service*2.


*1 Docker allows you to configure different actions on shutdown, in this case we would want to restart.

*2 Each time the docker starts, it doesn't conserves any file from the past sessions, still, you can configure persistence specified files or folders (and specify if this ones can be modified), allowing you to use custom configuration and maintain the changes for the next session (ie. if you have a database, ftp server, mail server, etc.).


# How to start

## Docker instalation

### apt

    sudo apt-get update
    sudo apt-get insall docker #?? check

### pacman

    sudo pacman -Ssy
    sudo pacman -S docker

## Check instalation

    docker -v

## Start docker service

    To start using dockers you need to start the service (unless it's alredy running) 

### Service (Debian based distros)

    sudo service docker start

### Systemctl (Debian based distros)

    sudo systemctl start docker

## Enable docker service

    You can enable the service instead of start it, so it starts on boot, but not required

# docker run

## First example

    docker run ubuntu:latest ls /

### Explanation

    <docker> <run> <ubuntu>:<latest> <ls />

    <package> <function> <docker image from docker.hub>:<version from the image> <command> 

     Here we are starting a virtual Ubuntu, with the latest version aviable at Docker Hub,  and executing the command "ls /"



# docker-composeacabo de acabar clase por lo que me to ca # docker stack