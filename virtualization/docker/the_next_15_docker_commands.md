# The Next 15 Docker Commands

In an earlier post for the Docker Tutorial Series, we discussed the first 15 Docker commands. We shared some hands-on experience in how they are used and what they do.

In this post, we will talk about another 15 Docker commands, leading us to a more practical experience using Docker.

Here they are:

## daemon:

Docker daemon is the persistent background process that helps manage containers. In general, daemon is a long running process servicing requests for services.

-d flag is used to run the daemon

## build:

As discussed before, Docker images can be built using dockerfiles. A simple command for the build is as follows:

```text
docker build [options] PATH | URL
```

There are also some interesting options that Docker provides, such as:

--rm=true; all intermediate containers are removed after a successful build

--no-cache=false; avoids using cache during build

The following screenshot depicts the use of the Docker build command.

## attach:

Docker allows interaction with running containers using the attach command. The command also allows viewing of daemonized processes. Detaching from the container can be done in two ways:

Ctrl+c - for a quiet exit

Ctrl- - to detach with a stack trace

The syntax for attach is

```text
docker attach container
```

This screenshot shows a simple execution of the attach command.

## diff:

Docker provides a very powerful command diff which lists the changes in the files and directories. The changes include addition, deletion and those represented by the A, D and C flags, respectively. This command improves debugging processes and allows faster sharing of environments.

The syntax is

```text
docker diff container
```

Here’s a screenshot of the diff command executed.

## events:

Real-time details of events can be collected from the server by specifying the duration for which the real-time data needs to be collected.

## import:

Docker allows imports from remote locations and a local file or directory. Import from remote locations is done using http, and imports from local files or directories is accomplished using the “-” parameter.

The syntax for import from a remote location is

```text
docker import http://example.com/example.tar
```

The following image illustrates import from a local file.

## export:

Similar to import, the export command is used to transfer the contents of the filesystem as a tar file. The following image depicts a simple execution of the same.

## cp:

This command copies files from the containers filesystems to the specified path. The syntax for the cp command is

```text
docker cp container:path hostpath.
```

This image depicts the working of the cp command.

## login:

A simple command to login the Docker registry server is Docker login with the following syntax:

```text
docker login [options] [server]
```

To login a self-hosted registry use the command

```text
docker login localhost:8080
```

## inspect:

Low-level information about containers and images can be collected using the Docker inspect command. Information, including the following, can be gathered using the inspect command:

IP address of an instance List of port bindings Search for specific port mapping Collect configuration details The syntax for inspect is

```text
docker inspect container/image
```

## kill:

The container’s main process is killed by sending the SIGKILL signal. The syntax is

```text
docker kill [options] container
```

## rmi:

Removing one or more images can be achieved using the rmi command. The syntax is as follows:

```text
docker rmi image
```

An image can have multiple tags linked to it. On deletion of an image, you should make sure all tags are removed to avoid errors. The following image shows an example usage of the rmi command.

## wait:

Docker provides a simple wait command to print the exit code only after the container exits.

## load:

Load an image or a repository in tar form to STDIN, which can be achieved using the command docker load.

This screenshot shows loading the app\_box.tar file to the STDIN.

## save:

Similar to load, an image can be saved as tar and sent to the STDOUT. This can be done using the save command

```text
docker save image
```

A simple illustration for this is:

