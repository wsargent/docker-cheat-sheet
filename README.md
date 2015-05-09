# Docker Cheat Sheet

NOTE: This used to be a gist that continually expanded.  It's now a github project because it's considerably easier for other people to edit, fix and expand on Docker using Github.  Just click  [README.md](https://github.com/wsargent/docker-cheat-sheet/blob/master/README.md), and then on the "writing pen" icon on the right to edit.

* [Why](#why)
* [Prerequisites](#prerequisites)
* [Installation](#installation)
* [Containers](#containers)
* [Images](#images)
* [Registry and Repository](#registry--repository)
* [Dockerfile](#dockerfile)
* [Layers](#layers)
* [Links](#links)
* [Volumes](#volumes)
* [Exposing Ports](#exposing-ports)
* [Machine, Swarm and Compose](#machines-swarm--and-compose)
* [Best Practices](#best-practices)
* [Tips](#tips)
* [Tools](#tools)

## Why

"With Docker, developers can build any app in any language using any toolchain. “Dockerized” apps are completely portable and can run anywhere - colleagues’ OS X and Windows laptops, QA servers running Ubuntu in the cloud, and production data center VMs running Red Hat.

Developers can get going quickly by starting with one of the 13,000+ apps available on Docker Hub. Docker manages and tracks changes and dependencies, making it easier for sysadmins to understand how the apps that developers build work. And with Docker Hub, developers can automate their build pipeline and share artifacts with collaborators through public or private repositories.

Docker helps developers build and ship higher-quality applications, faster." -- [What is Docker](https://www.docker.com/whatisdocker/#copy1)

## Prerequisites

I use [Oh My Zsh](https://github.com/robbyrussell/oh-my-zsh) with the [Docker plugin](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins#docker) for autocompletion of docker commands.  YMMV.

### Linux

The 3.10.x kernel is [the minimum requirement](http://docs.docker.com/installation/binaries/#check-kernel-dependencies) for Docker.

### MacOS

Use [Homebrew](http://brew.sh/).

```
ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
```

## Installation

[Installation instructions](https://docs.docker.com/installation/) are available.  Docker has recognized that installation / deployment is a PITA, and [Docker Machine](https://github.com/docker/machine) aka [bug 8681](https://github.com/docker/docker/issues/8681) deals with this specifically.

### Linux

Quick and easy install script provided by Docker:

```
curl -sSL https://get.docker.com/ | sh
```

If you're not willing to run a random shell script, please see the [installation](https://docs.docker.com/installation/) instructions for your distribution.

### Mac OS X

Download Docker for OSX from the [Github Releases](https://github.com/boot2docker/osx-installer/releases) page.

The canonical way to use Docker is with the aid of the boot2docker VM.  However, using the out of the box boot2docker doesn't give me control over my Vagrant instances (especially the lack of port forwarding).  So here's how to use boot2docker from a Vagrant instance.

We use the [YungSang modified boot2docker instance](https://github.com/YungSang/boot2docker-vagrant-box) from the [Vagrant Cloud](https://vagrantcloud.com/yungsang/boxes/boot2docker):

```
mkdir ~/boot2docker
cd ~/boot2docker
vagrant init yungsang/boot2docker
vagrant up
export DOCKER_HOST=tcp://localhost:2375
docker version
```

> NOTE: the YungSang boot2docker opens up port forwarding to the network, so is not safe on public wifi.  You can make a good argument that docker without TLS is [fundamentally unsafe](https://medium.com/@kevanahlquist/never-run-docker-on-a-tcp-socket-without-tls-1e7df31cf18c).  I only do it because I have [Hands Off](http://www.oneperiodic.com/products/handsoff/) installed to limit external network access.  

Then start up a container:

```
docker run -i -t ubuntu /bin/bash
```

That's it, you have a running Docker container. 

## Containers

[Your basic isolated Docker process](http://etherealmind.com/basics-docker-containers-hypervisors-coreos/).  Containers are to Virtual Machines as threads are to processes.  Or you can think of them as chroots on steroids.

### Lifecycle

* [`docker create`](https://docs.docker.com/reference/commandline/cli/#create) creates a container but does not start it.
* [`docker run`](https://docs.docker.com/reference/commandline/cli/#run) creates and starts a container in one operation.
* [`docker stop`](https://docs.docker.com/reference/commandline/cli/#stop) stops it.
* [`docker start`](https://docs.docker.com/reference/commandline/cli/#start) will start it again.
* [`docker restart`](https://docs.docker.com/reference/commandline/cli/#restart) restarts a container.
* [`docker rm`](https://docs.docker.com/reference/commandline/cli/#rm) deletes a container.
* [`docker kill`](https://docs.docker.com/reference/commandline/cli/#kill) sends a SIGKILL to a container.
* [`docker attach`](https://docs.docker.com/reference/commandline/cli/#attach) will connect to a running container.
* [`docker wait`](https://docs.docker.com/reference/commandline/cli/#wait) blocks until container stops.

If you want to run and then interact with a container, `docker start`, then spawn a shell as described in [Executing Commands](https://github.com/wsargent/docker-cheat-sheet/#executing-commands).

If you want a transient container, `docker run --rm` will remove the container after it stops.

If you want to remove also the volumes associated with the container, the deletion of the container must include the -v switch like in `docker --rm -v`.

If you want to poke around in an image, `docker run -t -i <myimage> <myshell>` to open a tty.

If you want to map a directory on the host to a docker container, `docker run -v $HOSTDIR:$DOCKERDIR`.  Also see [Volumes](https://github.com/wsargent/docker-cheat-sheet/#volumes).

If you want to integrate a container with a [host process manager](https://docs.docker.com/articles/host_integration/), start the daemon with `-r=false` then use `docker start -a`.

If you want to expose container ports through the host, see the [exposing ports](#exposing-ports) section.

Restart policies on crashed docker instances are [covered here](http://container42.com/2014/09/30/docker-restart-policies/).

### Info

* [`docker ps`](https://docs.docker.com/reference/commandline/cli/#ps) shows running containers.
* [`docker logs`](https://docs.docker.com/reference/commandline/cli/#logs) gets logs from container.
* [`docker inspect`](https://docs.docker.com/reference/commandline/cli/#inspect) looks at all the info on a container (including IP address).
* [`docker events`](https://docs.docker.com/reference/commandline/cli/#events) gets events from container.
* [`docker port`](https://docs.docker.com/reference/commandline/cli/#port) shows public facing port of container.
* [`docker top`](https://docs.docker.com/reference/commandline/cli/#top) shows running processes in container.
* [`docker stats`](https://docs.docker.com/reference/commandline/cli/#stats) shows containers' resource usage statistics.
* [`docker diff`](https://docs.docker.com/reference/commandline/cli/#diff) shows changed files in the container's FS.

`docker ps -a` shows running and stopped containers.

### Import / Export

There doesn't seem to be a way to use docker directly to import files into a container's filesystem.  The closest thing is to mount a host file or directory as a data volume and copy it from inside the container.

* [`docker cp`](http://docs.docker.com/reference/commandline/cli/#cp) copies files or folders out of a container's filesystem.
* [`docker export`](https://docs.docker.com/reference/commandline/cli/#export) turns container filesystem into tarball archive stream to STDOUT.

### Executing Commands

* [`docker exec`](https://docs.docker.com/reference/commandline/cli/#exec) to execute a command in container.

To enter a running container, attach a new shell process to a running container called foo, use: `docker exec -it foo /bin/bash`.

## Images

Images are just [templates for docker containers](https://docs.docker.com/introduction/understanding-docker/#how-does-a-docker-image-work).

### Lifecycle

* [`docker images`](https://docs.docker.com/reference/commandline/cli/#images) shows all images.
* [`docker import`](https://docs.docker.com/reference/commandline/cli/#import) creates an image from a tarball.
* [`docker build`](https://docs.docker.com/reference/commandline/cli/#build) creates image from Dockerfile.
* [`docker commit`](https://docs.docker.com/reference/commandline/cli/#commit) creates image from a container.
* [`docker rmi`](https://docs.docker.com/reference/commandline/cli/#rmi) removes an image.
* [`docker insert`](https://docs.docker.com/reference/commandline/cli/#insert) inserts a file from URL into image. (kind of odd, you'd think images would be immutable after create)
* [`docker load`](https://docs.docker.com/reference/commandline/cli/#load) loads an image from a tar archive as STDIN, including images and tags (as of 0.7).
* [`docker save`](https://docs.docker.com/reference/commandline/cli/#save) saves an image to a tar archive stream to STDOUT with all parent layers, tags & versions (as of 0.7).

### Info

* [`docker history`](https://docs.docker.com/reference/commandline/cli/#history) shows history of image.
* [`docker tag`](https://docs.docker.com/reference/commandline/cli/#tag) tags an image to a name (local or registry).

Docker image ids are [sensitive information](https://medium.com/@quayio/your-docker-image-ids-are-secrets-and-its-time-you-treated-them-that-way-f55e9f14c1a4) and should not be exposed to the outside world.  Treat them like passwords.

## Registry & Repository

A repository is a *hosted* collection of tagged images that together create the file system for a container.

A registry is a *host* -- a server that stores repositories and provides an HTTP API for [managing the uploading and downloading of repositories](https://docs.docker.com/userguide/dockerrepos/).

Docker.com hosts its own [index](https://registry.hub.docker.com/) to a central registry which contains a large number of repositories.  Having said that, the central docker registry [does not do a good job of verifying images](https://titanous.com/posts/docker-insecurity) and should be avoided if you're worried about security.

* [`docker login`](https://docs.docker.com/reference/commandline/cli/#login) to login to a registry.
* [`docker search`](https://docs.docker.com/reference/commandline/cli/#search) searches registry for image.
* [`docker pull`](https://docs.docker.com/reference/commandline/cli/#pull) pulls an image from registry to local machine.
* [`docker push`](https://docs.docker.com/reference/commandline/cli/#push) pushes an image to the registry from local machine.

### Run local registry

[Registry implementation](http://github.com/docker/docker-registry) has an official image for basic setup that can be launched with
[`docker run -p 5000:5000 registry`](https://github.com/docker/docker-registry#quick-start)
Note that this installation does not have any authorization controls. You may use option `-P -p 127.0.0.1:5000:5000` to limit connections to localhost only.
In order to push to this repository tag image with `repositoryHostName:5000/imageName` then push this tag.

## Dockerfile

[The configuration file](https://docs.docker.com/reference/builder/). Sets up a Docker container when you run `docker build` on it.  Vastly preferable to `docker commit`.  If you use [jEdit](http://jedit.org), I've put up a syntax highlighting module for [Dockerfile](https://github.com/wsargent/jedit-docker-mode) you can use.  You may also like to try the [tools section](#tools).

### Instructions

* [.dockerignore](https://docs.docker.com/reference/builder/#the-dockerignore-file)
* [FROM](https://docs.docker.com/reference/builder/#from)
* [MAINTAINER](https://docs.docker.com/reference/builder/#maintainer)
* [RUN](https://docs.docker.com/reference/builder/#run)
* [CMD](https://docs.docker.com/reference/builder/#cmd)
* [EXPOSE](https://docs.docker.com/reference/builder/#expose)
* [ENV](https://docs.docker.com/reference/builder/#env)
* [ADD](https://docs.docker.com/reference/builder/#add)
* [COPY](https://docs.docker.com/reference/builder/#copy)
* [ENTRYPOINT](https://docs.docker.com/reference/builder/#entrypoint)
* [VOLUME](https://docs.docker.com/reference/builder/#volume)
* [USER](https://docs.docker.com/reference/builder/#user)
* [WORKDIR](https://docs.docker.com/reference/builder/#workdir)
* [ONBUILD](https://docs.docker.com/reference/builder/#onbuild)

### Tutorial

* [Flux7's Dockerfile Tutorial](http://flux7.com/blogs/docker/docker-tutorial-series-part-3-automation-is-the-word-using-dockerfile/)

### Examples

* [Examples](https://docs.docker.com/reference/builder/#dockerfile-examples)
* [Best practices for writing Dockerfiles](https://docs.docker.com/articles/dockerfile_best-practices/)
* [Michael Crosby](http://crosbymichael.com/) has some more [Dockerfiles best practices](http://crosbymichael.com/dockerfile-best-practices.html) / [take 2](http://crosbymichael.com/dockerfile-best-practices-take-2.html).

## Best Practices

This is where general Docker best practices and war stories go:

* [The Rabbit Hole of Using Docker in Automated Tests](http://gregoryszorc.com/blog/2014/10/16/the-rabbit-hole-of-using-docker-in-automated-tests/)
* [Bridget Kromhout](https://twitter.com/bridgetkromhout) has a useful blog post on [running Docker in production](http://sysadvent.blogspot.co.uk/2014/12/day-1-docker-in-production-reality-not.html) at Dramafever.  
* There's also a best practices [blog post](http://developers.lyst.com/devops/2014/12/08/docker/) from Lyst.
* [A Docker Dev Environment in 24 Hours!](http://blog.relateiq.com/a-docker-dev-environment-in-24-hours-part-2-of-2/)
* [Building a Development Environment With Docker](http://tersesystems.com/2013/11/20/building-a-development-environment-with-docker/)
* [Discourse in a Docker Container](http://samsaffron.com/archive/2013/11/07/discourse-in-a-docker-container)

## Layers

The versioned filesystem in Docker is based on layers.  They're like [git commits or changesets for filesystems](https://docs.docker.com/terms/layer/).

Note that if you're using [aufs](http://en.wikipedia.org/wiki/Aufs) as your filesystem, Docker does not always remove data volumes containers layers when you delete a container!  See [PR 8484](https://github.com/docker/docker/pull/8484) for more details.

## Links

Links are how Docker containers talk to each other [through TCP/IP ports](https://docs.docker.com/userguide/dockerlinks/).  [Linking into Redis](https://docs.docker.com/examples/running_redis_service/) and [Atlassian](http://blogs.atlassian.com/2013/11/docker-all-the-things-at-atlassian-automation-and-wiring/) show worked examples.  You can also (in 0.11) resolve [links by hostname](https://docs.docker.com/userguide/dockerlinks/#updating-the-etchosts-file).

NOTE: If you want containers to ONLY communicate with each other through links, start the docker daemon with `-icc=false` to disable inter process communication.

If you have a container with the name CONTAINER (specified by `docker run --name CONTAINER`) and in the Dockerfile, it has an exposed port:

```
EXPOSE 1337
```

Then if we create another container called LINKED like so:

```
docker run -d --link CONTAINER:ALIAS --name LINKED user/wordpress
```

Then the exposed ports and aliases of CONTAINER will show up in LINKED with the following environment variables:

```
$ALIAS_PORT_1337_TCP_PORT
$ALIAS_PORT_1337_TCP_ADDR
```

And you can connect to it that way.

To delete links, use `docker rm --link `.

If you want to link across docker hosts then you should look at [Swarm](http://docs.docker.com/swarm/). This [link on stackoverflow](http://stackoverflow.com/questions/21283517/how-to-link-docker-services-across-hosts) provides some good information on different patterns for linking containers across docker hosts.

## Volumes

Docker volumes are [free-floating filesystems](http://docs.docker.com/userguide/dockervolumes/).  They don't have to be connected to a particular container.  You should use volumes mounted from [data-only containers](https://medium.com/@ramangupta/why-docker-data-containers-are-good-589b3c6c749e) for portability.

Volumes are useful in situations where you can't use links (which are TCP/IP only).  For instance, if you need to have two docker instances communicate by leaving stuff on the filesystem.

You can mount them in several docker containers at once, using `docker run -volume-from`.

Because volumes are isolated filesystems, they are often used to store state from computations between transient containers.  That is, you can have a stateless and transient container run from a recipe, blow it away, and then have a second instance of the transient container pick up from where the last one left off.

See [advanced volumes](http://crosbymichael.com/advanced-docker-volumes.html) for more details.  Container42 is [also helpful](http://container42.com/2014/11/03/docker-indepth-volumes/).

For an easy way to clean abandoned volumes, see [docker-cleanup-volumes](https://github.com/chadoe/docker-cleanup-volumes)

As of 1.3, you can [map MacOS host directories as docker volumes](http://docs.docker.com/userguide/dockervolumes/#mount-a-host-directory-as-a-data-volume) through boot2docker:

```
docker run -v /Users/wsargent/myapp/src:/src
```

You can also use remote NFS volumes if you're [feeling brave](http://www.tech-d.net/2014/03/29/docker-quicktip-4-remote-volumes/).

You may also consider running data-only containers as described [here](http://container42.com/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/) to provide some data portability.

## Exposing ports

Exposing incoming ports through the host container is [fiddly but doable](https://docs.docker.com/reference/run/#expose-incoming-ports).


The fastest way is to map the container port to the host port (only using localhost interface) using `-p`:

```
docker run -p 127.0.0.1:$HOSTPORT:$CONTAINERPORT --name CONTAINER -t someimage
```

If you don't want to use the `-p` option on the command line, you can persist port forwarding by using [EXPOSE](https://docs.docker.com/reference/builder/#expose):

```
EXPOSE <CONTAINERPORT>
```

If you're running Docker in Virtualbox, you then need to forward the port there as well, using [forwarded_port](https://docs.vagrantup.com/v2/networking/forwarded_ports.html).  It can be useful to define something in Vagrantfile to expose a range of ports so that you can dynamically map them:

```
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  ...

  (49000..49900).each do |port|
    config.vm.network :forwarded_port, :host => port, :guest => port
  end

  ...
end
```

If you forget what you mapped the port to on the host container, use `docker port` to show it:

```
docker port CONTAINER $CONTAINERPORT
```

## Machines, Swarm  and Compose
In early 2015 Docker developed and open sourced a number of components that use the Docker API to provide some higher level services.

* [Machine](https://docs.docker.com/machine/), is a tool for easily creating a docker host either locally or on cloud provider (i.e. AWS, Digital Ocean etc).
* [Swarm](https://docs.docker.com/swarm/), is clustering for docker. It provides a way to group a number of docker hosts into a single virtual entity and provides mechanisms to schedule containers across these hosts.
* [Compose](https://docs.docker.com/compose/), provides a tool for describing a multi-container application within a single file. Compose primarily Fig renamed.

The [Demo of the Machine + Swarm + Compose integration](https://www.youtube.com/watch?v=M4PFY6RZQHQ) video provides a good introduction to the various components and how they work together.



## Tips

Sources:

* [15 Docker Tips in 5 minutes](http://sssslide.com/speakerdeck.com/bmorearty/15-docker-tips-in-5-minutes)

### Last Ids

```
alias dl='docker ps -l -q'
docker run ubuntu echo hello world
docker commit `dl` helloworld
```

### Commit with command (needs Dockerfile)

```
docker commit -run='{"Cmd":["postgres", "-too -many -opts"]}' `dl` postgres
```

### Get IP address

```
docker inspect `dl` | grep IPAddress | cut -d '"' -f 4
```

or

```
wget http://stedolan.github.io/jq/download/source/jq-1.3.tar.gz
tar xzvf jq-1.3.tar.gz
cd jq-1.3
./configure && make && sudo make install
docker inspect `dl` | jq -r '.[0].NetworkSettings.IPAddress'
```

or using a [go template](https://docs.docker.com/reference/commandline/cli/#inspect)

```
docker inspect -f '{{ .NetworkSettings.IPAddress }}' <container_name>
```

### Get port mapping

```
docker inspect -f '{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' <containername>
```

### Find containers by regular expression

```
for i in $(docker ps -a | grep "REGEXP_PATTERN" | cut -f1 -d" "); do echo $i; done`
```

### Get Environment Settings

```
docker run --rm ubuntu env
```

### Kill running containers

```
docker kill $(docker ps -q)
```

### Delete old containers

```
docker ps -a | grep 'weeks ago' | awk '{print $1}' | xargs docker rm
```

### Delete stopped containers

```
docker rm -v `docker ps -a -q -f status=exited`
```

### Delete dangling images

```
docker rmi $(docker images -q -f dangling=true)
```

### Delete all images

```
docker rmi $(docker images -q)
```

### Show image dependencies

```
docker images -viz | dot -Tpng -o docker.png
```

### Slimming down Docker containers  [Intercity Blog](http://bit.ly/1DycW5A)

- Cleaning APT
```
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
```
- Flatten an image
```
ID=$(docker run -d image-name /bin/bash)
docker export $ID | docker import – flat-image-name
```

- For backup
```
ID=$(docker run -d image-name /bin/bash)
(docker export $ID | gzip -c > image.tgz)
gzip -dc image.tgz | docker import - flat-image-name
```


## Tools

* [Compose](#compose)
* [Panamax](#panamax)
* [Vessel](#vessel)

### Compose

[Compose](https://github.com/docker/compose) (originally named fig) is a helper app that makes it easier to run multiple docker containers on the same host.
I would expect it to be used during dev/qa more than in production.

Compose works with a YAML file (default name can be ```docker-compose.yml```, use ```-f``` to provide a different filename)  that defines the containers you wish to use with it.
Compose will take its project name from the name of the folder containing your yml configuration but you can override that with the ```-p``` parameter.

Once I have my config defined, I can use ```docker-compose up -d``` to run it (the ```-d``` runs it as a detached task). This will start and link any containers.

You can do everything you do with compose using docker directly but running multiple containers with parameters
would require some sort of script if you plan to do it more than once so the yml config of compose
and the convenience commands it provides are worth considering.

Here's an example of setting up a ```docker-compose.yml``` for an app with an apache packaged client container and a tomcat packaged app war:

First, here are the two docker commands to run these containers:

```
docker run -p 8080:8080 -v /Users/me/tomcatwork/trial.properties:/usr/share/tomcat6/trial.properties:rw -d me/tcfull
docker run -p 80:80 -v /Users/me/dockerwork/localproxy.conf:/etc/apache2/conf-enabled/proxy.conf:rw -d me/afull
```

At this point, I haven't linked the containers - I'm using the proxy.conf to specify the tomcat address.
my docker-compose.yml looks like this:

```
app:
  image: me/tcfull
  ports:
    - "8080"
  volumes:
    - /Users/me/tomcatwork/trial.properties:/usr/share/tomcat6/trial.properties:rw
web:
  image: me/afull
  ports:
    - "80:80"
  volumes:
    - /Users/me/figwork/proxy.conf:/etc/apache2/conf-enabled/proxy.conf:rw
  links:
    - app
```

As you can see it follows the docker commands with the addition of names for the containers and a links section for the web container, linking it to the app container.

As part of that linking process, docker will copy any environment variables defined in the app container over to the web container, define new environment variables for the address the app container is running at and also add an app entry in the etc/hosts file for the web container. I can modify my proxy conf to address ```http://app:8080``` and fig/docker will take care of the rest.

I can then use commands like ```docker-compose stop``` and ```docker-compose rm``` to stop all my containers and remove them.
NB - docker will [eventually](https://gist.github.com/aanand/9e7ac7185ffd64c1a91a) absorb figs functionality with docker groups and docker up but it looks like they're keeping the yml config so it should be pretty seamless when it happens.


#### Troubleshooting

```docker-compose run``` is a useful command for debugging issues. It allows me to startup a named container (and any it links to) and run a one off command. 

This allows me to do things like ```docker-compose run web env``` which will give me a list of all the environment variables that are available on the web container including the ones generated via the link to app. 

I can also use ```docker-compose run web bash``` to run my web container interactively the way it has been setup by compose with the link to app so I can debug any issues from the command line.


### Panamax

* [Panamax](http://panamax.io/)

Nice web UI, will let you set up and download multiple docker containers.


### Vessel

* [Vessel](http://awvessel.github.io/)

Vessel automates the setup & use of dockerized development environments.
