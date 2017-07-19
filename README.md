# Devops_docker
# Docker installation
## install docker
```apt-get install -y docker.io```
## start Docker with the systemctl command
```systemctl start docker```
## Enable docker to run at system boot:
```systemctl enable docker```
## check the docker version
```docker version```
# USAGE --- creating containers
## search for a base image with the docker search command
```docker search ubuntu```
## download the base image to our server
```docker pull ubuntu```
## see all downloaded images
```docker images```
## To create the container
```docker create ubuntu:16.04``` this creates a container using the downloaded image and the version needed.
**Note:** the above 'docker create' command will create a new container **but not start it**
## To create a container and start it immediately so you can run commands
```docker run -i -t ubuntu:16.04 /bin/bash```
## To exit the container
```exit``` **note:** The container will stop when you leave it with the command exit
## If you like to have a container that is running in the background, you just need to add the -d option in the command.
```docker run -i -t -d ubuntu:16.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"```
### Now you can see the container running in the background by using command
```docker ps```
### if you want to see the logs result from that bash command
```docker logs NAMES/ContainerID```
e.g
```docker logs abcddadadadedaswf```
### How can I access the shell of container that runs in the background mode?
```docker exec -i -t abcddadadadedaswf /bin/bash```
## stop container without deleting it
```docker stop abcddadadadedaswf```
## start container
```docker start abcddadadadedaswf```
## If you like to remove the container, stop it first and then remove it
```docker rm abcddadadadedaswf```
