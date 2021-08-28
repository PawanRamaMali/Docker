# Docker

Docker Course Contents for my students

## What is Docker?

It is a set of platform as a service (PaaS) products that uses OS-level virtualization to deliver software in packages called containers which helps developers,system admins to develop, deploy and run applications.

## Docker file

Docker image file is contains a set of instructions used to create docker containers.

Docker image is created using a docker image file.

## Docker Image 

A Docker image contains everything needed to run an application as a container. This includes:

* Code
* Libraries
* Environment variables
* Configuration files

The docker image file can then be deployed to any Docker environment and executable as a container.

## Docker Container

A Docker container is a runtime instance of an docker image.

From one image you can create multiple containers (all running the sample application) on multiple Docker platforms.

A container is a runnable instance of an image. You can create, start, stop, move, or delete a container using the Docker API or CLI. 
You can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state.

## Docker Commands

* Run a command in a new container
```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```
Example
```
docker run -it --name ubuntu_cont ubuntu:latest bash
```

* Start one or more stopped containers in detached mode
```
docker start [OPTIONS] CONTAINER [CONTAINER...]
```
