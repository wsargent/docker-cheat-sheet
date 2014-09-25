# Docker Cheat Sheet

NOTE: This used to be a gist that continually expanded.  It's now a github project because it's considerably easier for other people to edit, fix and expand on Docker using Github.  Just click  [README.md](https://github.com/wsargent/docker-cheat-sheet/blob/master/README.md), and then on the "writing pen" icon on the right to edit.

* [Why](https://github.com/wsargent/docker-cheat-sheet#why)
* [I just want a dev environment](https://github.com/wsargent/docker-cheat-sheet#tldr-i-just-want-a-dev-environment)
* [Prerequisites](https://github.com/wsargent/docker-cheat-sheet#prequisites)
* [Installation](https://github.com/wsargent/docker-cheat-sheet#installation)
* [Containers](https://github.com/wsargent/docker-cheat-sheet#containers)
* [Images](https://github.com/wsargent/docker-cheat-sheet#images)
* [Registry and Repository](https://github.com/wsargent/docker-cheat-sheet#registry--repository)
* [Dockerfile](https://github.com/wsargent/docker-cheat-sheet#dockerfile)
* [Layers](https://github.com/wsargent/docker-cheat-sheet#layers)
* [Links](https://github.com/wsargent/docker-cheat-sheet#links)
* [Volumes](https://github.com/wsargent/docker-cheat-sheet#volumes)
* [Exposing Ports](https://github.com/wsargent/docker-cheat-sheet#exposing-ports)
* [Tips](https://github.com/wsargent/docker-cheat-sheet#tips)

## Why

[Why Should I Care (For Developers)](https://www.docker.io/the_whole_story/#Why-Should-I-Care-\(For-Developers\))

> "Docker interests me because it allows simple environment isolation and repeatability. I can create a run-time environment once, package it up, then run it again on any other machine. Furthermore, everything that runs in that environment is isolated from the underlying host (much like a virtual machine). And best of all, everything is fast and simple."

## TL;DR, I just want a dev environment

* [A Docker Dev Environment in 24 Hours!](http://blog.relateiq.com/a-docker-dev-environment-in-24-hours-part-2-of-2/)
* [Building a Development Environment With Docker](http://tersesystems.com/2013/11/20/building-a-development-environment-with-docker/)
* [Discourse in a Docker Container](http://samsaffron.com/archive/2013/11/07/discourse-in-a-docker-container)

You may also like to try the following tools (and add more details here after you try them):

* [Fig](http://www.fig.sh/)
* [Panamax](http://panamax.io/)
* [Vessel](http://awvessel.github.io/)
* [Shipyard](http://shipyard-project.com/)

## Prequisites

Use [Homebrew](http://brew.sh/).

```
ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
```

## Installation

This is all MacOS specific.

Install VirtualBox and Vagrant using [Brew Cask](https://github.com/phinze/homebrew-cask).

```
brew tap caskroom/homebrew-cask
brew install brew-cask
brew cask install virtualbox
brew cask install vagrant
```

I personally don't use boot2docker because I already know how to use Vagrant, and I don't like how boot2docker doesn't give me control over my Vagrant instances (especially the lack of port forwarding).  So this is the real way to do it.

We use the [Open Vagrant files](https://github.com/phusion/open-vagrant-boxes) defined by Phusion, which have better default settings:

```
vagrant init phusion/ubuntu-14.04-amd64
vagrant up
vagrant ssh
```

Once you're in the Vagrant instance, install Docker like any other package:

```
sudo apt-get update
sudo apt-get install -qy software-properties-common # needed for add-apt-repository etc
sudo apt-get install -qy docker.io
sudo ln -sf /usr/bin/docker.io /usr/local/bin/docker
```

Then start up a container:

```
sudo docker run -i -t ubuntu /bin/bash
```

That's it, you have a running Docker container.  Also note that Vagrant 1.6 has Docker [supported as a built-in provisioner](https://docs.vagrantup.com/v2/docker/index.html) which can help you when configuring images.

I use [Oh My Zsh](https://github.com/robbyrussell/oh-my-zsh) with the [Docker plugin](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins#docker) for autocompletion of docker commands.  YMMV.

## Containers

[Your basic isolated Docker process](http://docker.readthedocs.org/terms/container/#container-def).  Containers are to Virtual Machines as threads are to processes.  Or you can think of them as chroots on steroids.

Some common misconceptions it's worth correcting:

* __Containers are not transient__.  `docker run` doesn't do what you think.
* __Containers are not limited to running a single command or process.__  You can use [supervisord](http://docs.docker.io/examples/using_supervisord/) or [runit](https://github.com/phusion/baseimage-docker).

### Lifecycle

* [`docker run`](http://docs.docker.io/reference/commandline/cli/#run) creates a container.
* [`docker stop`](http://docs.docker.io/reference/commandline/cli/#stop) stops it.
* [`docker start`](http://docs.docker.io/reference/commandline/cli/#start) will start it again.
* [`docker restart`](http://docs.docker.io/reference/commandline/cli/#restart) restarts a container.
* [`docker rm`](http://docs.docker.io/reference/commandline/cli/#rm) deletes a container.
* [`docker kill`](http://docs.docker.io/reference/commandline/cli/#kill) sends a SIGKILL to a container.  [Has issues](https://github.com/dotcloud/docker/issues/197).
* [`docker attach`](http://docs.docker.io/reference/commandline/cli/#attach) will connect to a running container.
* [`docker wait`](http://docs.docker.io/reference/commandline/cli/#wait) blocks until container stops.

If you want to run and then interact with a container, `docker start` then `docker attach` to get in (or, as of 0.9, `nsenter`).

If you want a transient container, `docker run --rm` will remove the container after it stops.

If you want to poke around in an image, `docker run -t -i <myimage> <myshell>` to open a tty.

If you want to map a directory on the host to a docker container, `docker run -v $HOSTDIR:$DOCKERDIR` (also see Volumes section).

If you want to integrate a container with a [host process manager](http://docs.docker.io/use/host_integration/), start the daemon with `-r=false` then use `docker start -a`.

If you want to expose container ports through the host, see the [exposing ports](https://github.com/wsargent/docker-cheat-sheet#exposing-ports) section.

### Info

* [`docker ps`](http://docs.docker.io/reference/commandline/cli/#ps) shows running containers.
* [`docker inspect`](http://docs.docker.io/reference/commandline/cli/#inspect) looks at all the info on a container (including IP address).
* [`docker logs`](http://docs.docker.io/reference/commandline/cli/#logs) gets logs from container.
* [`docker events`](http://docs.docker.io/reference/commandline/cli/#events) gets events from container.
* [`docker port`](http://docs.docker.io/reference/commandline/cli/#port) shows public facing port of container.
* [`docker top`](http://docs.docker.io/reference/commandline/cli/#top) shows running processes in container.
* [`docker diff`](http://docs.docker.io/reference/commandline/cli/#diff) shows changed files in the container's FS.

`docker ps -a` shows running and stopped containers.

### Import / Export

There doesn't seem to be a way to use docker directly to import files into a container's filesystem.  The closest thing is to mount a host file or directory as a data volume and copy it from inside the container.

* [`docker cp`](http://docs.docker.io/reference/commandline/cli/#cp) copies files or folders out of a container's filesystem.
* [`docker export`](http://docs.docker.io/reference/commandline/cli/#export) turns container filesystem into tarball.

### Entering a Docker Container

The "official" way to enter a docker container while it's running is to use `nsenter`, which uses [libcontainer under the hood](http://jpetazzo.github.io/2014/03/23/lxc-attach-nsinit-nsenter-docker-0-9/).  Using an `sshd` daemon is [considered evil](http://jpetazzo.github.io/2014/06/23/docker-ssh-considered-evil/).  

Unfortunately, nsenter requires some configuration and installation. If your operating system does not include nsenter (usually in a package named util-linux or similar, although it has to be quite a recent version), the easiest way is probably to install it through docker, as described in the first of the following links:

* [Installing nsenter using docker](https://github.com/jpetazzo/nsenter)
* [How to enter a Docker container](https://blog.codecentric.de/en/2014/07/enter-docker-container/)
* [Docker debug with nsenter on boot2docker](http://blog.sequenceiq.com/blog/2014/07/05/docker-debug-with-nsenter-on-boot2docker/)

`nsenter` allows you to run any command (e.g. a shell) inside a container that's already running another command (e.g. your database or webserver). This allows you to see all mounted volumes, check on processes, log files etc. inside a running container.

The first installation method described above also installs a small wrapper script wrapping `nsenter` named `docker-enter` that makes executing a shell inside a running container as easy as `docker-enter CONTAINER` and any other command via `docker-enter CONTAINER COMMAND`.

## Images

Images are just [templates for docker containers](http://docker.readthedocs.org/reference/terms/image/).

### Lifecycle

* [`docker images`](http://docs.docker.io/reference/commandline/cli/#images) shows all images.
* [`docker import`](http://docs.docker.io/reference/commandline/cli/#import) creates an image from a tarball.
* [`docker build`](http://docs.docker.io/reference/commandline/cli/#build) creates image from Dockerfile.
* [`docker commit`](http://docs.docker.io/reference/commandline/cli/#commit) creates image from a container.
* [`docker rmi`](http://docs.docker.io/reference/commandline/cli/#rmi) removes an image.
* [`docker insert`](http://docs.docker.io/reference/commandline/cli/#insert) inserts a file from URL into image. (kind of odd, you'd think images would be immutable after create)
* [`docker load`](http://docs.docker.io/reference/commandline/cli/#load) loads an image from a tar archive as STDIN, including images and tags (as of 0.7).
* [`docker save`](http://docs.docker.io/reference/commandline/cli/#save) saves an image to a tar archive stream to STDOUT with all parent layers, tags & versions (as of 0.7).

`docker import` and `docker commit` only set up the filesystem, not Dockerfile info like CMD or ENTRYPOINT or EXPOSE.  See [bug](https://github.com/dotcloud/docker/issues/1141).

### Info

* [`docker history`](http://docs.docker.io/reference/commandline/cli/#history) shows history of image.
* [`docker tag`](http://docs.docker.io/reference/commandline/cli/#tag) tags an image to a name (local or registry).

## Registry & Repository

A repository is a *hosted* collection of tagged images that together create the file system for a container.

A registry is a *host* -- a server that stores repositories and provides an HTTP API for [managing the uploading and downloading of repositories](http://docs.docker.io/use/workingwithrepository/).

Docker.io hosts its own [index](https://index.docker.io/) to a central registry which contains a large number of repositories.

* [`docker login`](http://docs.docker.io/reference/commandline/cli/#login) to login to a registry.
* [`docker search`](http://docs.docker.io/reference/commandline/cli/#search) searches registry for image.
* [`docker pull`](http://docs.docker.io/reference/commandline/cli/#pull) pulls an image from registry to local machine.
* [`docker push`](http://docs.docker.io/reference/commandline/cli/#push) pushes an image to the registry from local machine.

## Dockerfile

[The configuration file](http://docs.docker.io/introduction/working-with-docker/#working-with-the-dockerfile). Sets up a Docker container when you run `docker build` on it.  Vastly preferable to `docker commit`.

### Instructions

* [FROM](http://docs.docker.io/reference/builder/#from)
* [MAINTAINER](http://docs.docker.io/reference/builder/#maintainer)
* [RUN](http://docs.docker.io/reference/builder/#run)
* [CMD](http://docs.docker.io/reference/builder/#cmd)
* [EXPOSE](http://docs.docker.io/reference/builder/#expose)
* [ENV](http://docs.docker.io/reference/builder/#env)
* [ADD](http://docs.docker.io/reference/builder/#add)
* [ENTRYPOINT](http://docs.docker.io/reference/builder/#entrypoint)
* [VOLUME](http://docs.docker.io/reference/builder/#volume)
* [USER](http://docs.docker.io/reference/builder/#user)
* [WORKDIR](http://docs.docker.io/reference/builder/#workdir)
* [ONBUILD](http://docs.docker.io/reference/builder/#onbuild)

### Tutorial

* [Flux7's Dockerfile Tutorial](http://flux7.com/blogs/docker/docker-tutorial-series-part-3-automation-is-the-word-using-dockerfile/)

### Examples

* [Examples](http://docs.docker.io/reference/builder/#dockerfile-examples)

### Best Practices

Best to look at [http://github.com/wsargent/docker-devenv](http://github.com/wsargent/docker-devenv) and the [best practices](http://crosbymichael.com/dockerfile-best-practices.html) / [take 2](http://crosbymichael.com/dockerfile-best-practices-take-2.html) for more details.

If you use [jEdit](http://jedit.org), I've put up a syntax highlighting module for [Dockerfile](https://github.com/wsargent/jedit-docker-mode) you can use.

## Layers

The [versioned filesystem](http://en.wikipedia.org/wiki/Aufs) in Docker is based on layers.  They're like [git commits or changesets for filesystems](http://docker.readthedocs.org/reference/terms/layer/).

## Links

Links are how Docker containers talk to each other [through TCP/IP ports](http://docs.docker.io/use/working_with_links_names/).  [Linking into Redis](http://docs.docker.io/use/working_with_links_names/#links-service-discovery-for-docker) and [Atlassian](http://blogs.atlassian.com/2013/11/docker-all-the-things-at-atlassian-automation-and-wiring/) show worked examples.  You can also (in 0.11) resolve [links by hostname](http://docs.docker.io/use/working_with_links_names/#resolving-links-by-name).

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

## Volumes

Docker volumes are [free-floating filesystems](http://docs.docker.com/userguide/dockervolumes/).  They don't have to be connected to a particular container.

Volumes are useful in situations where you can't use links (which are TCP/IP only).  For instance, if you need to have two docker instances communicate by leaving stuff on the filesystem.

You can mount them in several docker containers at once, using `docker run -volume-from`

Because volumes are isolated filesystems, they are often used to store state from computations between transient containers.  That is, you can have a stateless and transient container run from a recipe, blow it away, and then have a second instance of the transient container pick up from where the last one left off.

See [advanced volumes](http://crosbymichael.com/advanced-docker-volumes.html) for more details.

## Exposing ports

Exposing ports through the host container is [fiddly but doable](http://docs.docker.io/use/port_redirection/#binding-a-port-to-an-host-interface).

First expose the port in your Dockerfile:

```
EXPOSE <CONTAINERPORT>
```

Then map the container port to the host port (only using localhost interface):

```
docker run -p 127.0.0.1:$HOSTPORT:$CONTAINERPORT --name CONTAINER -t someimage
```

If you're running Docker in Virtualbox, you then need to forward the port there as well.  It can be useful to define something in Vagrantfile to expose a range of ports so that you can dynamically map them:

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

or (this is unverified)

```
docker inspect -f '{{ .NetworkSettings.IPAddress }}' <container_name>
```

### Get port mapping

```
docker inspect -f '{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' <containername>
```

### Get Environment Settings

```
docker run --rm ubuntu env
```

### Delete old containers

```
docker ps -a | grep 'weeks ago' | awk '{print $1}' | xargs docker rm
```

### Delete stopped containers

```
docker rm `docker ps -a -q`
```

### Show image dependencies

```
docker images -viz | dot -Tpng -o docker.png
```
