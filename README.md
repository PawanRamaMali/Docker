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

* Run docker in a new container
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

Example
```
docker start -i ca12345
```

* List of containers
```
docker ps [OPTIONS]
```
Example
```
docker ps -a
```

* List of docker images
```
docker images [OPTIONS] [REPOSITORY[:TAG]]
```
Docker image has intermediate layers that increase reusability, decrease disk usage and speed up docker build by allowing each step to be cached! these intermediate layers are not shown by default.

Example
```
docker images -a
```

* Stop one or more running container
```
docker stop [OPTIONS] CONTAINER [CONTAINER...]
```
Example
```
docker stop -t 100 cont_name
```

* Kill one or more running containers
```
docker kill [OPTIONS] CONTAINER [CONTAINER...]
```
Example
```
docker kill cae
```

* Run a command in a running container
```
dockr exec [OPTIONS] CONTAINER [ARGS...]
```
COMMAND should be an executable, a chained or a quoted command will not work. 
Example:
`docker exec -ti my_container "echo a && echo b" `will not work, but
`docker exec -ti my_container sh -c "echo a && echo b"` will.

Example
```
docker exec -it 120 bash
```
