---
created: 2021-01-20T14:38:23+01:00
modified: 2021-01-26T19:48:37+01:00
---

# Docker_ASIX

# Introduction

Hi, this document is intended to help M**se and ASIX students.

## What is a docker?

A docker is a isolated*1 virtual machine.

*1 The docker is isolated by default unless it's specified in the configuration to not be isolated.

## How dockers work?

Dockers are preconfigured systems, usually supplying a single function or service (but not necessarily) and only with the packages necessaries to accomplish his commitments, the idea behind this is trying to use the minimum of resources as possibles, leaving more usable space.

## Why use dockers?

As noted before, dockers are meant to be optimised and efficient trying to use the minimum recourses necessaries.

Also, since they are isolated virtual machines, in case someone was able to reach inside our docker and take control of it, he wouldn't be able to reach our machine unless we allowed some sort of communication between those two, which is unlikely to happen, and in case the intruder managed to stop our service (ie. web service), it would restart*1, or to get rid from the intruder we could just restart the service*2.


*1 Docker allows you to configure different actions on shutdown, in this case we would want to restart.

*2 Each time the docker starts, it doesn't conserve any file from the past sessions, still, you can configure persistence specified files or folders (and specify if this ones can be modified), allowing you to use custom configuration and maintain the changes for the next session (ie. if you have a database, ftp server, mail server, etc.).


# How to start

## How to install

### Debian based distributions

Follow the instructions located in the next link.

https://docs.docker.com/engine/install/ubuntu/

### Arch based distributions

Follow the instructions located in the next link.

https://wiki.archlinux.org/index.php/Docker

## Check instalation
```shell
docker -v
```

## Start docker service

To start using dockers you need to start the service (unless it's alredy running) 

### Service (Debian based distros)
```shell
sudo service docker start
```

### Systemctl (Arch based distros)
```shell
sudo systemctl start docker
```

## Enable docker service

You can enable the service instead of start it, so it starts on boot, but not required

# docker run

## First example
```shell
docker run ubuntu:latest ls /
```

### Explanation


```shell
docker run ubuntu:latest ls /
```
    <docker> <run> <ubuntu>:<latest> <ls />

    <package> <function> <docker image from docker.hub>:<version from the image> <command> 

Here we are starting a virtual Ubuntu, with the latest version aviable at Docker Hub,  and executing the command "ls /"

### View active dockers
```shell
docker container ls
```

Will return a list of active dockers, and some information about it.

By default if the Docker don't have nothing to keep it busy it will shutdown by himself, what does it mean? If you used the first "docker run" example, after executing the command given it shutdown.

That's why at the moment, there is no container in the container list.


### Avoid docker from shutting down

```shell
docker run -t -d ubuntu
```

By default, if you don't specify the docker version it will try to use the one that you have alredy downloaded in your machine, if can't find it in your machine will proceed to download the latst version.

> -d: Dettach from the terminal, this way you can keep working with the terminal and close it if necessary, if you don't understand it we can say it sends the docker to the background.

> -t: Prevents the Docker from shutting down once has no labors left.

### Connect to running docker

First step is to get the Docker container id
```shell
docker container ls
```

Once we have the id we have to run the next command.
```shell
docker container exec -it $DOCKERID bash
```

We use bash to attach our terminal to the container one, but some systems might not have "bash", instead of bash we can use "sh".
```shell
docker container exec -it $DOCKERID sh
```

Now that we are connected to the terminal we could execute commands inside the docker.
This is useful to explore the default files of someone Docker Images.

Still, we can execute other commands without get attached.
```shell
docker container exec -it $DOCKERID ls /
```

### Stop running docker

Now that we know how to get the cotainer id, it's time to remove the ubuntu docker that we just start.

First, we need to stop the container, to do this we need get again the container id.
```shell
docker container ls

docker container stop $ID
```
Once the container is stopped, we can proceed to remove it.
```shell
docker container stop $ID
```
### Nginx example (web server service)
```shell
docker run --name desired-container-name -d -p 8080:80 nginx
```
> -p: <our_port:docker_port>  Let's us set port forwarding from a Docker container to our actual computer, in this case, we are taking the port 80 from the nginx container, and placing it in our port 8080.

> --name: Let's us setup a custom docker container image.

In this case we don't need to use -t to avoid the container from closing since the service itself will prevent it from closing while it works correctly.\

To test if it works you can open http://localhost:80 or http://localhost.

### Nginx example but with custom files

This time we will use a custom index.html file, instead of the nginx default one.

First step is to create the file and it's content, we are okay with a simple one.
```shell
printf "<h1>HI IM A CUSTOM INDEX.HTML</h1>"> index.html
```
Now it's time to run the docker container with our custom file.
```shell
docker run --name desired-container-name -d -p 8080:80 -v  "$(pwd)/index.html:/usr/share/nginx/html/index.html:ro" nginx
```
Now, if we check our webpage again, we can see our new message.

We can also share an entire folder.
```shell
mkdir folder
printf "<h1>HI IM A CUSTOM INDEX.HTML</h1>"> folder/index.html
$ docker run --name desired-container-name -d -p 8080:80 -v "$(pwd)/folder:/usr/share/nginx/html/:ro" nginx
```
> -v: Lets us substitude a folder or a file in our docker container by the folder or file specified, we can also use virtual volumes in case we created them.

### Ubuntu with shared folders

For this example is important that we don't create any folder.
```shell
docker run -t -d -v "$(pwd)/newfolder/:/shared/:rw"  ubuntu:latest
```
Now we need to get the Docker container id so we can connect to the container.

```shell
docker container ls
```
Once we find the Docker id we need to connect to the container and attach the terminal

```shell
docker container exec -it $CONTAINER_ID bash
```
Now that we are connected to the container, it's time to create a file in "/shared" 

```shell
prtintf "Hi, file created from Docker container!" > /shared/newfile
```
Once the file is created just left exit the container and check our new folder.

```shell
exit
ls ./newfolder
cat ./newfolder/newfile
```

As we can see, we didn't need to create the folder to share it, docker create it for us.


# docker-compose

## docker-compose explanation

Docker compose let us set up multiple docker container from a single file, making it easier to config and to manage.   

## docker-compose installation

Follow the instructions located in the next website.

https://docs.docker.com/compose/install/

## docker-compose first example (php apache web)

First step is to create the file "docker-compose.yml" (the file can use ".yaml" instead of ".yml")

```shell
touch ./docker-compose.yml
```
Once we have the file created we need to insert the next text inside the created file:

```yaml
# docker-compose.yml
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

```php
<?php
#demo.php
phpinfo();
?>
```

Once we have the files created, we can proceed to start the docker-container.

```shell
docker-compose up
```

To stop it we can simply press control+C

[comment]: <> (checked till here.)

## docker-compose tags

https://docs.docker.com/compose/compose-file/

```yaml
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
```

To test if it works you can open http://localhost:8080.

## docker-compose documentation

https://docs.docker.com/compose/compose-file/compose-file-v3/

## docker-compose 2 services

This time we will use 2 services, to make things easy and fast, both will be web services, in this case we will use apache (not php apache) and nginx, with default files.

Also, this time, we will name the docker-compose file as "docker-compose2.yml".


```yaml
#docker-compose2.yml
version: "3.9"
services:
  web1:
    image: httpd
    ports:
      - target: 80
        published: 8080 # This time we decide to publish port 8080
  web2:
    image: nginx
    ports:
      - target: 80
        published: 8081 # This time we decide to publish port 8081
```

Once we created the compose file only left start it.
```shell
docker-compose  -f docker-compose2.yml up
```


>-f: Let us specify the path to the file desired to use.

To test if it works you can open http://localhost:8080 and http://localhost:8081.

# docker stacks

# Docker custom images

## docker pull

Docker pull let us download images to our system, so we don't have to download them later.

```shell
docker pull nginx:1.18-alpine
```

Now if we list our downloaded images, we should be able to see it.

```shell
docker images ls
```



## dockerfile

With dockerfiles we can create custom images from a base image, which later we will build.

First step we are going to need to create the dockerfile with it's content, which we will call 'dockerfile'.

For this example we will use the nginx version alredy downloaded.

```dockerfile
#dockerfile
FROM nginx:1.18-alpine
RUN printf "hi from dockerfile" | tee /usr/share/nginx/html/index.html
```

## docker build

We can build a custom docker image from a docker file.

```bash
docker build -t my_image .
```
We could do this instead.
```shell
docker build -t my_image . -f ./dockerfile
```

> -t: Let us specify the desired tag for the image

> -f: Let us specify the file to use, if not specified it will search "./dockerfile"   

### build from docker-compose

First step is to create the file docker-compose.

```yaml
# docker-compose.yml
version: "3.9"
services:
  my_service:
    build:
        context: .
        dockerfile: dockerfile
    ports:
      - target: 80
        published: 8080
```

Once we have the file created, it's time to rise it up, to do this we call it like a normal docker-compose file.

```shell
docker-compose up
```

To test if it works you can open http://localhost:8080.

### Run built images

#### Run with docker run
```shell
docker run -p 8080:80 my_image
```
As we can see, we are calling it as any other image with "docker run".

#### Run with docker-compose

First step is to create the docker-compose file.

To test if it works you can open http://localhost:8080.

```yaml
# docker-compose.yml
version: "3.9"
services:
  my_service:
    image: my_image
    ports:
      - target: 80
        published: 8080
```

Once we have the file created, it's time to rise it up, to do this we call it like a normal docker-compose file.

```shell
docker-compose up
```

To test if it works you can open http://localhost:8080.

## docker push

With the command 'docker push' we are able to push an image to our dockerhub account, and later publish it so it's available to other users.

https://docs.docker.com/engine/reference/commandline/push/

Probably it's not important to know as a beginner, but still important to know about its existence.

[comment]: <> (## Docker swarm)

[comment]: <> (https://docs.docker.com/engine/swarm/)

## docker stack/swarm

### Docker stack explanation

With docker stack we can manage groups of dockers, being able to have replicas of the same docker, in case for some reason the published one fails, next in the backup list will replace it, leading to better disponibility.

### Deploy docker replicas

In this first example we will use only one docker service, but with 2 replicas.

```yaml
# docker-compose.yml
version: "3.9"
services:
  my_service:
    image: my_image
    ports:
      - target: 80
        published: 8080
    deploy:
      replicas: 2
```

Once we have our docker-compose file created, it's time to deploy our stack.

To deploy our stack first we need to initialize a swarm network, to do this we will use the next line:

```shell
docker swarm init
```

And finally we can join our stack to our inizialized swarm.

```shell
docker stack deploy -c docker-compose.yml my_stack
```

To test if it works you can open http://localhost:8080.

### Monitor your docker stacks

Now that we got our stack running, we can list our docker stacks.

```shell
docker stack ls
```

With the docker name that we used before (or that we can see with the command "docker stack ls"), we can list the container process running in our docker stack.

```shell
docker stack ps my_stack
```

As we can see, we have 2 stacks running right now, and if we check the names, we can see the names are $STACK_$SERVICE_1/2/...

### Test our docker replicas

Now it's time to test our replicas, to make things easy we will stop our active running container, so the backup one replace it.

To do this we need to list our running docker containers as we did previously, so we can obtain the container id from the desired container, remember the first container to be used is the one with the lower number in it's name, which should be 1.

```shell
docker stack ls
```

Once we have the container id, it's time to stop it.
```shell
docker container stop 961c5419ef6d
```

Finally we can check the process list from our stack and check the state of each container.

```shell
docker stack ps my_stack
```

As we can see, the container that we wanted to stop it's state it's "ready", and holds a subcontainer in it, which it's state it's "shutdown".

And under them we should be able to see the replica n2, which is in "running" state.

What happened regarding our first container is, once we forced the container to stop, the container shutdown, and restarted itself, but since our replica2 was ready and it took it's place, replica1 is holding in case replica2 stops working and need a replacement.

Now only left is checking if our replica is working properly.

To test if it works you can open http://localhost:8080.

If we want to do further testing we can stop the container from the replica2 and proceed to check again the status of the page and the status of the containers.


## docker volume && docker mount

https://docs.docker.com/storage/volumes/



# Credits

- Oriol Filter
- Internet