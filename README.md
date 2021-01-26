---
created: 2021-01-20T14:38:23+01:00\
modified: 2021-01-25T15:41:56+01:00
---

# Docker_ASIX

# Introduction

Hi, this document is intended to help M**se and ASIX students.

## What is a docker?

A docker is a isolated*1 virtual machine.

*1 The docker is isolated by default unless it's specified in the configuration to not be isolated.

## How dockers work?

[comment]: <>(//Now that we can agree on what's a docker, we can take a further explanation.)

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
    sudo apt-get install docker #?? check

Debian/Ubuntu repositories tends to be older versions (2-3 years old) but with ensured stability.

### pacman

    sudo pacman -Ssy
    sudo pacman -S docker

## Check instalation

    docker -v

## Start docker service

To start using dockers you need to start the service (unless it's alredy running) 

### Service (Debian based distros)

    sudo service docker start

### Systemctl (Arch based distros)

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

    $ docker run -t -d ubuntu


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

### Stop running docker

Now that we know how to get the cotainer id, it's time to remove the ubuntu docker that we just start.

First, we need to stop the container, to do this we need get again the container id.

    $ docker container ls

    $ docker container stop $ID

Once the container is stopped, we can proceed to remove it.

    $ docker container stop $ID

### Nginx example (web server service)

    $ docker run --name desired-container-name -d -p 8080:80 nginx

    -p: <our_port:docker_port>  Let's us set port forwarding from a Docker container to our actual computer, in this case, we are taking the port 80 from the nginx container, and placing it in our port 8080.

    --name: Let's us setup a custom docker container image.

In this case we don't need to use -t to avoid the container from closing since the service itself will prevent it from closing while it works correctly.\
To test if it works you can open http://localhost:80 or http://localhost.

### Nginx example but with custom files

This time we will use a custom index.html file, instead of the nginx default one.

First step is to create the file and it's content, we are okay with a simple one.

    printf "<h1>HI IM A CUSTOM INDEX.HTML</h1>"> index.html

Now it's time to run the docker container with our custom file.

    $ docker run --name desired-container-name -d -p 8080:80 -v  "$(pwd)/index.html:/usr/share/nginx/html/index.html:ro" nginx

Now, if we check our webpage again, we can see our new message.

We can also share an entire folder.

    $ mkdir folder

    $ printf "<h1>HI IM A CUSTOM INDEX.HTML</h1>"> folder/index.html

    $ docker run --name desired-container-name -d -p 8080:80 -v "$(pwd)/folder:/usr/share/nginx/html/:ro" nginx

    -v: Lets us substitude a folder or a file in our docker container by the folder or file specified, we can also use virtual volumes in case we created them.

### Ubuntu with shared folders

For this example is important that we don't create any folder.

    $ docker run -t -d -v "$(pwd)/newfolder/:/shared/:rw"  ubuntu:latest

Now we need to get the Docker container id so we can connect to the container.

    $ docker container ls

Once we find the Docker id we need to connect to the container and attach the terminal

    $ docker container exec -it $CONTAINER_ID bash

Now that we are connected to the container, it's time to create a file in "/shared" 

    $ prtintf "Hi, file created from Docker container!" > /shared/newfile

Once the file is created just left exit the container and check our new folder.

    $ exit
    $ ls ./newfolder
    $ cat ./newfolder/newfile

As we can see, we didn't need to create the folder to share it, docker create it for us.


# docker-compose

## docker-compose explanation

## docker-compose installation

## docker-compose first example (php apache web)

First step is to create the file "docker-compose.yml" (the file can use ".yaml" instead of ".yml")

    $ touch ./docker-compose.yml

Once we have the file created we need to insert the next text inside the created file:

```
#docker-compose.yml
version: "3.9"
services:
  web:
    image: php:7.0-apache
    ports:
      - target: 80
        published: 8080 # This time we decide to publish port 8080
    volumes:
      - ./demo.php:/var/www/html/index.php:ro
```

Now, it's time to create the file "demo.php"

```
<?php
#demo.php
phpinfo();
?>
```

Once we have the files created, we can proceed to start the docker-container.

    $ docker-compose up

To stop it we can simply press control+C

[comment]: <> (checked till here.)

## docker-compose tags

https://docs.docker.com/compose/compose-file/

    version: "3.9" # which version of docker-compose use. I don't really understand how it works at all, so i guess the later the better, I think it takes as a integer, so if you use 3.9 it will save it as 3, instead of a float 3.9, this field is optional since docker-composer version 1.27.0

    services: # list of services to create, the service name isn't intended to be the service name or the docker name, you can place the name that helps you idenftify the service.

      service_name: # As said previously, this name is field to be custom, in the example shown before we call it "web".

        image: php:7.1.2-apache # docker-container-image:version

        # build: # In case we want to use a docker file instead of a docker from dockerhub, docker file will be adressed later

         # context: Directory where docker file is located (with respect of the location from the user executing the file)

         # build: File name, in case the file is called "dockerfile" we can use a dot '.', otherwise we will use './dockerfile'.

        ports: # Ports to publish with from the docker
          
          # - docker_port:published_port 
          # - "docker_port:published_port"

           - target: 80 # docker port
             published: 8080 # published port
             protocol: tcp # tpc/udp # Optional, only select one tcp or udp, by default it will choose tcp
             # mode: host # Optional

        restart: on-failure # restart policy, what to do once container stops working, this option is ignored on swarm mode.
        # restart: "no"
        # restart: always
        # restart: on-failure
        # restart: unless-stopped
        # network_mode: host

        volumes: # Volumes to share with the docker
          - type: bind # instead of using a created volume it uses a folder or file in our system. 
            source: ./demo.php
            target: /var/www/html/index.php
            read_only: true

         # - ./demo.php:/var/www/html/index.php:ro # This is the simple way

To test if it works you can open http://localhost:8080.

## docker-compose documentation

https://docs.docker.com/compose/compose-file/compose-file-v3/

## docker-compose 2 services

This time we will use 2 services, to make things easy and fast, both will be web services, in this case we will use apache and nginx, with default files

```
#docker-compose.yml
version: "3.9"
services:
  web1:
    image: php:7.0-apache
    ports:
      - target: 80
        published: 8080 # This time we decide to publish port 8080
  web2:
    image: nginx
    ports:
      - target: 80
        published: 8081 # This time we decide to publish port 8081
```

To test if it works you can open http://localhost:8080 and http://localhost:8081.

# docker stacks

# other things to keep in mind

## docker build

## docker pull

## docker push

## Docker file

## .env file

## docker volume && docker mount

         https://docs.docker.com/storage/volumes/



# Credits

- Oriol Filter
- Internet