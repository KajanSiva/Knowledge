# Docker up and running

## Ch.4 : Working with Docker Images

### Building an image

Every Docker container is based on an image. They are the underlying definition of what gets reconstituted into a running container. Much like a virtual disk becomes a virtual machine when you start it up.

The Dockerfile describes all the steps that are required to create an image.

Example of a Dockerfile :
```
FROM node:0.10

LABEL "maintainer"="anna@example.com"
LABEL "rating"="Five Stars" "class"="First Class"

USER root

ENV AP /data/app
ENV SCPATH /etc/supervisor/conf.d

RUN apt-get -y update

# The daemons
RUN apt-get -y install supervisor
RUN mkdir -p /var/log/supervisor

# Supervisor Configuration
ADD ./supervisord/conf.d/* $SCPATH/

# Application Code
ADD *.js* $AP/

WORKDIR $AP

RUN npm install

CMD ["supervisord", "-n"]
```

Each line of a Dockerfile creates a new image layer that is stored by Docker.

We can add labels to images and containers to be able to search them easily. We can see the applied labels using the `docker inspect` command.

Instruction set :
- ENV : allows to set shell variables. To be used by the running application for configuration and during the build process.
- RUN : To start and create the required file structure
- ADD : to copy file from either the local filesystem or a remote URL into your image.
- WORKDIR : change the working directory in the image for remaining build instructions and the default process that launches with any resulting containers
- CMD : define the command that launches the process to run within the container.

The order of commands of the Dockerfile are very important.

.dockerignore file allows to define file and directories that you do not want to upload to the Docker host when building the image.

We can build a Docker image with the command :
```
docker build -t tag_name .
```

### Debugging build steps

Every container image is based on the image layers below it. One of the great benefits of that is that we can just run the lower layer as a container itself, using a shell to look around. Very useful when we want to debug a build step.

We can run commands into a container, like that :
```
docker run --rm -ti 8a773166616c /bin/bash
```

### Running the image

When the image is built, we can run it with the following command :
```
docker run -d -p 8080:8080 example/docker-node-hello:latest
```
This command will :
- Run the container in the background
- Use the image with the tag `example/docker-node-hello:latest`
- Map the port 8080 of the container with the port 8080 of the host

We can verify running containers with :
```
docker ps
```

### Storing images

TODO

### Advanced building techniques

Alpine linux is a lightweight version of Linux, useful to minimize the size of the images.

Layers are additive. We can't reduce the size of the image by simply deleting files that were generated in earlier steps.

That why it's important to order the Dockerfile so that the most stable and time-consuming portions of the build process happen first. And the code and dynamic content are added as late in the process as possible

Because those dynamic content will invalidate the cache.

Another technique is to use `&&` to aggregate multiple commands to avoid commiting unnecessary files to a new layer.

---

## Ch.5 : Working with Docker Containers

All the containers share a single kernel. And isolation between workloads is implemented entirely within that one kernel. This is called operatings system virtualization.

"A container is a self-contained execution environment that shares the kernel of the host system and which is (optionally) isolated from other containers in the system."

`docker run` is a convenience command which aggregates `docker create` and `docker start`.

### Basic configuration

#### Container name

By default a random name is given to the containers. We can give a specific one with the following :
```
docker create --name="awesome-service" ubuntu:latest sleep 120
```

#### Labels

A container inherits all the labels from his parent image.
But we can also pass new labels with the command :
```
docker run -d --name has-some-labels -l deployer=Ahmed -l tester=Asako ubuntu:latest sleep 1000
```

We can search and filter containers based on those metada using :
```
docker ps -a -f label=deployer=Ahmed
```

We can use `docker inspect` to see all the labels of a container.

#### Hostname

By default, Dockers copies certain file systems from the host, including `/etc/hostname`. And uses a bind mount to link that copy of the file into the container.
We can check that by executing `mount` on the container (by executing /bin/bash on it).

We can use a specific hostname with :
```
docker run --rm -ti --hostname="mycontainer.example.com" ubuntu:latest /bin/bash
```

#### Domain name service

We can change the default behavior concerning the DNS configuration with two parameters :
```
docker run --rm -ti --dns=8.8.8.8 --dns=8.8.4.4 --dns-search=example1.com --dns-search=example2.com ubuntu:latest /bin/bash
```

#### Mac address

We can specify a MAC address prefix using :
```
docker run --rm -ti --mac-address="a2:11:aa:22:bb:33" ubuntu:latest /bin/bash
```

#### Storage volume

Mounting storage from the Docker host is not generally advisable because it ties your container to a particular Docker host for its persistent state. But for cases like temporary cache files or other semi-ephemeral states, it can make sense.

We can mount directories or individual files using the following :
```
docker run --rm -ti -v /mnt/session_data:/data ubuntu:latest /bin/bash
```

By default, volumes are mounted read-write, to mount them read-only :
```
docker run --rm -ti -v /mnt/session_data:/data:ro ubuntu:latest /bin/bash
```

Neither the host mount point nor the mount point in the container needs to preexist for this command to work properly.

Containers should be designed to be stateless whenever possible. Managing storage creates undesirable dependencies and can easily make deployment scenarios much more complicated.

#### Resource quotas

We can define multiple kind of contrainsts with `docker run` or `docker create` :
- CPU shares
- CPU pinning
- Memory
- Block I/O
- ulimit

Constraints are normally applied at the time of container creation. If you need to change them, you can use the docker container update command or deploy a new container with the adjustments.

---


### Starting a container

We can list all the containers on the system, running or not with :
```
docker ps -a
```

And start the non-running ones with :
```
docker ps -a
```

#### Auto-restarting a container

We can specify auto restart settings with `--restart` arguments. Which accepts one of the four values :
- no
- always
- on-failure
- unless-stopped

#### Stopping a container

We can stop a container with :
```
docker stop 6b785f78b75e
```

As long as the container has not been deleted, we can restart it without needing to recreate it. Although memory contents will have been lost, all of the container’s filesystem contents and metadata, including environment variables and port bindings, are saved and will still be in place when we restart the container.

A normal docker stop sends a SIGTERM to the process. If we want to force a container to be killed if it hasn’t stopped after a certain amount of time, we can use the -t argument, like this:
```
docker stop -t 25 6b785f78b75e
```

---

#### Killing a container

When a container can't be stopped normally, we can kill it with :
```
docker kill 6b785f78b75e
```

Killed containers can be restarted with `docker start` just like after a `docker stop`.

Like the Linux `kill` command `docker kill` supports sending any Unix signals :
```
docker kill --signal=USR1 6b785f78b75e
```

---

#### Pausing and unpausing a container

We can pause and unpause a container with the following commands :

```
docker pause 6b785f78b75e
docker unpause 6b785f78b75e
```

Docker still considers the container to be running even when it is paused. But we can't use it.

---

#### Cleaning up Containers and Images

We can remove a non-running container with :
```
docker rm 92b797f12af1
```

We can list all the images with :
```
docker images
```

We can remove an image with :
```
docker rmi 873c28292d23
```

To purge all the images and containers from the system, we can use :
```
docker system prune
```
It's mostly useful on development mode.

To remove all unused images, instead of only dangling images :
```
docker system prune -a
```

To delete all of the containers on the Docker hosts, use the following command:
```
docker rm $(docker ps -a -q)
```

And to delete all the images on the Docker host, this command will get the job done:
```
docker rmi $(docker images -q)
```

The docker ps and docker images commands both support a filter argument that can make it easy to fine-tune the delete commands for certain circumstances.

---

