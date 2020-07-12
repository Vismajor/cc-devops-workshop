# Docker 101 - Getting started with containers

Containers offer way of packaging our applications which means they can be abstracted from the environment in which they actually run. This decoupling allows container-based applications to be deployed easily and consistently, regardless of whether the target environment is a private data center, the public cloud, or even a developer’s personal laptop. 

This gives developers the ability to create predictable environments that are isolated from the rest of the applications and can be run anywhere. This guide will focus on the most popular container technology, Docker, and show you how you can quickly spin up environments for you to host your applications and then provide a way to quickly package your applications and deploy anywhere.

## Prerequsites

To do this lab you'll need a system with Docker installed, if you don't already have it installed follow the link below which will take you through installing docker for your platform

https://docs.docker.com/get-docker/

When docker is sucessfully installed you should be able to run the ```docker --version``` command to verify that the docker daemon is running.

```
docker --version
```

![](https://github.com/sttrayno/Docker-101/blob/master/images/docker-v.gif?raw=true)


Alternatively, you can use an in-browser Docker playground called www.play-with-docker.com. The play-with-docker gives provides access to a full VM running Docker directly in a web browser, making it easy to work with Docker from any device.

To use this open www.play-with-docker.com on a browser the site displays a Captcha dialog to ensure that you're not a robot. Press Play and complete the Captcha to continue. The play-with-docker site displays a session countdown and an Add New Instance button. 

Click the Add New Instance button. The site creates and displays a terminal session in the browser. The rest of this learning lab uses the in-browser terminal session to work with Docker.

## Step 1 - Hello world

The simplest way to use Docker is to run an existing public image that's available from the [Docker Hub](hub.docker.com)

Docker Hub is a public exchange for sharing Docker containers. Other container sharing sites are available, but we'll take advantage of the fact that Docker's command-line interface searches DockerHub by default. Dockerhub is by far the most common and the largest

To run a publicly-available Docker Container, follow these steps:

Search for a container named "hello-world." Use the command docker search hello-world to find the "hello-world"" image:

```
docker search hello-world
```

![](https://github.com/sttrayno/Docker-101/blob/master/images/hello-world.gif?raw=true)

Docker searches the public DockerHub repositories and finds the "hello-world" image. You can see theres a few however theres only one with the name hello-world, so lets run it with the command:

```
docker run hello-world
```

![](https://github.com/sttrayno/Docker-101/blob/master/images/run-hello-world.gif?raw=true)


Docker first checks to see whether the "hello-world" image is available locally. If not, Docker automatically downloads it from DockerHub. Docker sets up the container to run locally, including ensuring its isolation from other processes. Once the preparations are made, Docker runs the image.

Congratulations! You just ran your first Docker container! Give yourself a pat on the back

## Step 2 -  A look at the dockerfile

Now we've ran our first container sucessfully and we know our docker environment is working well it's time to go a little more into the key concepts. One of the first concepts you need to understand to do anything with containers is the concept of the dockerfile as this is key to building containers.

Docker can build images automatically by reading the instructions from a Dockerfile. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.

An example dockerfile might look like below. For a full list of commands and details around what you can do in a dockerfile follow [this link](https://docs.docker.com/engine/reference/builder/)

```
FROM ubuntu
RUN apt-get update 
RUN apt-get -y install python
````

Lets build this text file. First create a new directory and then create a file named dockerfile and copy the above into it.

```
mkdir example1
cd example1
```

Feel free to do this on the GUI rather than CLI as shown. 

```
vi dockerfile
```

Lets disect that Dockerfile we've just created a bit a bit

A Dockerfile must begin with a `FROM` instruction. The FROM instruction specifies the Parent Image from which you are building. If you don't want to specify an image you can use the argument scratch however we're going to base the image we build here off of Ubuntu to make life a bit easier for us

The RUN commands then specify commands you wish to run locally on your container, in our case we're going to update our package manager and install python. This can be as simple as you want or complex as you want, lets keep it simple for now

That's all there is to it. Now we need to build our container into an image so that Docker can run it. To do that, make sure you're in the same directory on your command line as your docker file is and run the command:

```
docker build .
```

![](https://github.com/sttrayno/Docker-101/blob/master/images/docker-build.gif?raw=true)


After the Docker build completes you're now ready run the new container.  When it finishes building it should give you an image id which is a alphanumeric string after the output says the container has been sucessfully built. Take a note of that as you'll need it to run the next command:

```
docker run -it <image-id> /bin/bash
```

![](https://github.com/sttrayno/Docker-101/blob/master/images/docker-run.gif?raw=true)

Pretty nice, you should now be in a Linux shell and now have somewhere to run our next Linux module...

Also congratulations, you just built your first docker container from scratch.
