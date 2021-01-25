---
modified: 2021-01-25T14:52:07+01:00
---

# Docker_ASIX

# Introduction

Hi, this document is intended to help M**se and ASIX students.

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

## Docker installation

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

    $ docker run ubuntu:latest ls /

### Explanation


     $ docker run ubuntu:latest ls /
    <docker> <run> <ubuntu>:<latest> <ls />

    <package> <function> <docker image from docker.hub>:<version from the image> <command> 

     Here we are starting a virtual Ubuntu, with the latest version aviable at Docker Hub,  and executing the command "ls /"

### View active dockers

      $ docker container ls

      Will return a list of active dockers, and some information about it.
By default if the Docker don't have nothing to keep it busy it will shutdown by himself, what does it mean? If you used the first "docker run" example, after executing the command given it shutdown.
That's why at the moment, there is no container in the container list.


### Avoid docker from shutting down

       $ docker run ubuntu -t -d


       By default, if you don't specify the docker version it will try to use the one that you have alredy downloaded in your machine, if can't find it in your machine will proceed to download the latst version.

       -d: Dettach from the terminal, this way you can keep working with the terminal and close it if necessary, if you don't understand it we can say it sends the docker to the background.

        -t: Prevents the Docker from shutting down once has no labors left.

### Connect to running docker


         First step is to get the Docker container id

         $ docker container ls

         Once we have the id we have to run the next command.

         $ docker container exec -it $DOCKERID bash

         We use bash to attach our terminal to the container one, but some systems might not have "bash", instead of bash we can use "sh".

       $ docker container exec -it $DOCKERID sh

       Now that we are connected to the terminal we could execute commands inside the docker.
This is useful to explore the default files of someone Docker Images.

       Still, we can execute other commands without get attached.

       $ docker container exec -it $DOCKERID ls /

### Nginx example (web server service)

      $ docker run --name desired-container-name -d -p 8080:80 nginx

       -p: Let's us set port forwarding from a Docker container to our actual computer, in this case, we are taking the port 8080 from the nginx container, and placing it in our port 80, to test if it works you can open http://localhost:80 or http://localhost.

       --name: Let's us setup a custom docker container image.

       In this case we don't need to use -t to avoid the container from closing since the service itself will prevent it from closing while it works correctly.

### Nginx example but with custom files

        This time we will use a custom index.html file, instead of the nginx default one.

        First step is to create the file and it's content, we are okay with a simple one.

         printf "<h1>HI IM A CUSTOM INDEX.HTML</h1>"> index.html

         Now it's time to run the docker container with our custom file.

        $ docker run --name desired-container-name -d -p 8080:80 -v index.html:/var/www/html/index.html:ro nginx

        Now, if we check our webpage again, we can see our new message.

        We can also share a entire folder.

        $ docker run --name desired-container-name -d -p 8080:80 -v folder_path:/var/www/html/:ro nginx

         -v: Lets us substitude a folder or a file in our docker container by the folder or file specified, we can also use virtual volumes in case we created them.

### Ubuntu with shared folders


     $ docker run -t -v ./newfolder:/shared:rw  ubuntu:latest

     Now we need to get the Docker container id so we can connect to the container.

     $ docker container ls

     Once we find the Docker id we need to connect to the container and attach the terminal


     $ docker container exec -it $CONTAINER_ID bash

     Now that we are connected to the container, it's time to create a file in "/shared" 

     $ prtintf "Hi, file created from Docker container!" > /shared/.newfile

     Once the file is created just left exit the container and check our new folder.

     $ exit
     $ ls ./newfolder
     $ cat ./newfolder/.newfile


# docker-compose

# docker stacks

# other things to keep in mind

## docker build

## docker pull

## docker push

## Docker file

## docker volume && docker mount


         https://docs.docker.com/storage/volumes/



# Credits

- Oriol Filter
- Internet