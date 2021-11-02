# Simple Docker

## History

Old way

```text
server : application 1 1
```

Current way:

```text
OS    OS    OS     <== consumes resources (RAM, HD, CPU) before even an app installed /licensing  | better security
VM1 VM2 VM2..   x 10
<— Hypervisor —>
Physical Machine x 1
```

## What is a container?

```text
app               app               app
container      container      container  
<— OS  —>
<— VM  —>
```

The single ‘user space’ is now a within a container which allows for multiple user spaces via containers. Each container has its user space and app.

Question! Why not just install all apps within a ‘user space’ or container? We cannot share resources between applications. This filesystem, pid etc.

Containers are essentially runtime environment which consume low resources.

Question ! Can a container run on multiple operating systems?

No, containers use the OS kernel.

## How do containers work?

* Each container will have its own root file system
* Isolated process trees
* Isolated network stack
* Isolated users

## How do you achieve this?

* kernal namespaces I.e: pid mnt net user
* cgroups Helps us group together resources and apply limits \(flex resources\)

Map a cgroup to a container and set limits to memory, cpu. block IO has access to cgroup = container

* Capabilities Fine grain control on privileges

## What is Docker?

```text
Docker 
    |
[OS [Kernel[ namespaces| cgroups | capabilities ]]]
```

Docker is an implementation of a container. It provides a standard runtime for apps.

## What is the difference between Docker and LXC?

Docker previously used `LXC` as the execution driver to talk to the kernel.

`LXC` version needs to be compatible with Docker and because it is not developed by docker, now, docker uses the `libcontainer` as the default execution driver.

## What is the docker client and daemon?

The docker client sends commands to the docker daemon. The docker daemon is responsible for setting up containers which includes setting up cgroups, capabilities etc.

## Installation

On centos 6.5 do:

```text
sudo yum install docker-io

# Check info
sudo docker info
sudo docker version
```

My docker instance failed to start with the following error:

```text
/usr/bin/docker: relocation error: /usr/bin/docker: symbol dm_task_get_info_with_deferred_remove, version Base not defined in file libdevmapper.so.1.02 with link time reference
```

I just needed to update the `device-mapper-event-libs` package.

Reference:

[http://stackoverflow.com/questions/27216473/docker-1-3-fails-to-start-on-rhel6-5](http://stackoverflow.com/questions/27216473/docker-1-3-fails-to-start-on-rhel6-5)

## Configure Docker to Communicate over Network

```text
# Stop the service
sudo /etc/init.d/docker stop

# -H Bind to IP and port (Host option)
# -d Start in Daemon mode
# '&' is to give us the prompt back
sudo docker run -H 192.168.33.10:2375 -d &
```

Now the client needs to be configured to listen on the network port instead of the local unix socket:

```text
export DOCKER_HOST="tcp://192.168.33.10:2375"
```

Now when you run docker commands it will communicate via the network port. This also means you're local client can communicate with a remote docker container.

## Containers on the Machine

Dockerizing Applications: A "Hello world" Working with Containers

```text
# List running container
docker ps

# List containers
docker ps -a

# To start container
docker start a5da0b881f63

# Attach to container
docker attach a5da0b881f63

# To stop a container
docker stop <name>

# Show standard output of a container
docker logs <name>
```

## Docker Images

```text
# commit
docker commit sharp_wilson haani-niyaz/httpd

# View images
docker images

# delete cotainer
docker rm sharp_wilson

# delete image
docker rmi <image-id>
```

## How to Run a Web Server inside a Docker Container and Access it via your Host

I had previously created a centos container by running `docker run centos /bin/bash`

Now when I do `docker ps -a` I get a container ID `a5da0b881f63` \(this would be different for you\) and a name of 'sharp\_wilson' which is auto assigned by Docker and will be different for you.

```text
# Start docker
docker start sharp_wilson

# Check if its running
docker ps

# Attach to container
docker attach sharp_wilson

# Install httpd in container
yum install -y httpd

# Exit
CTRL+P and CTRL+Q

# Create an image
docker commit  a5da0b881f63 haani-niyaz/httpd

# Run the new container with port mapping
# You are mapping local port 9999 to the container's httpd listening port 80
docker run -it -p 9999:80 haani-niyaz/httpd /bin/bash &

# Check if running and Attach as before

# Run apache once in your container's shell
apachectl

# Now you can access the webserver via the browser
# I am using my host VM's IP
http://192.168.33.10:9999
```

