# Docker Cheat Sheet

**Want to improve this cheat sheet?  See the [Contributing](#contributing) section!**

## Содержание

* [Почему Docker](#Почему-Docker)
* [Предпосылки](#Предпосылки)
* [Установка](#Установка)
* [Контейнеры](#Контейнеры)
* [Образы](#images)
* [Сеть](#networks)
* [Реестр и репозиторий](#registry--repository)
* [Dockerfile](#dockerfile)
* [Слои](#layers)
* [Ссылка](#links)
* [Тома](#volumes)
* [Отображение портов](#exposing-ports)
* [Лучшая практика](#best-practices)
* [Безопасность](#security)
* [Советы](#tips)
* [Содействие](#contributing)

## Почему Docker

"С Docker разработчики могут создавать любое приложение на любом языке, используя любую инструментальную цепочку. Приложения помещаются в контейнер - становятся полностью переносимы и могут работать где угодно - на компьютерах под управлением OS X и Windows, серверах QA, работающих под управлением Ubuntu в облаке, и виртуальных машинах производственного центра обработки данных Red Hat.

Разработчики могут быстро начать работу, начиная с одного из 13 000 приложений, доступных на Docker Hub. Docker управляет и отслеживает изменения и зависимости, что облегчает для системных администраторов понимание того, как работают приложения, созданные разработчиками. И с Docker Hub разработчики могут автоматизировать свой процес сборки и совместно использовать артефакты с сотрудниками через публичные или частные репозитории.

Docker помогает разработчикам создавать и отправлять более качественные приложения быстрее " -- [Что такое Docker](https://www.docker.com/what-docker#copy1)

## Предпосылки

Я использую [Oh My Zsh](https://github.com/robbyrussell/oh-my-zsh) вместе с [Docker plugin](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins#docker) для автозаполнения команд docker. Возможно у вас другой подход.

### Linux

Ядро 3.10.x [минимальное требование] (https://docs.docker.com/engine/installation/binaries/#check-kernel-dependencies) для Docker.

### MacOS

 10.8 “Mountain Lion” или более новый.

## Установка

### Linux

Быстрый и простой скрипт установки, предоставляемый Docker:

```
curl -sSL https://get.docker.com/ | sh
```

Если вы не хотите запускать случайный сценарий оболочки, см. [Инструкции](https://docs.docker.com/engine/installation/linux/) по установке на ваш дистрибутив.

Если вы являетесь полноправным новичком Docker, вы должны следовать [сериям учебников] (https://docs.docker.com/engine/getstarted/) сейчас.

### macOS
Скачать и установить [Docker Community Edition](https://www.docker.com/community-edition). если у вас есть Homebrew-Cask, просто введите `brew cask install docker`.
Или загрузите и установите [Docker Toolbox](https://docs.docker.com/toolbox/overview/).  [Docker для Mac](https://docs.docker.com/docker-for-mac/) это хорошо, но это не совсем так, как установка VirtualBox.  [
См. Сравнение](https://docs.docker.com/docker-for-mac/docker-toolbox/).

> ** ПРИМЕЧАНИЕ ** Docker Toolbox является устаревшим. вы должны использовать Docker Community Edition, см. (Docker Toolbox)[https://docs.docker.com/toolbox/overview/]

После установки Docker Community Edition щелкните значок докера. Затем запустите контейнер:

```
docker run hello-world
```

Вот и все, у вас есть работающий контейнер Docker.


Если вы являетесь полноправным новичком докеров, вы должны, вероятно, исследовать [серию учебников] (https://docs.docker.com/engine/getstarted/) сейчас.

## Контейнеры

[Ваш основной изолированный процесс Докера](http://etherealmind.com/basics-docker-containers-hypervisors-coreos/). Контейнеры - это виртуальные машины, поскольку потоки относятся к процессам. Или вы можете думать о них как о chroot на стероидах.

### Жизненный цикл


* [`docker create`](https://docs.docker.com/engine/reference/commandline/create) создает контейнер, но не запускает его.
* [`docker rename`](https://docs.docker.com/engine/reference/commandline/rename/) позволяет переименовать контейнер.
* [`docker run`](https://docs.docker.com/engine/reference/commandline/run) создает и запускает контейнер за одну операцию.
* [`docker rm`](https://docs.docker.com/engine/reference/commandline/rm) удаляет контейнер.
* [`docker update`](https://docs.docker.com/engine/reference/commandline/update/) обновляет ограничения ресурсов контейнера.

Обычно, если вы запускаете контейнер без параметров, он запускается и останавливается немедленно, если вы хотите его запустить, вы можете использовать команду, `docker run -td container_id` это будет использовать опцию `-t` который будет выделять псевдо-TTY сессию и `-d` который автоматически отсоединяет контейнер (запускает контейнер в фоновом режиме и показыват ID контейнера).

Если вам нужен переходный контейнер, `docker run --rm` удалит контейнер после его остановки.

Если вы хотите сопоставить каталог на хосте с контейнером докера, `docker run -v $HOSTDIR:$DOCKERDIR`. Также смотрите [Тома](https://github.com/wsargent/docker-cheat-sheet/#volumes).

Если вы хотите удалить также тома, связанные с контейнером, удаление контейнера должно включать `-v` измените примерно так `docker rm -v`.

Существует также [логирование](https://docs.docker.com/engine/admin/logging/overview/) доступны для отдельных контейнеров в докерах 1.10. Чтобы запустить докер с помощью специального лог журнала (например, в syslog), используйте `docker run --log-driver=syslog`.

Другим полезным вариантом является `docker run --name yourname docker_image` потому что, когда вы укажете `--name` внутри команды run это позволит вам запускать и останавливать контейнер, вызывая его с именем, которое вы указали при его создании.

### Запуск и остановка

* [`docker start`](https://docs.docker.com/engine/reference/commandline/start) запускает контейнер, чтобы он работал.
* [`docker stop`](https://docs.docker.com/engine/reference/commandline/stop) останавливает запущенный контейнер.
* [`docker restart`](https://docs.docker.com/engine/reference/commandline/restart) останавливается и запускает контейнер.
* [`docker pause`](https://docs.docker.com/engine/reference/commandline/pause/)
приостанавливает работу контейнера, "замораживает" его на месте.
* [`docker unpause`](https://docs.docker.com/engine/reference/commandline/unpause/) снимает "заморозку" контейнера.
* [`docker wait`](https://docs.docker.com/engine/reference/commandline/wait) блокирует до остановки контейнера.
* [`docker kill`](https://docs.docker.com/engine/reference/commandline/kill) посылает SIGKILL к запущеннному контейнеру.
* [`docker attach`](https://docs.docker.com/engine/reference/commandline/attach) будет подключаться к работающему контейнеру.


Если вы хотите интегрировать контейнер с [диспетчером хостов](https://docs.docker.com/engine/admin/host_integration/), запустите демона с помощью `-r = false`, а затем используйте` docker start -a `.

Если вы хотите открыть порты контейнера через хост, см. Раздел [раскрытие портов](#открытие-портов).

Перезагрузка политик в разбитых экземплярах докеров [рассматривается здесь] (http://container42.com/2014/09/30/docker-restart-policies/).

#### Ограничения процессора

Вы можете ограничить процессор, используя либо процент от всех процессоров, либо используя определенные ядра.

Например, вы можете указать параметр [`cpu-shares`](https://docs.docker.com/engine/reference/run/#/cpu-share-constraint). Параметр немного странный - 1024 означает 100% CPU, поэтому, если вы хотите, чтобы контейнер занимал 50% всех ядер процессора, вы должны указать 512. См. https://goldmann.pl/blog/2014/09/11/resource-management-in-docker/#_cpu для получения дополнительной информации:


```
docker run -ti --c 512 agileek/cpuset-test
```
Вы также можете использовать только некоторые ядра процессора, используя [`cpuset-cpus`](https://docs.docker.com/engine/reference/run/#/cpuset-constraint). См. https://agileek.github.io/docker/2014/08/06/docker-cpuset/ для получения дополнительной информации:


```
docker run -ti --cpuset-cpus=0,4,6 agileek/cpuset-test
```
Обратите внимание, что Docker все еще может **видеть** все процессоры внутри контейнера -- он просто не использует все из них. Подробнее см. https://github.com/docker/docker/issues/20770.


#### Ограничения памяти

Вы также можете установить [ограничения памяти](https://docs.docker.com/engine/reference/run/#/user-memory-constraints) на Docker:

```
docker run -it -m 300M ubuntu:14.04 /bin/bash
```

#### Возможности

Возможности Linux можно установить, используя `cap-add` и `cap-drop`.  См. https://docs.docker.com/engine/reference/run/#/runtime-privilege-and-linux-capabilities для подробностей.  Это должно использоваться для большей безопасности.

Чтобы подключить файловую систему на основе FUSE, вам необходимо объединить оба --cap-add и --device:

```
docker run --rm -it --cap-add SYS_ADMIN --device /dev/fuse sshfs
```

Обеспечить доступ к одному устройству:

```
docker run -it --device=/dev/ttyUSB0 debian bash
```

Обеспечить доступ ко всем устройствам:

```
docker run -it --privileged -v /dev/bus/usb:/dev/bus/usb debian bash
```

подробнее о привилегированных контейнерах [здесь](
https://docs.docker.com/engine/reference/run/#/runtime-privilege-and-linux-capabilities)


### Info

* [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps) показывает запущенные контейнеры.
* [`docker logs`](https://docs.docker.com/engine/reference/commandline/logs) получает журналы из контейнера. (Вы можете использовать собственный драйвер журнала, но журналы доступны только для `json-file` и  `journald` в  1.10).
* [`docker inspect`](https://docs.docker.com/engine/reference/commandline/inspect) просматривает всю информацию о контейнере (включая IP-адрес).
* [`docker events`](https://docs.docker.com/engine/reference/commandline/events) получает события из контейнера.
* [`docker port`](https://docs.docker.com/engine/reference/commandline/port) показывает открытый порт контейнера.
* [`docker top`](https://docs.docker.com/engine/reference/commandline/top) показывает запущенные процессы в контейнере.
* [`docker stats`](https://docs.docker.com/engine/reference/commandline/stats) показывает статистику использования ресурсов контейнеров.
* [`docker diff`](https://docs.docker.com/engine/reference/commandline/diff) показывает измененные файлы в FS контейнера.

`docker ps -a` показывает запущенные и остановленные контейнеры.

`docker stats --all` показывает текущий список контейнеров.

### Импорт / Экспорт

* [`docker cp`](https://docs.docker.com/engine/reference/commandline/cp) копирует файлы или папки между контейнером и локальной файловой системой.
* [`docker export`](https://docs.docker.com/engine/reference/commandline/export) экспортировать файловую систему контейнера в качестве tar-архива.

### Выполнение команд

* [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec) для выполнения команды в контейнере.


Чтобы войти в запущенный контейнер, присоедините новый процесс оболочки к запущенному контейнеру с именем foo, используйте:`docker exec -it foo /bin/bash`.

## Образы

Образы - это просто [шаблоны для docker контейнеров](https://docs.docker.com/engine/understanding-docker/#how-does-a-docker-image-work).

### Жизненный цикл

* [`docker images`](https://docs.docker.com/engine/reference/commandline/images) показывает все образы.
* [`docker import`](https://docs.docker.com/engine/reference/commandline/import) создает образ из архива.
* [`docker build`](https://docs.docker.com/engine/reference/commandline/build) создает образ из Dockerfile.
* [`docker commit`](https://docs.docker.com/engine/reference/commandline/commit) создает образ из контейнера, временно приостанавливая его, если он запущен.
* [`docker rmi`](https://docs.docker.com/engine/reference/commandline/rmi) удаляет образ.
* [`docker load`](https://docs.docker.com/engine/reference/commandline/load) загружает образ из архива tar в качестве STDIN, включая образы и теги (начиная с 0.7).
* [`docker save`](https://docs.docker.com/engine/reference/commandline/save) сохраняет образ в поток архива tar в STDOUT со всеми родительскими слоями, тегами и версиями (начиная с 0,7).

### Info

* [`docker history`](https://docs.docker.com/engine/reference/commandline/history) показывает историю образа.
* [`docker tag`](https://docs.docker.com/engine/reference/commandline/tag) теги образа к имени (локальному или реестру).

## Проверка версии Docker

Очень важно, чтобы вы всегда знали текущую версию Docker, в которой вы сейчас работаете, в любой момент времени. Это очень полезно, потому что вы узнаете, какие функции совместимы с тем, что вы используете. Это также важно, потому что вы знаете, какие контейнеры запускать из хранилища докеров, когда вы пытаетесь получить контейнеры шаблонов. Это говорит о том, как узнать, какая версия докера у нас работает в настоящее время:


* ['docker version'](https://docs.docker.com/engine/reference/commandline/version/)   проверьте, какая версия докера у вас запущена.
* [docker version [OPTIONS]]

Получить версию сервера
$ docker version --format '{{.Server.Version}}'

1.8.0
Dump raw JSON data
$ docker version --format '{{json .}}'

{"Client":{"Version":"1.8.0","ApiVersion":"1.20","GitCommit":"f5bae0a","GoVersion":"go1.4.2","Os":"linux","Arch":"am"}

### Cleaning up
Хотя вы можете использовать команду `docker rmi` для удаления определенных образов, есть инструмент под названием [docker-gc](https://github.com/spotify/docker-gc), который будет безопасно очищать образы, которые больше не используются любыми контейнерами.

### Загрузка/Сохранение образов

Загрузите образ из файла:
```
docker load < my_image.tar.gz
```

Сохранить существующий образ:
```
docker save my_image:my_tag | gzip > my_image.tar.gz
```
### Импорт/Экспорт контейнера

Импортировать контейнер как образ из файла:
```
cat my_container.tar.gz | docker import - my_image:my_tag
```

Экспортировать существующий контейнер:
```
docker export my_container | gzip > my_container.tar.gz
```


### Разница между загрузкой сохраненного образа и импортом экспортированного контейнера в качестве образа

Загрузка изображения с помощью команды `load` создает новый образ, включая его историю.
Импорт контейнера в качестве образа с помощью команды `import` создает новый образ, исключая историю, которая приводит к меньшему размеру образов по сравнению с загрузкой образа.

## Сети
Docker имеет функцию [network](https://docs.docker.com/engine/userguide/networking/). Об этом мало что известно, поэтому это хорошее место для расширения чит-листа. Существует примечание, в котором говорится, что это хороший способ настроить контейнеры докеров, чтобы разговаривать друг с другом без использования портов. Подробнее см. [Работа с сетями](https://docs.docker.com/engine/userguide/networking/work-with-networks/).

### Жизненный цикл

* [`docker network create`](https://docs.docker.com/engine/reference/commandline/network_create/)
* [`docker network rm`](https://docs.docker.com/engine/reference/commandline/network_rm/)

### Info

* [`docker network ls`](https://docs.docker.com/engine/reference/commandline/network_ls/)
* [`docker network inspect`](https://docs.docker.com/engine/reference/commandline/network_inspect/)

### Connection

* [`docker network connect`](https://docs.docker.com/engine/reference/commandline/network_connect/)
* [`docker network disconnect`](https://docs.docker.com/engine/reference/commandline/network_disconnect/)

Вы можете указать [конкретный IP-адрес для контейнера](https://blog.jessfraz.com/post/ips-for-all-the-things/):

```
# создать новую сеть bridge с вашей подсетью и шлюзом для вашего ip-блока
docker network create --subnet 203.0.113.0/24 --gateway 203.0.113.254 iptastic

# запустите контейнер nginx с определенным ip в этом блоке
$ docker run --rm -it --net iptastic --ip 203.0.113.2 nginx

# curl ip из любого другого места (при условии, что это общедоступный ip-блок)
$ curl 203.0.113.2
```

## Реестр и репозиторий

Репозиторий - это * размещенная * коллекция помеченных образов, которые вместе создают файловую систему для контейнера.

Реестр - это * хост * - сервер, который хранит репозитории и предоставляет HTTP API для [управления загрузкой и загрузкой репозиториев](https://docs.docker.com/engine/tutorials/dockerrepos/).

Docker.com размещает свой собственный [index](https://hub.docker.com/) в центральном реестре, который содержит большое количество репозиториев. Сказав это, центральный реестр докеров (не делает хорошую работу по проверке образов)(https://titanous.com/posts/docker-insecurity), и его следует избегать, если вас беспокоит безопасность.

* [`docker login`](https://docs.docker.com/engine/reference/commandline/login) для входа в реестр.
* [`docker logout`](https://docs.docker.com/engine/reference/commandline/logout) для выхода из реестра.
* [`docker search`](https://docs.docker.com/engine/reference/commandline/search) ищет реестр для образа.
* [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull) вытаскивает образ из реестра на локальный компьютер.
* [`docker push`](https://docs.docker.com/engine/reference/commandline/push) толкает образ в реестр с локальной машины.

### Запуск локального реестра

Вы можете запустить локальный реестр с помощью проекта [docker distribution](https://github.com/docker/distribution) и посмотреть на [локальное развертывание](https://github.com/docker/docker.github.io/blob/master/registry/deploying.md) инструкци.

Также см. [Список рассылки](https://groups.google.com/a/dockerproject.org/forum/#!forum/distribution).

## Dockerfile

[Файл конфигурации](https://docs.docker.com/engine/reference/builder/). Устанавливает контейнер Docker, когда вы запускаете на нем `docker build`. Крайне предпочтительнее `docker commit`.

Вот некоторые распространенные текстовые редакторы и их модули подсветки синтаксиса, которые вы могли бы использовать для создания Dockerfiles:
* Если вы используете [jEdit](http://jedit.org), я установил модуль подсветки синтаксиса для [Dockerfile](https://github.com/wsargent/jedit-docker-mode) вы можете использовать.
* [Sublime Text 2](https://packagecontrol.io/packages/Dockerfile%20Syntax%20Highlighting)
* [Atom](https://atom.io/packages/language-docker)
* [Vim](https://github.com/ekalinin/Dockerfile.vim)
* [Emacs](https://github.com/spotify/dockerfile-mode)
* [TextMate](https://github.com/docker/docker/tree/master/contrib/syntax/textmate)
* [VS Code](https://github.com/Microsoft/vscode-docker)
* Также см. [Docker meets the IDE](https://domeide.github.io/)

### Инструкции

* [.dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)
* [FROM](https://docs.docker.com/engine/reference/builder/#from) Устанавливает базовое изображение для последующих инструкций.
* [MAINTAINER (устаревший - вместо этого используйте LABEL)](https://docs.docker.com/engine/reference/builder/#maintainer-deprecated) Задайте поле Author созданных образов.
* [RUN](https://docs.docker.com/engine/reference/builder/#run) выполнять любые команды в новом слое поверх текущего образа и фиксировать результаты.
* [CMD](https://docs.docker.com/engine/reference/builder/#cmd) предоставлять значения по умолчанию для исполняемого контейнера.
* [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) сообщает Docker, что контейнер прослушивает указанные сетевые порты во время выполнения. ПРИМЕЧАНИЕ: на самом деле не делает доступными порты.
* [ENV](https://docs.docker.com/engine/reference/builder/#env) устанавливает переменную среды.
* [ADD](https://docs.docker.com/engine/reference/builder/#add) копирует в контейнер новые файлы, каталоги или удаленный файл. Недействительный кеш. Избегайте `ADD` и вместо этого используйте` COPY`.
* [COPY](https://docs.docker.com/engine/reference/builder/#copy) копирует в контейнер новые файлы или каталоги. Обратите внимание, что это копируется только с правами root, поэтому вы должны вручную управлять вне зависимости от настроек USER / WORKDIR. См. https://github.com/moby/moby/issues/30110
* [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) настраивает контейнер, который будет запускаться как исполняемый файл.
* [VOLUME](https://docs.docker.com/engine/reference/builder/#volume) создает точку монтирования для внешних томов или других контейнеров.
* [USER](https://docs.docker.com/engine/reference/builder/#user) задает имя пользователя для следующих команд RUN / CMD / ENTRYPOINT.
* [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) устанавливает рабочий каталог.
* [ARG](https://docs.docker.com/engine/reference/builder/#arg) определяет переменную времени сборки.
* [ONBUILD](https://docs.docker.com/engine/reference/builder/#onbuild) добавляет инструкцию триггера, когда изображение используется в качестве основы для другой сборки.
* [STOPSIGNAL](https://docs.docker.com/engine/reference/builder/#stopsignal) устанавливает сигнал системного вызова, который будет отправлен в контейнер для выхода.
* [LABEL](https://docs.docker.com/engine/userguide/labels-custom-metadata/) устанавливает сигнал системного вызова, который будет отправлен в контейнер для выхода.

### Tutorial

* [Учебник Flux7's Dockerfile
](http://flux7.com/blogs/docker/docker-tutorial-series-part-3-automation-is-the-word-using-dockerfile/)

### Примеры

* [Примеры](https://docs.docker.com/engine/reference/builder/#dockerfile-examples)
* [Рекомендации по написанию Dockerfiles](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)
* [Michael Crosby](http://crosbymichael.com/) has some more [Dockerfiles best practices](http://crosbymichael.com/dockerfile-best-practices.html) / [take 2](http://crosbymichael.com/dockerfile-best-practices-take-2.html).
* [Building Good Docker Images](http://jonathan.bergknoff.com/journal/building-good-docker-images) / [Building Better Docker Images](http://jonathan.bergknoff.com/journal/building-better-docker-images)
* [Managing Container Configuration with Metadata](https://speakerdeck.com/garethr/managing-container-configuration-with-metadata)
* [How to write excellent Dockerfiles](https://rock-it.pl/how-to-write-excellent-dockerfiles/)

## Layers

The versioned filesystem in Docker is based on layers. They're like [git commits or changesets for filesystems](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/).

## Links

Links are how Docker containers talk to each other [through TCP/IP ports](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/). [Linking into Redis](https://docs.docker.com/engine/examples/running_redis_service/) and [Atlassian](https://blogs.atlassian.com/2013/11/docker-all-the-things-at-atlassian-automation-and-wiring/) show worked examples. You can also resolve [links by hostname](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/#/updating-the-etchosts-file).

This has been deprected to some extent by [user-defined networks](https://docs.docker.com/engine/userguide/networking/#user-defined-networks).

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

To delete links, use `docker rm --link`.

Generally, linking between docker services is a subset of "service discovery", a big problem if you're planning to use Docker at scale in production.  Please read [The Docker Ecosystem: Service Discovery and Distributed Configuration Stores](https://www.digitalocean.com/community/tutorials/the-docker-ecosystem-service-discovery-and-distributed-configuration-stores) for more info.

## Volumes

Docker volumes are [free-floating filesystems](https://docs.docker.com/engine/tutorials/dockervolumes/). They don't have to be connected to a particular container. You should use volumes mounted from [data-only containers](https://medium.com/@ramangupta/why-docker-data-containers-are-good-589b3c6c749e) for portability.  

### Lifecycle

* [`docker volume create`](https://docs.docker.com/engine/reference/commandline/volume_create/)
* [`docker volume rm`](https://docs.docker.com/engine/reference/commandline/volume_rm/)

### Info

* [`docker volume ls`](https://docs.docker.com/engine/reference/commandline/volume_ls/)
* [`docker volume inspect`](https://docs.docker.com/engine/reference/commandline/volume_inspect/)

Volumes are useful in situations where you can't use links (which are TCP/IP only). For instance, if you need to have two docker instances communicate by leaving stuff on the filesystem.

You can mount them in several docker containers at once, using `docker run --volumes-from`.

Because volumes are isolated filesystems, they are often used to store state from computations between transient containers. That is, you can have a stateless and transient container run from a recipe, blow it away, and then have a second instance of the transient container pick up from where the last one left off.

See [advanced volumes](http://crosbymichael.com/advanced-docker-volumes.html) for more details. Container42 is [also helpful](http://container42.com/2014/11/03/docker-indepth-volumes/).

You can [map MacOS host directories as docker volumes](https://docs.docker.com/engine/tutorials/dockervolumes/#mount-a-host-directory-as-a-data-volume):

```
docker run -v /Users/wsargent/myapp/src:/src
```

You can use remote NFS volumes if you're [feeling brave](https://docs.docker.com/engine/tutorials/dockervolumes/#/mount-a-shared-storage-volume-as-a-data-volume).

You may also consider running data-only containers as described [here](http://container42.com/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/) to provide some data portability.

[Be aware that you can mount files as volumes.](#volumes-can-be-files)


## Открытие портов


Exposing incoming ports through the host container is [fiddly but doable](https://docs.docker.com/engine/reference/run/#expose-incoming-ports).

This is done by mapping the container port to the host port (only using localhost interface) using `-p`:

```
docker run -p 127.0.0.1:$HOSTPORT:$CONTAINERPORT --name CONTAINER -t someimage
```

You can tell Docker that the container listens on the specified network ports at runtime by using [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose):

```
EXPOSE <CONTAINERPORT>
```

Note that EXPOSE does not expose the port itself -- only `-p` will do that. To expose the container's port on your localhost's port:

```
iptables -t nat -A DOCKER -p tcp --dport <LOCALHOSTPORT> -j DNAT --to-destination <CONTAINERIP>:<PORT>
```

If you're running Docker in Virtualbox, you then need to forward the port there as well, using [forwarded_port](https://docs.vagrantup.com/v2/networking/forwarded_ports.html). Define a range of ports in your Vagrantfile like this so you can dynamically map them:

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

## Best Practices

This is where general Docker best practices and war stories go:

* [The Rabbit Hole of Using Docker in Automated Tests](http://gregoryszorc.com/blog/2014/10/16/the-rabbit-hole-of-using-docker-in-automated-tests/)
* [Bridget Kromhout](https://twitter.com/bridgetkromhout) has a useful blog post on [running Docker in production](http://sysadvent.blogspot.co.uk/2014/12/day-1-docker-in-production-reality-not.html) at Dramafever.
* There's also a best practices [blog post](http://developers.lyst.com/devops/2014/12/08/docker/) from Lyst.
* [Building a Development Environment With Docker](https://tersesystems.com/2013/11/20/building-a-development-environment-with-docker/)
* [Discourse in a Docker Container](https://samsaffron.com/archive/2013/11/07/discourse-in-a-docker-container)

## Security

This is where security tips about Docker go. The Docker [security](https://docs.docker.com/engine/security/security/) page goes into more detail.

First things first: Docker runs as root. If you are in the `docker` group, you effectively [have root access](http://reventlov.com/advisories/using-the-docker-command-to-root-the-host). If you expose the docker unix socket to a container, you are giving the container [root access to the host](https://www.lvh.io/posts/dont-expose-the-docker-socket-not-even-to-a-container.html).  

Docker should not be your only defense. You should secure and harden it.

For an understanding of what containers leave exposed, you should read [Understanding and Hardening Linux Containers](https://www.nccgroup.trust/globalassets/our-research/us/whitepapers/2016/april/ncc_group_understanding_hardening_linux_containers-1-1.pdf) by [Aaron Grattafiori](https://twitter.com/dyn___). This is a complete and comprehensive guide to the issues involved with containers, with a plethora of links and footnotes leading on to yet more useful content. The security tips following are useful if you've already hardened containers in the past, but are not a substitute for understanding.

### Security Tips

For greatest security, you want to run Docker inside a virtual machine. This is straight from the Docker Security Team Lead -- [slides](http://www.slideshare.net/jpetazzo/linux-containers-lxc-docker-and-security) / [notes](http://www.projectatomic.io/blog/2014/08/is-it-safe-a-look-at-docker-and-security-from-linuxcon/). Then, run with AppArmor / seccomp / SELinux / grsec etc to [limit the container permissions](http://linux-audit.com/docker-security-best-practices-for-your-vessel-and-containers/). See the [Docker 1.10 security features](https://blog.docker.com/2016/02/docker-engine-1-10-security/) for more details.

Docker image ids are [sensitive information](https://medium.com/@quayio/your-docker-image-ids-are-secrets-and-its-time-you-treated-them-that-way-f55e9f14c1a4) and should not be exposed to the outside world. Treat them like passwords.

See the [Docker Security Cheat Sheet](https://github.com/konstruktoid/Docker/blob/master/Security/CheatSheet.adoc) by [Thomas Sjögren](https://github.com/konstruktoid): some good stuff about container hardening in there.

Check out the [docker bench security script](https://github.com/docker/docker-bench-security), download the [white papers](https://blog.docker.com/2015/05/understanding-docker-security-and-best-practices/) and subscribe to the [mailing lists](https://www.docker.com/docker-security) (unfortunately Docker does not have a unique mailing list, only dev / user).

You should start off by using a kernel with unstable patches for grsecurity / pax compiled in, such as [Alpine Linux](https://en.wikipedia.org/wiki/Alpine_Linux). If you are using grsecurity in production, you should spring for [commercial support](https://grsecurity.net/business_support.php) for the [stable patches](https://grsecurity.net/announce.php), same as you would do for RedHat. It's $200 a month, which is nothing to your devops budget.

Since docker 1.11 you can easily limit the number of active processes running inside a container to prevent fork bombs. This requires a linux kernel >= 4.3 with CGROUP_PIDS=y to be in the kernel configuration.

```
docker run --pids-limit=64
```

Also available since docker 1.11 is the ability to prevent processes from gaining new privileges. This feature have been in the linux kernel since version 3.5. You can read more about it in [this](http://www.projectatomic.io/blog/2016/03/no-new-privs-docker/) blog post.

```
docker run --security-opt=no-new-privileges
```

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

Define and run a user in your Dockerfile so you don't run as root inside the container:

```
RUN groupadd -r user && useradd -r -g user user
USER user
```

### User Namespaces

There's also work on [user namespaces](https://s3hh.wordpress.com/2013/07/19/creating-and-using-containers-without-privilege/) -- it is in 1.10 but is not enabled by default.

To enable user namespaces ("remap the userns") in Ubuntu 15.10, [follow the blog example](https://raesene.github.io/blog/2016/02/04/Docker-User-Namespaces/).

### Security Videos

* [Using Docker Safely](https://youtu.be/04LOuMgNj9U)
* [Securing your applications using Docker](https://youtu.be/KmxOXmPhZbk)
* [Container security: Do containers actually contain?](https://youtu.be/a9lE9Urr6AQ)
* [Linux Containers: Future or Fantasy?](https://www.youtube.com/watch?v=iN6QbszB1R8)

### Security Roadmap

The Docker roadmap talks about [seccomp support](https://github.com/docker/docker/blob/master/ROADMAP.md#11-security).
There is an AppArmor policy generator called [bane](https://github.com/jfrazelle/bane), and they're working on [security profiles](https://github.com/docker/docker/issues/17142).

## Tips

Sources:

* [15 Docker Tips in 5 minutes](http://sssslide.com/speakerdeck.com/bmorearty/15-docker-tips-in-5-minutes)
* [CodeFresh Everyday Hacks Docker](https://codefresh.io/blog/everyday-hacks-docker/)

### Prune

The new [Data Management Commands](https://github.com/docker/docker/pull/26108) have landed as of Docker 1.13:

* `docker system prune`
* `docker volume prune`
* `docker network prune`
* `docker container prune`
* `docker image prune`

### df

`docker system df` presents a summary of the space currently used by different docker objects.

### Heredoc Docker Container

```
docker build -t htop - << EOF
FROM alpine
RUN apk --no-cache add htop
EOF
```

### Last Ids

```
alias dl='docker ps -l -q'
docker run ubuntu echo hello world
docker commit $(dl) helloworld
```

### Commit with command (needs Dockerfile)

```
docker commit -run='{"Cmd":["postgres", "-too -many -opts"]}' $(dl) postgres
```

### Get IP address

```
docker inspect $(dl) | grep -wm1 IPAddress | cut -d '"' -f 4
```

or install [jq](https://stedolan.github.io/jq/):

```
docker inspect $(dl) | jq -r '.[0].NetworkSettings.IPAddress'
```

or using a [go template](https://docs.docker.com/engine/reference/commandline/inspect):

```
docker inspect -f '{{ .NetworkSettings.IPAddress }}' <container_name>
```

or when building an image from Dockerfile, when you want to pass in a build argument:

```
DOCKER_HOST_IP=`ifconfig | grep -E "([0-9]{1,3}\.){3}[0-9]{1,3}" | grep -v 127.0.0.1 | awk '{ print $2 }' | cut -f2 -d: | head -n1`
echo DOCKER_HOST_IP = $DOCKER_HOST_IP
docker build \
  --build-arg ARTIFACTORY_ADDRESS=$DOCKER_HOST_IP
  -t sometag \
  some-directory/
 ```

### Get port mapping

```
docker inspect -f '{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' <containername>
```

### Find containers by regular expression

```
for i in $(docker ps -a | grep "REGEXP_PATTERN" | cut -f1 -d" "); do echo $i; done
```

### Get Environment Settings

```
docker run --rm ubuntu env
```

### Kill running containers

```
docker kill $(docker ps -q)
```

### Delete all containers (force!! running or stopped containers)

```
docker rm -f $(docker ps -qa)
```

### Delete old containers

```
docker ps -a | grep 'weeks ago' | awk '{print $1}' | xargs docker rm
```

### Delete stopped containers

```
docker rm -v $(docker ps -a -q -f status=exited)
```

### Delete containers after stopping

```
docker stop $(docker ps -aq) && docker rm -v $(docker ps -aq)
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

### Slimming down Docker containers

- Cleaning APT in a RUN layer

This should be done in the same layer as other apt commands.
Otherwise, the previous layers still persist the original information and your images will still be fat.

```
RUN {apt commands} \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
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

To check the CPU, memory, and network I/O usage of a single container, you can use:

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

For all containers listed by image:

```
docker ps -a -f ancestor=ubuntu
```

Remove all untagged images
```
docker rmi $(docker images | grep “^” | awk '{split($0,a," "); print a[3]}')
```

Remove container by a regular expression
```
docker ps -a | grep wildfly | awk '{print $1}' | xargs docker rm -f
```
Remove all exited containers
```
docker rm -f $(docker ps -a | grep Exit | awk '{ print $1 }')
```

### Volumes can be files

Be aware that you can mount files as volumes. For example you can inject a configuration file like this:

``` bash
# copy file from container
docker run --rm httpd cat /usr/local/apache2/conf/httpd.conf > httpd.conf

# edit file
vim httpd.conf

# start container with modified configuration
docker run --rm -ti -v "$PWD/httpd.conf:/usr/local/apache2/conf/httpd.conf:ro" -p "80:80" httpd
```

## Contributing

Here's how to contribute to this cheat sheet.

### Open README.md

Click [README.md](https://github.com/wsargent/docker-cheat-sheet/blob/master/README.md) <-- this link

![Click This](../images/click.png)

### Edit Page

![Edit This](../images/edit.png)

### Make Changes and Commit

![Change This](../images/change.png)

![Commit](../images/commit.png)
