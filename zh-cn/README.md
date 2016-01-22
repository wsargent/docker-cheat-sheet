# Docker Cheat Sheet

注意: 本文由 gist 不断扩展而来。现在它已经成为了一个 GitHub 工程，这样便于其他 Github 用户编辑，修改以及在 Docker 上扩展它。只需要点击 [README.md](https://github.com/wsargent/docker-cheat-sheet/blob/master/README.md)，然后点击右上角的 "writing pen" 图标便能开始编辑了。

* [为何使用 Docker](#why)
* [系统环境](#prerequisites)
* [安装](#installation)
* [容器(Containers)](#containers)
* [镜像(Images)](#images)
* [网络(Networks)](#networks)
* [仓管中心和仓库(Registry & Repository)](#registry--repository)
* [Dockerfile](#dockerfile)
* [层(Layers)](#layers)
* [链接(Links)](#links)
* [卷标(Volumes)](#volumes)
* [暴露端口(Exposing Ports)](#exposing-ports)
* [最佳实践](#best-practices)
* [安全](#security)
* [小贴士](#tips)

## 为何使用 Docker

"通过 Docker, 开发者可以使用任何语言任何工具创建任何应用。“Dockerized” 的应用是完全可移植的，能在任何地方运行 - 不管是同事的 OS X 和 Windows 笔记本，或是在云端运行的 Ubuntu QA 服务，还是在虚拟机运行的 Red Hat 产品数据中心。

 Docker Hub 上有 13,000+ 应用雏形，开发者可以从中选取一个进行快速扩展开发。Docker 跟踪管理变更和依赖关系，让系统管理员能更容易理解开发人员是如何让应用运转起来的。而开发者可以通过 Docker Hub 的共有/私有仓库，构建他们的自动化编译，与其他合作者共享成果。

Docker 帮助开发者构建和发布高质量的应用，更快的。" -- [什么是 Docker](https://www.docker.com/whatisdocker/#copy1)

## 系统环境

我用的是 [Oh My Zsh](https://github.com/robbyrussell/oh-my-zsh) ，和 [Docker 插件](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins#docker) ，它可以自动补全 docker 的命令。YMMV。

### Linux

3.10.x 内核是能运行 Docker 的[最低要求](https://docs.docker.com/installation/binaries/#check-kernel-dependencies)。

### MacOS

10.8 “Mountain Lion” 或者更新的版本。

## 安装

### Linux

Docker 提供了快速安装脚本：

```
curl -sSL https://get.docker.com/ | sh
```

如果你不想执行一个不明不白的 shell 脚本，那么请看[安装教程](https://docs.docker.com/installation/)，选择你在用的发行版本。  

如果你是一个 Docker 超新手，那么我建议你先去看看[系列教程](https://docs.docker.com/linux/started/)。

### Mac OS X

下载和安装 [Docker Toolbox](https://www.docker.com/toolbox)。如果它不工作，那么看看[安装教程](https://docs.docker.com/installation/mac/)。

Docker 原来用的是 boot2docker，不过现在已经改成 docker machine 了。Docker 网站有说明[如何升级](https://docs.docker.com/installation/mac/#migrate-from-boot2docker)。如果你有一个 docker 实例的话，你还是可以直接安装[Docker Machine](https://docs.docker.com/machine/install-machine/)的。

安装好 Docker Toolbox 之后，通过 VirtualBox provider 安装带 Docker Machine 的 VM:

```
docker-machine create --driver=virtualbox default
docker-machine ls
eval "$(docker-machine env default)"
```

然后启动 container:

```
docker run hello-world
```

好了，你现在有了一个运行中的 Docker container 了。

如果你是一个 Docker 超新手，那么我建议你先去看看[系列教程](https://docs.docker.com/mac/started/)。

## 容器(Container)

[最基本的 Docker 进程](http://etherealmind.com/basics-docker-containers-hypervisors-coreos/)。容器(Container)之于虚拟机(Virtual Machine)就好比线程之于进程。或者你可以把他们想成是 chroots on steroids。

### 生命周期

* [`docker create`](https://docs.docker.com/reference/commandline/create) 创建一个容器但是不启动。
* [`docker run`](https://docs.docker.com/reference/commandline/run) 在同一个操作中创建并启动一个容器.
* [`docker rm`](https://docs.docker.com/reference/commandline/rm) 删除容器。

如果你想要一个临时容器，`docker run --rm` 会在容器停止之后删除它。

如果你想映射宿主(host)的一个文件夹到 docker 容器，`docker run -v $HOSTDIR:$DOCKERDIR`。参考 [Volumes](https://github.com/wsargent/docker-cheat-sheet/#volumes)。

如果你想同时删除和容器关联的 volumes ，那么在删除容器的时候必须包含 -v 选项，像这样 `docker rm -v`。

## 启动和停止

* [`docker start`](https://docs.docker.com/reference/commandline/start) 启动容器。
* [`docker stop`](https://docs.docker.com/reference/commandline/stop) 停止运行中的容器。
* [`docker restart`](https://docs.docker.com/reference/commandline/restart) 停止之后再启动容器。
* [`docker pause`](https://docs.docker.com/engine/reference/commandline/unpause/) 暂停运行中的容器，将其 "冻结" 在当前状态。
* [`docker unpause`](https://docs.docker.com/engine/reference/commandline/unpause/) 结束容器暂停状态。
* [`docker wait`](https://docs.docker.com/reference/commandline/wait) 阻塞，到运行中的容器停止为止。
* [`docker kill`](https://docs.docker.com/reference/commandline/kill) 向运行中容器发送 SIGKILL 指令。
* [`docker attach`](https://docs.docker.com/reference/commandline/attach) 链接到运行中容器。

如果你想整合容器到[宿主进程管理(host process manager)](https://docs.docker.com/articles/host_integration/)，那么以 `-r=false` 启动守护进程(daemon)然后使用 `docker start -a`。

如果你想通过宿主暴露容器的端口(ports)，请看[暴露端口](#exposing-ports)一节。

故障 docker 实例的重启策略在[这里](http://container42.com/2014/09/30/docker-restart-policies/)。

### 信息

* [`docker ps`](https://docs.docker.com/reference/commandline/ps) 查看运行中的所有容器。
* [`docker logs`](https://docs.docker.com/reference/commandline/logs) 从容器中获取日志。
* [`docker inspect`](https://docs.docker.com/reference/commandline/inspect) 查看某个容器的所有信息(包括 IP 地址)。
* [`docker events`](https://docs.docker.com/reference/commandline/events) 从容器中获取事件(events)。
* [`docker port`](https://docs.docker.com/reference/commandline/port) 查看容器的公开端口。
* [`docker top`](https://docs.docker.com/reference/commandline/top) 查看容器中活动进程。
* [`docker stats`](https://docs.docker.com/reference/commandline/stats) 查看容器的资源使用情况统计信息。
* [`docker diff`](https://docs.docker.com/reference/commandline/diff) 查看容器的 FS 中有变化文件信息。

`docker ps -a` 查看所有容器，包括正在运行的和已停止的。

### 导入 / 导出

* [`docker cp`](https://docs.docker.com/reference/commandline/cp) 在容器和本地文件系统之间复制文件或文件夹。
* [`docker export`](https://docs.docker.com/reference/commandline/export) 将容器的文件系统切换为压缩包(tarball archive stream)输出到 STDOUT。

### 执行命令

* [`docker exec`](https://docs.docker.com/reference/commandline/exec) 在容器中执行命令。

比如，进入正在运行的容器，在名为 foo 的容器中打开一个新的 shell 进程: `docker exec -it foo /bin/bash`.

## 镜像(Images)

镜像是[docker 容器的模板](https://docs.docker.com/introduction/understanding-docker/#how-does-a-docker-image-work)。

### 生命周期

* [`docker images`](https://docs.docker.com/reference/commandline/images) 查看所有镜像。
* [`docker import`](https://docs.docker.com/reference/commandline/import) 从压缩文件中创建镜像。
* [`docker build`](https://docs.docker.com/reference/commandline/build) 从 Dockerfile 创建镜像。
* [`docker commit`](https://docs.docker.com/reference/commandline/commit) 为容器创建镜像，如果容器正在运行则会临时暂停。
* [`docker rmi`](https://docs.docker.com/reference/commandline/rmi) 删除镜像。
* [`docker load`](https://docs.docker.com/reference/commandline/load) 通过 STDIN 从压缩包加载镜像，包括镜像和标签(images and tags) (0.7 起).
* [`docker save`](https://docs.docker.com/reference/commandline/save) 通过 STDOUT 保存镜像到压缩包，包括所有的父层，标签和版本(parent layers, tags & versions) (0.7 起).

### 信息

* [`docker history`](https://docs.docker.com/reference/commandline/history) 查看镜像历史记录。
* [`docker tag`](https://docs.docker.com/reference/commandline/tag) 给镜像命名打标(tags) (本地或者仓库)。

### 清理

虽然你可以用 `docker rmi` 命令来删除指定的镜像，但是这里有个称为 [docker-gc](https://github.com/spotify/docker-gc) 的工具，它可以以一种安全的方式，清理掉那些不再被任何容器使用的镜像。

## 网络(Networks) 

Docker 有[网络(networks)](https://docs.docker.com/engine/userguide/networking/dockernetworks/)功能。我并不是很了解它，所以这是一个扩展本文的好地方。这里有篇笔记指出，这是一种可以不使用端口来达成 docker 容器间通信的好方法。详情查阅[通过网络来工作](https://docs.docker.com/engine/userguide/networking/work-with-networks/)。

### 生命周期

* [`docker network create`](https://docs.docker.com/engine/reference/commandline/network_create/)
* [`docker network rm`](https://docs.docker.com/engine/reference/commandline/network_rm/)

### 信息

* [`docker network ls`](https://docs.docker.com/engine/reference/commandline/network_ls/)
* [`docker network inspect`](https://docs.docker.com/engine/reference/commandline/network_inspect/)

### 链接

* [`docker network connect`](https://docs.docker.com/engine/reference/commandline/network_connect/)
* [`docker network disconnect`](https://docs.docker.com/engine/reference/commandline/network_disconnect/)

## Registry 和 Repository

仓库(repository)是*被托管(hosted)*的已命名镜像(tagged images)集合，这组镜像用于构建容器文件系统。

仓管中心(registry)是一个*托管服务(host)* -- 一个服务，用于存储仓库和提供 HTTP API，以便[管理上传和下载仓库](https://docs.docker.com/userguide/dockerrepos/)。

Docker.com 把它自己的[索引](https://registry.hub.docker.com/)托管到了它的仓管中心，那里有数量众多的仓库。不过话虽如此，这个仓管中心[并没有很好的验证镜像](https://titanous.com/posts/docker-insecurity)，所以如果你很担心安全问题的话，请尽量避免使用它。

* [`docker login`](https://docs.docker.com/reference/commandline/login) 登入仓管中心。
* [`docker search`](https://docs.docker.com/reference/commandline/search) 从仓管中心检索镜像。
* [`docker pull`](https://docs.docker.com/reference/commandline/pull) 从仓管中心拉去镜像到本地。
* [`docker push`](https://docs.docker.com/reference/commandline/push) 从本地推送镜像到仓管中心。

### 本地仓管中心

[如何实现仓管中心](https://github.com/docker/docker-registry)，官方提供了一个镜像，实现了基本的安装，可以通过执行
[`docker run -p 5000:5000 registry`](https://github.com/docker/docker-registry#quick-start)启动。
注意: 该实现并没有提供任何的权限控制。所以你可以通过选项 `-P -p 127.0.0.1:5000:5000` 来限制只能从本机接入。
为了推送仓库到该中心，请把镜像的标签命名为 `repositoryHostName:5000/imageName` ，然后推送该标签。

## Dockerfile

[配置文件](https://docs.docker.com/reference/builder/)。当你执行 `docker build` 的时候会根据该配置文件设置 Docker 容器。远优于使用 `docker commit`。如果你使用 [jEdit](http://jedit.org)，我为 [Dockerfile](https://github.com/wsargent/jedit-docker-mode)做了个语法高亮模块。你还可以试试 [工具集](#tools)部分的内容。

### 指令

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

### 教程

* [Flux7's Dockerfile Tutorial](http://flux7.com/blogs/docker/docker-tutorial-series-part-3-automation-is-the-word-using-dockerfile/)

### 例子

* [Examples](https://docs.docker.com/reference/builder/#dockerfile-examples)
* [Best practices for writing Dockerfiles](https://docs.docker.com/articles/dockerfile_best-practices/)
* [Michael Crosby](http://crosbymichael.com/) has some more [Dockerfiles best practices](http://crosbymichael.com/dockerfile-best-practices.html) / [take 2](http://crosbymichael.com/dockerfile-best-practices-take-2.html).

## 层(Layers)

Docker 的版本化文件系统是基于层的。就像[git的提交或文件变更系统](https://docs.docker.com/terms/layer/)一样。

注意: 如果你使用 [aufs](https://en.wikipedia.org/wiki/Aufs) 作为你的文件系统，当删除一个容器的时候，Docker 并不一定能成功删除的文件卷标！更多详细信息请参阅 [PR 8484](https://github.com/docker/docker/pull/8484)。

## 链接(Links)

链接(Links)[通过 TCP/IP 端口](https://docs.docker.com/userguide/dockerlinks/)实现了 Docker 容器之间的通讯。[链接到 Redis](https://docs.docker.com/examples/running_redis_service/) 和 [Atlassian](https://blogs.atlassian.com/2013/11/docker-all-the-things-at-atlassian-automation-and-wiring/) 是两个可用的例子。你还可以(0.11 开始)[通过 hostname 关联链接](https://docs.docker.com/userguide/dockerlinks/#updating-the-etchosts-file)。

注意: 如果你希望容器之间**只**通过链接进行通讯，在启动 docker 守护进程的时候请添加参数 `-icc=false` 来禁用内部进程通讯。

如果你有一个名为 CONTAINER 的容器(通过 `docker run --name CONTAINER` 指定) 并且在 Dockerfile 中，它的端口暴露为:

```
EXPOSE 1337
```

然后，我们创建另外一个名为 LINKED 的容器:

```
docker run -d --link CONTAINER:ALIAS --name LINKED user/wordpress
```

然后 CONTAINER 的端口和别名将会以如下的环境变量出现在 LINKED 中:

```
$ALIAS_PORT_1337_TCP_PORT
$ALIAS_PORT_1337_TCP_ADDR
```

之后你就可以通过这种方式来链接它了。

要删除链接，通过命令 `docker rm --link `。

如果你想跨 docker 主机链接，你可以查看 [Swarm](https://docs.docker.com/swarm/) 部分。. 在 [stackoverflow 上的这个链接](https://stackoverflow.com/questions/21283517/how-to-link-docker-services-across-hosts)也提供了一些关于如何跨 docker 主机进行链接的好方式。

## 卷标(Volumes)

Docker 的卷标(volumes)是一个[free-floating 文件系统](https://docs.docker.com/userguide/dockervolumes/)。它们不应该链接到特定的容器上。好的做法是如果可能，应当把卷标挂载到[纯数据容器(data-only containers)](https://medium.com/@ramangupta/why-docker-data-containers-are-good-589b3c6c749e)上。

### 生命周期

* [`docker volumes create`](https://docs.docker.com/engine/reference/commandline/volume_create/)
* [`docker volumes rm`](https://docs.docker.com/engine/reference/commandline/volume_rm/)

### 信息

* [`docker volumes ls`](https://docs.docker.com/engine/reference/commandline/volume_ls/)
* [`docker volumes inspect`](https://docs.docker.com/engine/reference/commandline/volume_inspect/)

卷标在不能使用链接(只有 TCP/IP )的情况下非常有用。例如，如果你有两个 docker 实例需要通讯并在文件系统上留下记录。

你可以一次性将其挂载到多个 docker 容器上，通过 `docker run --volumes-from`。

因为卷标是独立的文件系统，它们通常被用于存储各容器之间的瞬时状态。也就是说，你可以配置一个无状态临时容器，关掉之后，当你有第二个这种临时容器实例的时候，你可以从上一次保存的状态继续执行。

查看[卷标进阶](http://crosbymichael.com/advanced-docker-volumes.html)来获取更多细节。Container42 [非常有用](http://container42.com/2014/11/03/docker-indepth-volumes/)。

从 1.3 开始，你可以[映射宿主 MacOS 的文件夹作为 docker 卷标](https://docs.docker.com/userguide/dockervolumes/#mount-a-host-directory-as-a-data-volume)通过 boot2docker:

```
docker run -v /Users/wsargent/myapp/src:/src
```

你也可以用远程 NFS 卷标，如果你觉得你[有足够勇气](http://www.tech-d.net/2014/03/29/docker-quicktip-4-remote-volumes/)。

可还可以考虑运行一个纯数据容器，像[这里](http://container42.com/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/)所说的那样，提供可移植数据。

## 暴露端口(Exposing ports)

通过宿主容器暴露输入端口是相当[繁琐，但有效](https://docs.docker.com/reference/run/#expose-incoming-ports)的。

这种方式可以将容器端口映射到宿主端口上(只使用本地主机(localhost)接口)，通过使用 `-p`:

```
docker run -p 127.0.0.1:$HOSTPORT:$CONTAINERPORT --name CONTAINER -t someimage
```

你可以告诉 Docker 容器在运行时监听指定的网络端口，通过使用 [EXPOSE](https://docs.docker.com/reference/builder/#expose):

```
EXPOSE <CONTAINERPORT>
```

但是注意 EXPOSE 并不会暴露端口本身，只有 `-p` 这样做。

如果你是在 Virtualbox 中运行 Docker，那么你需要转发端口(forward the port)，使用 [forwarded_port](https://docs.vagrantup.com/v2/networking/forwarded_ports.html)。它可以用于在 Vagrantfile 上配置暴露端口段，这样你就可以动态的映射它们了:

```
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  ...

  (49000..49900).each do |port|
    config.vm.network :forwarded_port, :host => port, :guest => port
  end

  ...
end
```

如果你忘记你将什么端口映射到宿主容器上的话，使用 `docker port` 来查看它:

```
docker port CONTAINER $CONTAINERPORT
```

## 最佳实践

This is where general Docker best practices and war stories go:

* [The Rabbit Hole of Using Docker in Automated Tests](http://gregoryszorc.com/blog/2014/10/16/the-rabbit-hole-of-using-docker-in-automated-tests/)
* [Bridget Kromhout](https://twitter.com/bridgetkromhout) has a useful blog post on [running Docker in production](http://sysadvent.blogspot.co.uk/2014/12/day-1-docker-in-production-reality-not.html) at Dramafever.  
* There's also a best practices [blog post](http://developers.lyst.com/devops/2014/12/08/docker/) from Lyst.
* [A Docker Dev Environment in 24 Hours!](https://engineering.salesforceiq.com/2013/11/05/a-docker-dev-environment-in-24-hours-part-2-of-2.html)
* [Building a Development Environment With Docker](http://tersesystems.com/2013/11/20/building-a-development-environment-with-docker/)
* [Discourse in a Docker Container](http://samsaffron.com/archive/2013/11/07/discourse-in-a-docker-container)

## Security

This is where security tips about Docker go.  The [security](https://docs.docker.com/engine/articles/security/) page goes into more detail.

First things first: Docker runs as root.  If you are in the `docker` group, you effectively [have root access](http://reventlov.com/advisories/using-the-docker-command-to-root-the-host).  If you expose the docker unix socket to a container, you are giving the container [root access to the host](https://www.lvh.io/posts/dont-expose-the-docker-socket-not-even-to-a-container.html).  Docker should not be your only defense.

### Security Tips

For greatest security, you want to run Docker inside a virtual machine, or on a host.  This is straight from the Docker Security Team Lead -- [slides](http://www.slideshare.net/jpetazzo/linux-containers-lxc-docker-and-security) / [notes](http://www.projectatomic.io/blog/2014/08/is-it-safe-a-look-at-docker-and-security-from-linuxcon/).  Then, run with AppArmor / seccomp / SELinux / grsec etc to [limit the container permissions](http://linux-audit.com/docker-security-best-practices-for-your-vessel-and-containers/).

Docker image ids are [sensitive information](https://medium.com/@quayio/your-docker-image-ids-are-secrets-and-its-time-you-treated-them-that-way-f55e9f14c1a4) and should not be exposed to the outside world.  Treat them like passwords.

See the [Docker Security Cheat Sheet](https://github.com/konstruktoid/Docker/blob/master/Security/CheatSheet.md) by [Thomas Sjögren](https://github.com/konstruktoid): some good stuff about container hardening in there.

Check out the [docker bench security script](https://github.com/docker/docker-bench-security), download the [white papers](https://blog.docker.com/2015/05/understanding-docker-security-and-best-practices/) and subscribe to the [mailing lists](https://www.docker.com/docker-security) (unfortunately Docker does not have a unique mailing list, only dev / user).

You should start off by using a kernel with unstable patches for grsecurity / pax compiled in, such as [Alpine Linux](https://en.wikipedia.org/wiki/Alpine_Linux).  If you are using grsecurity in production, you should spring for [commercial support](https://grsecurity.net/business_support.php) for the [stable patches](https://grsecurity.net/announce.php), same as you would do for RedHat.  It's $200 a month, which is nothing to your devops budget.

From the [Docker Security Cheat Sheet](http://container-solutions.com/content/uploads/2015/06/15.06.15_DockerCheatSheet_A2.pdf) (it's in PDF which makes it hard to use, so copying below) by [Container Solutions](http://container-solutions.com/is-docker-safe-for-production/):

Turn off interprocess communication with:

```
docker -d --icc=false --iptables
```

Set the container to be read-only:

```
docker run --read-only
```

Verify images with a hashsum:

```
docker pull debian@sha256:a25306f3850e1bd44541976aa7b5fd0a29be
```

Set volumes to be read only:

```
docker run -v $(pwd)/secrets:/secrets:ro debian
```

Set memory and CPU sharing:

```
docker -c 512 -mem 512m
```

Define and run a user in your Dockerfile so you don't run as root inside the container:

```
RUN groupadd -r user && useradd -r -g user user
USER user
```

### Security Videos

* [Using Docker Safely](https://youtu.be/04LOuMgNj9U)
* [Securing your applications using Docker](https://youtu.be/KmxOXmPhZbk)
* [Container security: Do containers actually contain?](https://youtu.be/a9lE9Urr6AQ)

### Security Roadmap

The Docker roadmap talks about [seccomp support](https://github.com/docker/docker/blob/master/ROADMAP.md#11-security).
There is an AppArmor policy generator called [bane](https://github.com/jfrazelle/bane), and they're working on [security profiles](https://github.com/docker/docker/issues/17142).  There's also work on [user namespaces](https://s3hh.wordpress.com/2013/07/19/creating-and-using-containers-without-privilege/) which [just made it out of experimental](https://github.com/docker/docker/commit/cc63db4fd19f99372a84cc97a87a023fa9193734#diff-991890e619874cd6bb0277584bb7f7a4R632).

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

or using a [go template](https://docs.docker.com/reference/commandline/inspect)

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

### Delete dangling volumes

As of Docker 1.9:

```
docker volume rm $(docker volume ls -q -f dangling=true)
```

In 1.9.0, the filter `dangling=false` does _not_ work - it is ignored and will list all volumes.

### Show image dependencies

```
docker images -viz | dot -Tpng -o docker.png
```

### Slimming down Docker containers  [Intercity Blog](http://bit.ly/1Wwo61N)

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

### Monitor system resource utilization for running containers

To check the CPU, memory, and network i/o usage of a single container, you can use:

```
docker stats <container>
```

For all containers listed by id:

```
docker stats $(docker ps -q)
```

For all containers listed by name:

```
docker stats $(docker ps --format '{{.Names}}')
```
