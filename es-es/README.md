# Docker Cheat Sheet

**¿Quieres colaborar en este *cheat sheet*? ¡Revisa la sección de [Contribución](#contributing)!**

## Tabla de Contenidos

* [Por qué Docker](#por-qué-Docker)
* [Prerrequisitos](#prerrequisitos)
* [Instalación](#instalación)
* [Contenedores](#contenedores)
* [Imágenes](#imágenes)
* [Redes](#redes)
* [Registry y Repositorios](#Registry-y-Repositorios)
* [Dockerfile](#dockerfile)
* [Capas](#capas)
* [Enlaces](#enlaces)
* [Volúmenes](#volúmenes)
* [Exponiendo Puertos](#Exponiendo-Puertos)
* [Buenas prácticas](#buenas-prácticas)
* [Docker-Compose](#docker-compose)
* [Seguridad](#seguridad)
* [Consejos](#consejos)
* [Contribución](#contribución)

## Por qué Docker

"Con Docker, los desarrolladores (y desarrolladoras) pueden construir cualquier aplicación en cualquier lenguaje usando cualquier herramienta. Las aplicaciones "Dockerizadas" son totalmente portables y pueden funcionar en cualquier lugar: En portátiles con OS X y Windows de compañeros; servidores de QA con Ubuntu en el cloud; y VMs de los datacenters de producción que funcionan con Red Hat.

Los desarrolladores pueden empezar a trabajar rápidamente a partir de cualquiera de las más de 13.000 aplicaciones disponibles en Docker Hub. Docker gestiona y guarda los cambios y dependencias, facilitando el trabajo a los Administradores de Sistemas a la hora de entender cómo funcionan las aplicaciones hechas por los desarrolladores. Y, con Docker Hub, los desarrolladores puedes automatizar el despliegue y compartir el trabajo con colaboradores a través de repositorios públicos o privados.

Docker ayuda a los desarrolladores a trabajar y conseguir aplicaciones de mejor calidad de forma más rápida." -- [Qué es docker](https://www.docker.com/what-docker#copy1)

## Prerrequisitos

De forma opcional, se puede hacer utilizar [Oh My Zsh](https://github.com/ohmyzsh/oh-my-zsh) con el [plugin de Docker](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins#docker) para autocompletar los comandos de Docker.

### Linux

[El requisito mínimo](https://docs.docker.com/engine/installation/binaries/#check-kernel-dependencies) para Docker es utilizar una versión de Kernel (núcleo) posterior a la 3.10.x.

### MacOS

Se requiere de la versión 10.8 “Mountain Lion” o posterior.

### Windows 10

Se debe activar Hyper-V en la BIOS.

En caso de estar disponible, también se debe activar VT-D (Procesadores Intel).

### Windows Server

Como mínimo se requiere la versiín de Windows Server 2016 para instalar Docker y Docker Compose. No obstante, existen limitaciones en esta versión, como a la hora de utilizar redes virtualizadas y contenedores Linux.

Se recomienda utilizar Windows Server 2019 o posteriores.

## Instalación

### Linux

Ejecuta este comando rápido y sencillo proporcionado por Docker:

```sh
curl -sSL https://get.docker.com/ | sh
```

Si no estás dispuesto a ejecutar un shell script que no sabes lo que trae, por favor: revisa las instrucciones de [instalación](https://docs.docker.com/engine/installation/linux/) de tu distribución.

Si eres totalmente nuevo en Docker, te recomendamos seguir esta [serie de tutoriales](https://docs.docker.com/engine/getstarted/).

### macOS

Descarga e instala [Docker Community Edition](https://www.docker.com/community-edition). Si tienes Homebrew-Cask, simplemente escribe `brew cask install docker`. O descarga e instala [Docker Toolbox](https://docs.docker.com/toolbox/overview/). [Docker For Mac](https://docs.docker.com/docker-for-mac/) está bien, pero no está tan pulido como como la instalación de VirtualBox. [Revisa la comparación aquí](https://docs.docker.com/docker-for-mac/docker-toolbox/).

> **NOTA:** Docker Toolbox está deprecado. Deberías utilizar Docker Community Edition, revisa [Docker Toolbox](https://docs.docker.com/toolbox/overview/).

Una vez hayas instalado Docker Community Edition, haz click en el icono de docker en el Launchpad. Entonces inicia un contenedor:

```sh
docker run hello-world
```

¡Y ya estaría! Ya tienes un contenedor de docker funcionando.

Si eres totalmente nuevo en Docker, te recomendamos seguir esta [serie de tutoriales](https://docs.docker.com/engine/getstarted/).

### Windows 10

Las instrucciones para instalar Docker Desktop para Windows se encuentran [aquí](https://hub.docker.com/editions/community/docker-ce-desktop-windows)

Una vez instalado, abre Powershell como administrador y ejecuta:

```powershell
# Muestra la versión de docker instalada:
docker version

# Descarga, crea, y ejecuta 'hello-world':
docker run hello-world
```

Para continuar con esta chuleta, haz click derecho sobre el icono de Docker en la sección de notificaciones (abajo a la derecha), y ves a ajustes. Para montar volúmenes, el disco C:/ debe ser habilitado en ajustes para poder pasar la información a los contenedores (se detalla más adelante en este artículo).

Para alternar entre contenedores Windows y Linux, haz botón derecho en el icono de Docker en la sección de notificaciones y haz click en el botón de cambiar el sistema operativo del contenedor. Hacer esto parará los contenedores que estén funcionando y serán inaccesibles hasta que el SO del contenedor vuelva a cambiar.

Adicionalmente, si tienes WSL (Subsitema de Windows para Linux) o WSL2 instalado en tu equipo, quizás también quieras instalar el Kernel de Linux para Windows. Las instrucciones para ello pueden encontrarse [aquí](https://techcommunity.microsoft.com/t5/windows-dev-appconsult/using-wsl2-in-a-docker-linux-container-on-windows-to-run-a/ba-p/1482133). Esto requiere la característica de Subsistema de Windows para Linux. Esto permitirá que los contenedores sean accesibles desde los sistemas operativos WSL, así como mejorar la eficiencia ejecutando sistemas operaviso WSL en docker. También es preferible utilizar la [terminal de Windows](https://docs.microsoft.com/en-us/windows/terminal/get-started) para esto.

### Windows Server 2016 / 2019

Sigue las instrucciones de Microsoft que puedes encontrar [aquí](https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/deploy-containers-on-server#install-docker)

Si haces uso de la última versión de 2019, prepárate para trabajar solo con powershell, dado que es solo una imágen del núcleo del servidor (sin interfaz de escritorio). Cuando inicies esta máquina, se logueará y mostrará una ventana de powerhell. Se recomienda instalar editores de texto y otras herramientas utilizando [Chocolatey](https://chocolatey.org/install)

Tras instalarlo, funcionarán los siguientes comandos:

```powershell
# Muestra la versión de docker instalada:
docker version

# Descarga, crea, y ejecuta 'hello-world':
docker run hello-world
```

Windows Server 2016 no puede ejecutar imágenes de Linux.

Windows Server Build 2004 es capaz de ejecutar contenedores de Linux y Windows simultáneamente a través del aislamiento de Hyper-V. Cuando se ejecuten los contenedores, utiliza el comando ```isolation=hyperv```, el cual lo aislará utilizando distintas instancias de kernel para cada contenedor.

### Revisar la versión

Es muy importante que siempre conozcas la versión de Docker que estás utilizando en cualquier momento. Es muy útil dado que permite saber las características compatibles con lo que estés ejecutando. Esto también es importante para conocer que contenedores puedes ejecutar de la docker store cuando estés intentando utilizar un contenedor como plantilla. Dicho esto, veamos como recuperar la versión de Docker que está ejecutándose actualmente.

* [`docker version`](https://docs.docker.com/engine/reference/commandline/version/) muestra que versión de docker está ejecutándose.

Recuperar la versión del servidor:

```console
$ docker version --format '{{.Server.Version}}'
1.8.0
```

Puedes volcar la información en un JSON:

```console
$ docker version --format '{{json .}}'
{"Client":{"Version":"1.8.0","ApiVersion":"1.20","GitCommit":"f5bae0a","GoVersion":"go1.4.2","Os":"linux","Arch":"am"}
```

## Contenedores

[Proceso básico del aislamiento en Docker](http://etherealmind.com/basics-docker-containers-hypervisors-coreos/). Los contenedores son a las máquinas virtuales lo que los threads son a los procesos. O puedes verlo como un *chroot* dopado.

### Ciclo de vida

* [`docker create`](https://docs.docker.com/engine/reference/commandline/create) crea un contenedor pero no lo inicia.
* [`docker rename`](https://docs.docker.com/engine/reference/commandline/rename/) permite renombrar el nombre de un contenedor.
* [`docker run`](https://docs.docker.com/engine/reference/commandline/run) crea e inicia un contenedor.
* [`docker rm`](https://docs.docker.com/engine/reference/commandline/rm) elimina un contenedor.
* [`docker update`](https://docs.docker.com/engine/reference/commandline/update/) actualiza los recursos máximos de un contenedor.

Si ejecutas un contenedor sin opciones este se iniciará y detendrá automáticamente, si quieres mantenerlo funcionando puedes utilizar el comando `docker run -td container_id`, esto utilizará la opción `-t`, que habilitará una pseudo-sesión de TTY, y `-d`, que separará el contenedor automáticamente (lo ejecutará en segundo plano y mostrará la ID del contenedor)

Si quieres un contenedor efímero, `docker run --rm` eliminará el contenedor en cuanto se detenga.

Si quieres mapear un directorio del host al contenedor de docker, `docker run -v $HOSTDIR:$DOCKERDIR`. Revisa [Volúmenes](https://github.com/wsargent/docker-cheat-sheet/#volumes).

Si al eliminar el contenedor también quieres borrar los volúmenes asociados, el borrado deberá contener `-v`, por ejemplo: `docker rm -v`.

También existe un [driver de logs](https://docs.docker.com/engine/admin/logging/overview/) disponible para contenedores individuales en Docker 1.10. Para ejecutar docker con un driver de logs personalizado, ejecuta `docker run --log-driver=syslog`.

Otra opciónútil es `docker run --name yourname docker_image` donde especificando la opción `--name` dentro del comando *run*, esto te permitirá iniciar y detener el contenedor utilizando el nombre especificado al crearlo.

### Ejecutando y deteniendo

* [`docker start`](https://docs.docker.com/engine/reference/commandline/start) inicia un contenedor.
* [`docker stop`](https://docs.docker.com/engine/reference/commandline/stop) detiene un contenedor que esté iniciado.
* [`docker restart`](https://docs.docker.com/engine/reference/commandline/restart) detiene y ejecuta un contenedor.
* [`docker pause`](https://docs.docker.com/engine/reference/commandline/pause/) pausa un contenedor que se está ejecutando, congelándolo.
* [`docker unpause`](https://docs.docker.com/engine/reference/commandline/unpause/) reactiva un contenedor.
* [`docker wait`](https://docs.docker.com/engine/reference/commandline/wait) se bloquea hasta que el contenedor se detiene.
* [`docker kill`](https://docs.docker.com/engine/reference/commandline/kill) envía una SIGKILL a un contenedor.
* [`docker attach`](https://docs.docker.com/engine/reference/commandline/attach) se conecta a un contenedor.

Si quieres despegarte de un contenedor, utiliza `Ctrl + p, Ctrl + q`.

Si quieres integrar un contenedor con un [gestor de procesos](https://docs.docker.com/engine/admin/host_integration/), inicia el daemon con `-r=false`, después utiliza `docker start -a`.

Si quieres exponer un puerto del contenedor a través del host, revisa la sección [exponiendo puertos](#exposing-ports).

Las políticas de reinicio en una instancia bloqueada se [explica aquí](http://container42.com/2014/09/30/docker-restart-policies/).

#### Restricciones de CPU

Puedes limitar la CPU, ya sea especificando el porcentáge global de las CPU o definiendo el número de núcleos.

Por ejemplo, puedes especificar la configuración de [`cpu-shares`](https://docs.docker.com/engine/reference/run/#/cpu-share-constraint). Este parámetro es un poco raro -- 1024 significa el 100% de la CPU, por lo que si quieres que el contenedor utilice el 50% de todas las CPU, deberás especificar 512. Revisa <https://docs.docker.com/engine/reference/run/#/cpu-share-constraint> para más información.

```sh
docker run -it -c 512 agileek/cpuset-test
```

Tambiés puedes utilizar únicamente algunos núcleos de la CPU utilizando [`cpuset-cpus`](https://docs.docker.com/engine/reference/run/#/cpuset-constraint). Revisa <https://agileek.github.io/docker/2014/08/06/docker-cpuset/> para más detalles y algunos vídeos guays:

```sh
docker run -it --cpuset-cpus=0,4,6 agileek/cpuset-test
```

Fíjate que Docker puede seguir **viendo** todas las CPU dentro del contenedor -- simplemente no la utiliza entera. Revisa <https://github.com/docker/docker/issues/20770> para más información.

#### Restricciones de memoria

También puedes especificar [restricciones de memoria](https://docs.docker.com/engine/reference/run/#/user-memory-constraints) en Docker

```sh
docker run -it -m 300M ubuntu:14.04 /bin/bash
```

#### Capacidades

Las capacidades de linux se pueden establecer utilizando `cap-add` y `cap-drop`. Revisa <https://docs.docker.com/engine/reference/run/#/runtime-privilege-and-linux-capabilities> para más detalles. Debe usarse para una mejor seguridad.

Para montar un sistema de ficheros basado en FUSE, debes combinar --cap-add con --device:

```sh
docker run --rm -it --cap-add SYS_ADMIN --device /dev/fuse sshfs
```

Para dar acceso a un único dispositivo:

```sh
docker run -it --device=/dev/ttyUSB0 debian bash
```

Para dar acceso a todos los dispositivos:

```sh
docker run -it --privileged -v /dev/bus/usb:/dev/bus/usb debian bash
```

Más información sobre contenedores con privilegios [aquí](
https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)

### Información

* [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps) muestra los contenedores funcionando.
* [`docker logs`](https://docs.docker.com/engine/reference/commandline/logs) recupera los logs del contenedor. (Puedes utilizar un driver personalizado para los logs, pero los logs solo están disponibles para `json-file` y `journald` en la versión 1.10).
* [`docker inspect`](https://docs.docker.com/engine/reference/commandline/inspect) revisa toda la información del contenedor (incluyendo la dirección IP).
* [`docker events`](https://docs.docker.com/engine/reference/commandline/events) recupera los eventos del contenedor.
* [`docker port`](https://docs.docker.com/engine/reference/commandline/port) muestra los puertos abiertos al exterior del contenedor.
* [`docker top`](https://docs.docker.com/engine/reference/commandline/top) muestra los procesos que se están ejecutando en el contenedor,
* [`docker stats`](https://docs.docker.com/engine/reference/commandline/stats) Muestra las estadísticas del uso de recursos del contenedor.
* [`docker diff`](https://docs.docker.com/engine/reference/commandline/diff) Muestra los archivos que han cambiado en el sistema de ficheros del contenedor.

`docker ps -a` muestra todos los contenedores: que están funcionados o parados.

`docker stats --all` lista todos los contenedores, por defecto solo los que están funcionando.

### Import / Export

* [`docker cp`](https://docs.docker.com/engine/reference/commandline/cp) copia los ficheros y carpetas de un contenedor al sistema de ficheros local.
* [`docker export`](https://docs.docker.com/engine/reference/commandline/export) vuelca el sistema de ficheros de un contenedor como fichero .tar en el STDOUT.

### Ejecuntando comandos

* [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec) ejecuta un comando en el contenedor.

Para entrar a un contenedor que está funcionando, acopla un nuevo proceso de terminal al contenedor usando: `docker exec -it <ID/Nombre del contenedor> /bin/bash`.

## Imágenes


Las imágenes simplemente son [plantillas para contenedores de docker](https://docs.docker.com/engine/understanding-docker/#how-does-a-docker-image-work).

### Ciclo de vida

* [`docker images`](https://docs.docker.com/engine/reference/commandline/images) muestra todas las imágenes.
* [`docker import`](https://docs.docker.com/engine/reference/commandline/import) crea una imágen a partir de un fichero .tar.
* [`docker build`](https://docs.docker.com/engine/reference/commandline/build) crea una imágen a partir de un Dockerfile.
* [`docker commit`](https://docs.docker.com/engine/reference/commandline/commit) crea una imágen a partir de un contenedor, deteniéndolo temporalmente si está funcionando.
* [`docker rmi`](https://docs.docker.com/engine/reference/commandline/rmi) elimina una imágen.
* [`docker load`](https://docs.docker.com/engine/reference/commandline/load) carga una imágen a partir de un fichero .tar pasado como STDIN, incluyendo imágenes y etiquetas.
* [`docker save`](https://docs.docker.com/engine/reference/commandline/save) guarda una imágen en un fichero .tar pasado como STDOUT con todas las capas superiores, etiquetas y versiones.

### Información

* [`docker history`](https://docs.docker.com/engine/reference/commandline/history) muestra el historial de una imágen.
* [`docker tag`](https://docs.docker.com/engine/reference/commandline/tag) etiqueta una imágen (local o *registry*).

### Limpiar

Puedes utilizar el comando `docker rmi` para eliminar una imágen específica, pero también existe una herramienta alternativa llamada [docker-gc](https://github.com/spotify/docker-gc) que elimina de forma segura las imágenes que ya no están siendo utilizadas por ningún contenedor. En la versión 1.13 de docker, también existe el comando `docker image prine`, el cual elimina las imágenes que no están siendo utilizadas. Revisa [Prune](#prune)

### Cargar/Guardar una imágen

Carga una imágen a partir de un fichero:

```sh
docker load < my_image.tar.gz
```

Guarda una imágen existente:

```sh
docker save my_image:my_tag | gzip > my_image.tar.gz
```

### Importar/Exportar un contenedor

Importa un contenedor como imágen a partir de un fichero:

```sh
cat my_container.tar.gz | docker import - my_image:my_tag
```

Exporta un contenedor existente:

```sh
docker export my_container | gzip > my_container.tar.gz
```

### Diferencia entre cargar y guardar una imágen e importar y exportar un contenedor como imágen

Cargar una imágen utilizando el comando `load` crea una nueva imágen incluyeno su historial.
Importar un contenedor como imágen utilizando el comando `import` crea una nueva imágen excluyendo el historial, lo que se traduce en una imágen más ligera comparada con cargarla.

## Redes

Docker tiene la característica de [Redes](https://docs.docker.com/engine/userguide/networking/). Docker automáticamente crea tres interficies de red al instalarlo (puente, host, nula). Por defecto, cuando se lanza un nuevo contenedor es añadido la red puente. Para habilitar la comunicación entre varios contenedores puedes crear una nueva red y lanzar los contenedores en ella. Esto permite a los contenedores comunicarse entre ellos y aislarese de los contenedores que no están conectados a su misma red. Además, esto permite mapear nombres de contenedores a sus direcciones IP. Revisa *[working with networks](https://docs.docker.com/engine/userguide/networking/work-with-networks/)* para más información.

### Ciclo de vida

* [`docker network create`](https://docs.docker.com/engine/reference/commandline/network_create/) NAME Crea una nueva red (por defecto de tipo puente).
* [`docker network rm`](https://docs.docker.com/engine/reference/commandline/network_rm/) NAME Elimina una o más redes indicando el nombre o el identificador. No pueden haber contenedores conectados a la red al eliminarla.

### Info

* [`docker network ls`](https://docs.docker.com/engine/reference/commandline/network_ls/) Lista las redes creadas.
* [`docker network inspect`](https://docs.docker.com/engine/reference/commandline/network_inspect/) NAME Muestra información detallada de una o más redes.

### Connection

* [`docker network connect`](https://docs.docker.com/engine/reference/commandline/network_connect/) NETWORK CONTAINER Conecta un contenedor a una red.
* [`docker network disconnect`](https://docs.docker.com/engine/reference/commandline/network_disconnect/) NETWORK CONTAINER Desconecta un contenedor de una red.

Puedes especificar una [ip específica a un contenedor](https://blog.jessfraz.com/post/ips-for-all-the-things/):

```sh
# crea una nueva red puente con la subnet y puerta de enlace específicada
docker network create --subnet 203.0.113.0/24 --gateway 203.0.113.254 iptastic

# ejecuta un contenedor de nginx con al ip especificada en la red iptastic
$ docker run --rm -it --net iptastic --ip 203.0.113.2 nginx

# curl hacia la ip desde cualquier otro lugar (dando por hecho que es una ip pública hehe)
$ curl 203.0.113.2
```

## Registry y Repositorios

(Nota de traducción: Registry sería traducido como Regitro, pero nadie le llama así en el mundo real, así que...)

Un repositorio es una colección *alojada* de imágenes enlazadas que unidas crean el sistema de ficheros para un contenedor.

Un Registry es un *alojamiento* -- un servidor que almacena repositorios y provee de una API HTTP para [gestionar la actualización y descarga de repositorios](https://docs.docker.com/engine/tutorials/dockerrepos/).

Docker.com posee su propio [índice](https://hub.docker.com) como un Registry centralizado que contiene un gran número de repositorios. Dicho esto, aclarar que el docker Registry [no hace un buen trabajo verificando imágenes](https://titanous.com/posts/docker-insecurity), por lo que quizás deberías evitarlo si te preocupa la seguridad.

* [`docker login`](https://docs.docker.com/engine/reference/commandline/login) para loguear en un registry.
* [`docker logout`](https://docs.docker.com/engine/reference/commandline/logout) para desloguear de un registry.
* [`docker search`](https://docs.docker.com/engine/reference/commandline/search) busca en el registry por una imágen.
* [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull) recupera una imágen del registry a local.
* [`docker push`](https://docs.docker.com/engine/reference/commandline/push) sube una imágen local al registry.

### Ejecutar un registry local

Puedes ejecutar un registry local utilizando el proyecto [distribución de docker](https://github.com/docker/distribution) y revisando las instrucciones de como realizar el [deploy local](https://github.com/docker/docker.github.io/blob/master/registry/deploying.md)

Adicionalmente revisa la [mailing list](https://groups.google.com/a/dockerproject.org/forum/#!forum/distribution)

## Dockerfile

[El archivo de configuración](https://docs.docker.com/engine/reference/builder/). Configura un contenedor de docker al ejecutar `docker build` en el. Mucho más preferible a `docker commit`.

Aquí tienes varios editores de textos comunes y módulos de resaltado de sintaxis que puedes usar para crear Dockerfiles:

* Si utilizas [jEdit](http://jedit.org), puedes hacer uso del módulo de resaltado de sintaxis para [Dockerfile](https://github.com/wsargent/jedit-docker-mode).
* [Sublime Text 2](https://packagecontrol.io/packages/Dockerfile%20Syntax%20Highlighting)
* [Atom](https://atom.io/packages/language-docker)
* [Vim](https://github.com/ekalinin/Dockerfile.vim)
* [Emacs](https://github.com/spotify/dockerfile-mode)
* [TextMate](https://github.com/docker/docker/tree/master/contrib/syntax/textmate)
* [VS Code](https://github.com/Microsoft/vscode-docker)
* Revisa [Docker meets the IDE](https://domeide.github.io/)

### Instructions

* [.dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)
* [FROM](https://docs.docker.com/engine/reference/builder/#from) utiliza una imágen de base para las siguientes instrucciones.
* [MAINTAINER (deprecated - use LABEL instead)](https://docs.docker.com/engine/reference/builder/#maintainer-deprecated) especifica el autor que ha generado las imágenes.
* [RUN](https://docs.docker.com/engine/reference/builder/#run) ejecuta cualquier comando en una nueva capa de la imágen y guarda el estado resultante.
* [CMD](https://docs.docker.com/engine/reference/builder/#cmd) proporcionar valores predeterminados para un contenedor en ejecución.
* [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) informa a Docker que el contenedor estará escuchando los puertos especificados mientras se ejecute. NOTA: no hace que los puertos sean accesibles.
* [ENV](https://docs.docker.com/engine/reference/builder/#env) define una variable de entorno.
* [ADD](https://docs.docker.com/engine/reference/builder/#add) copia nuevos ficheros, directorios o archivos remotos al contenedor. Invalida cachés. Procura evitar usar `ADD` e intenta utilizar `COPY` en su lugar.
* [COPY](https://docs.docker.com/engine/reference/builder/#copy) copia nuevos ficheros o directorios al contenedor. Por defecto los copia como *root* independientemente de la configuración de USER/WORKDIR. Utiliza `--chown=<user>:<group>` para cambiar el dueño. (Lo mismo aplica a `ADD`).
* [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) configura un contenedor que funcionará como ejecutable.
* [VOLUME](https://docs.docker.com/engine/reference/builder/#volume) crea un punto de montaje para volúmenes externos u otros contenedores.
* [USER](https://docs.docker.com/engine/reference/builder/#user) especifica el usuario que ejecutará los próximos comandos de tipo RUN / CMD / ENTRYPOINTS.
* [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) especifica el directorio de trabajo.
* [ARG](https://docs.docker.com/engine/reference/builder/#arg) define una variable que estará disponible durante el build.
* [ONBUILD](https://docs.docker.com/engine/reference/builder/#onbuild) añade una instrucción que será lanzada cuando la imágen sea utilizada como base de otro build.
* [STOPSIGNAL](https://docs.docker.com/engine/reference/builder/#stopsignal) define la señal que será enviada al contenedor para detenerse.
* [LABEL](https://docs.docker.com/config/labels-custom-metadata/) aplica metadatos de clave/valor para las imágenes, contenedores o daemons (servicios).
* [SHELL](https://docs.docker.com/engine/reference/builder/#shell) reemplaza la shell por defecto que es utilizada por Docker para ejecutar los comandos.
* [HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) indica a docker como probar el contenedor para revisar que sigue funcionando.

### Tutorial

* [Flux7's Dockerfile Tutorial](https://www.flux7.com/tutorial/docker-tutorial-series-part-3-automation-is-the-word-using-dockerfile/)

### Examples

* [Ejemplos](https://docs.docker.com/engine/reference/builder/#dockerfile-examples)
* [Buenas prácticas para escribir Dockerfiles](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)
* [Michael Crosby](http://crosbymichael.com/) tiene más consejos de [buenas prácticas para Dockerfile](http://crosbymichael.com/dockerfile-best-practices.html) / [toma 2](http://crosbymichael.com/dockerfile-best-practices-take-2.html).
* [Construyendo buenas imágenes de Docker](http://jonathan.bergknoff.com/journal/building-good-docker-images) / [Construyendo mejores imágenes de Docker](http://jonathan.bergknoff.com/journal/building-better-docker-images)
* [Gestionando la configuración de un contenedor utilizando metadatos](https://speakerdeck.com/garethr/managing-container-configuration-with-metadata)
* [Cómo escribir excelentes Dockerfiles](https://rock-it.pl/how-to-write-excellent-dockerfiles/)

## Capas

Las veriones de los sitemas de ficheros en docker se basan en capas. Son similares a los [git commits o conjuntos de cambios para sistemas de ficheros](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/).

## Enlaces

Los enlaces definen como los contenedores de Docker se comunican entre ellos [mediante puertos TCP/IP](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/). [Atlassian](https://blogs.atlassian.com/2013/11/docker-all-the-things-at-atlassian-automation-and-wiring/) lo explica con ejemplos. También puedes resolver [enlaces con nombres de equipo](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/#/updating-the-etchosts-file).

Esto ha sido parcialmente deprecado por [redes definidas por los usuarios](https://docs.docker.com/network/).

NOTA: Si ÚNICAMENTE quieres que los contenedores se comuniquen mediante enlaces, inicia el servicio de Docker con `-icc=false` para desactivar la comunicación entre procesos.

Si tienes un contenedor con el nombre CONTAINER (especificado vía `docker run --name CONTAINER`) y, en el Dockerfile, explones un puerto:

```
EXPOSE 1337
```

Y entonces creas otro contenedor llamado LINKED de la forma:

```sh
docker run -d --link CONTAINER:ALIAS --name LINKED user/wordpress
```

Entonces los puertos expuertos y alias de CONTAINER se mostrarán en LINKED con las siguientes variables de entorno:
Then the exposed ports and aliases of CONTAINER will show up in LINKED with the following environment variables:

```sh
$ALIAS_PORT_1337_TCP_PORT
$ALIAS_PORT_1337_TCP_ADDR
```

Y te puedes conectar a él de esta forma.

Para eliminar los enlaces, utiliza `docker rm --link'.

Generalmente, enlazar mediante servicios de docker es un subgrupo de "descrubrimiento de servicios", un gran problema si tienes pensado utilizar Docker para escalar en producción. Por favor, lee [The Docker Ecosystem: Service Discovery and Distributed Configuration Stores](https://www.digitalocean.com/community/tutorials/the-docker-ecosystem-service-discovery-and-distributed-configuration-stores) para más información.

## Volúmenes

Los volúmenes de Docker son [sitemas de archivos flotantes](https://docs.docker.com/engine/tutorials/dockervolumes/). Estos no se conectan a un contenedor en particular. Puedes utilizar los volúmenes montados de [contenedores de únicamente datos](https://medium.com/@ramangupta/). A partir de Docker 1.9.0, Docker ha nombrado volúmenes que reemplazan los contenedores de solo datos. Considere usar volúmenes con nombre para implementarlo en lugar de contenedores de datos.


### Ciclo de vida

* [`docker volume create`](https://docs.docker.com/engine/reference/commandline/volume_create/)
* [`docker volume rm`](https://docs.docker.com/engine/reference/commandline/volume_rm/)

### Información

* [`docker volume ls`](https://docs.docker.com/engine/reference/commandline/volume_ls/)
* [`docker volume inspect`](https://docs.docker.com/engine/reference/commandline/volume_inspect/)

Los volúmenes son útiles en situaciones donde no puedes utilizar enlaces (los cuales son sólo TCP/IP). Por ejemplo, si necesitases tener dos instancias de docker comunicándose dejando datos en el sistema de ficheros.

Puedes montarlos en distintos contenedores de docker a la vez, utilizando `docker run --volumes-from`.

Dadp que los volúmenes están aislados del sistema de ficheros, estos también son utilizados para almacenar el estado a partir de cálculos de contenedores temporales. Exacto, puedes tener un contenedor sin estado y temporal ejecutándose a partir de una receta,  destruírlo, y entonecs tener otra instancia temporal que pueda recuperar lo que ha dejado atrás el primer contenedor.

Revisa [volúmenes avanzados](http://crosbymichael.com/advanced-docker-volumes.html) para más información. [Container42](http://container42.com/2014/11/03/docker-indepth-volumes/) también es de utilidad.

Puedes [mapear directorios de MacOS como volúmenes de Docker](https://docs.docker.com/engine/tutorials/dockervolumes/#mount-a-host-directory-as-a-data-volume):

```sh
docker run -v /Users/wsargent/myapp/src:/src
```

Puedes utilizar un volúmen NFS remoto si [eres valiente](https://docs.docker.com/engine/tutorials/dockervolumes/#/mount-a-shared-storage-volume-as-a-data-volume)

También puedes plantearme utilizar contenedores de solo datos como se describen [aquí](http://container42.com/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/) para tener portabilidad de datos.

Ten en cuenta que puedes montar [archivos como volúmenes](#volumes-can-be-files).

## Exponiendo puertos

Exponer puertos de entrada a través de un contenedor es [complejo pero factible](https://docs.docker.com/engine/reference/run/#expose-incoming-ports).

Puede hacerse a través del mapeo de puertos del contenedor hacia el host (utilizando únicamente la interficie de localhost) mediante el uso de `p`:

```sh
docker run -p 127.0.0.1:$HOSTPORT:$CONTAINERPORT \
  --name CONTAINER \
  -t algunaimágen
```
Puedes decirle a docker que el contenedor escucha en el puerto especificado utilizando [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose):

```Dockerfile
EXPOSE <CONTAINERPORT>
```

Nótese que `EXPOSE` no expone el puerto por si mismo - solo `-p` lo hace.

Para exponer el puerto de un contenedor en localhost, ejecuta:

```sh
iptables -t nat -A DOCKER -p tcp --dport <LOCALHOSTPORT> -j DNAT --to-destination <CONTAINERIP>:<PORT>
```

Si estás ejecutando Docker en Virtualbox, también necesitarás hacer forward del puerto, utilizando [forwarded_port] (https://docs.vagrantup.com/v2/networking/forwarded_ports.html). Define un rango de puertos en tu Vagrantfile de la siguiente manera para mapearlos dinámicamente:

```
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  ...

  (49000..49900).each do |port|
    config.vm.network :forwarded_port, :host => port, :guest => port
  end

  ...
end
```

Si te olvidas de los puertos que has mapeado, utiliza `docker port` para mostrarlo:

```sh
docker port CONTAINER $CONTAINERPORT
```

## Buenas prácticas

Aquí tienes algunas buenas prácticas de Docker y algunas batallitas:

* [The Rabbit Hole of Using Docker in Automated Tests](http://gregoryszorc.com/blog/2014/10/16/the-rabbit-hole-of-using-docker-in-automated-tests/)
* [Bridget Kromhout](https://twitter.com/bridgetkromhout) has a useful blog post on [running Docker in production](http://sysadvent.blogspot.co.uk/2014/12/day-1-docker-in-production-reality-not.html) at Dramafever.
* También tienes buenas prácticas en este [post](http://developers.lyst.com/devops/2014/12/08/docker/) de Lyst.
* [Building a Development Environment With Docker](https://tersesystems.com/2013/11/20/building-a-development-environment-with-docker/)
* [Discourse in a Docker Container](https://samsaffron.com/archive/2013/11/07/discourse-in-a-docker-container)

## Docker-Compose

Compose es una herramienta para definir y ejecutar varias aplicaciones en contenedores de Docker. Con Compose, utilizas un archivo YAML para configurar tus servicios. Entonces, con un único comando, creas e inicias todos los servicios desde tus configuraciones. Para aprender más sobre las características de Compose, revisa la [lista de características](https://docs.docker.com/compose/overview/#features).

Utilizando el siguiente comando puedes iniciar tu aplicación:

```sh
docker-compose -f <docker-compose-file> up
```

También puedes ejecutar docker-compose en segundo plano utilizando el parámentro -d, entonces podrás detenerlo cuando lo necesitas con el comando:

```sh
docker-compose stop
```

Puedes tirarlo todo, eliminando los contenedores completamente, con el comando down. Utiliza el parámetro `--volumes` para también eliminar los volúmenes de datos.

## Seguridad

Aquí te dejamos algunso consejos de seguridad sobre como funciona Docker. La página de Docker de [seguridad]](https://docs.docker.com/engine/security/security/) lo explica en más detalle.

Lo primero: Docker se ejecuta como root. Si estás en el grupo `docker`, [tienes acceso root](https://web.archive.org/web/20161226211755/http://reventlov.com/advisories/using-the-docker-command-to-root-the-host). Si expones el socket de unix de docker a un contenedor, le estás dando al contenedor acceso [root a la máquina host](https://www.lvh.io/posts/dont-expose-the-docker-socket-not-even-to-a-container/).


Docker no debería ser tu única defensa. Deberías asegurarlo y protejerlo.

Para entender como se exponen los contenedores, deberías leer [Entendiendo y endureciendo Contenedores Linux](https://www.nccgroup.trust/globalassets/our-research/us/whitepapers/2016/april/ncc_group_understanding_hardening_linux_containers-1-1.pdf) de [Aaron Grattafiori](https://twitter.com/dyn___). Esta es una guía completa y entendible sobre los riesgos relacionados con los contenedores, con una gran cantidad de enlaces y notas a pié de página. Los siguientes consejos de seguridad son útiles si ya has protejido tus contenedores en el pasado, pero no son substitutos a entenderlo. 

### Consejos de seguridad

Para mayor seguridad, puedes ejecutar Docker dentro de una máquina virtual. Esto es un consejo del Jefe del Equipo de Seguridad de Docker -- [diapositivas](http://www.slideshare.net/jpetazzo/linux-containers-lxc-docker-and-security) / [notas](http://www.projectatomic.io/blog/2014/08/is-it-safe-a-look-at-docker-and-security-from-linuxcon/). Entonces, ejecutalo con AppArmor / seccomp / SELinux /grsec etc. para [limitar los permisos del contenedor](http://linux-audit.com/docker-security-best-practices-for-your-vessel-and-containers/). Revisa [las características de seguridad de Docker 1.10](https://blog.docker.com/2016/02/docker-engine-1-10-security/) para más detalles.

Los identificadores de las imágenes de Docker son [información sensible](https://medium.com/@quayio/your-docker-image-ids-are-secrets-and-its-time-you-treated-them-that-way-f55e9f14c1a4) y no deberían exponerse al mundo exterior. Trátalos como contraseñas.

Revisa la [Chuleta de Seguridad de Docker](https://github.com/konstruktoid/Docker/blob/master/Security/CheatSheet.adoc) de [Thomas Sjögren](https://github.com/konstruktoid): ahí podrás encontrar buenos consejos sobre como protejerse.

Revisa el [script de seguridad de Docker Bench](https://github.com/docker/docker-bench-security).


[Las 10 Mejores Prácticas de Seguridad para Imágenes de Docker](https://snyk.io/blog/10-docker-image-security-best-practices/) de Snyk

Puedes empezar utilizando un Kernel con parches inestables de *grsecurity* / *pax* compilados, como [Alpine Linux](https://en.wikipedia.org/wiki/Alpine_Linux). Si haces uso de *grsecurity* en producción, deberías buscar [soporte comercial](https://grsecurity.net/business_support.php) para los [parches estables](https://grsecurity.net/announce.php), de la misma forma que deberías hacer para RedHat. Son unos 200$ al mes, lo cual es insignificante para el presupuesto de devops.

A partir de Docker 1.11, puedes limitar fácilmente el número de procesos que se ejecutan en un contenedor para evitar *fork bombs*. Esto requiere utilizar un Kernel de Linux >= 4.3 con CGROUP_PIDS=y en la configuración del kernel.

```sb
docker run --pids-limit=64
```

A partir de Docker 1.11 también está disponible la posibilidad de evitar que los procesos ganen nuevos privilegios. Esta característica está en el Kernel de Linux desde la versión 3.5. Puedes leer más al respecto en [este](http://www.projectatomic.io/blog/2016/03/no-new-privs-docker/) blog.

```sh
docker run --security-opt=no-new-privileges
```

De la [Chuleta de Seguridad de Docker](http://container-solutions.com/content/uploads/2015/06/15.06.15_DockerCheatSheet_A2.pdf) (es un PDF y es un poco complejo de usar, así que mejor copia de abajo) de [Container Solutions](http://container-solutions.com/is-docker-safe-for-production/):

Desactiva la comunicación interproceso con:

```sh
docker -d --icc=false --iptables
```

Establece que el contenedor sea solo lectura:

```sh
docker run --read-only
```

Verifica las imágenes con un *hashsum*:

```sh
docker pull debian@sha256:a25306f3850e1bd44541976aa7b5fd0a29be
```

Establece el volúmen como solo lectura:

```sh
docker run -v $(pwd)/secrets:/secrets:ro debian
```

Crea y utiliza un usiario en el Dockerfile para evitar ejecutar como root dentro del contenedor: 

```Dockerfile
RUN groupadd -r user && useradd -r -g user user
USER user
```

### Espacio de Nombres del Usuario (*User Namespaces*)

También hay que trabajar con los [espacios de nombres de usuario](https://s3hh.wordpress.com/2013/07/19/creating-and-using-containers-without-privilege/) -- disponibles en la 1.10, pero no activados por defecto.

Para activar esta característica ("reasignar los usuarios") en ubuntu 15.10, [sigue este ejemplo](https://raesene.github.io/blog/2016/02/04/Docker-User-Namespaces/).

### Videos de Seguridad

* [Using Docker Safely](https://youtu.be/04LOuMgNj9U)
* [Securing your applications using Docker](https://youtu.be/KmxOXmPhZbk)
* [Container security: Do containers actually contain?](https://youtu.be/a9lE9Urr6AQ)
* [Linux Containers: Future or Fantasy?](https://www.youtube.com/watch?v=iN6QbszB1R8)

### Ruta de Seguridad

La ruta de Docker habla sobre [el soporte de *seccomp*](https://github.com/docker/docker/blob/master/ROADMAP.md#11-security).

También hay una política de AppArmor llamada [base](https://github.com/jfrazelle/bane), y están trabajando en [perfiles de seguridad](https://github.com/docker/docker/issues/17142)

## Consejos

Fuentes:

* [15 Docker Tips in 5 minutes](http://sssslide.com/speakerdeck.com/bmorearty/15-docker-tips-in-5-minutes)
* [CodeFresh Everyday Hacks Docker](https://codefresh.io/blog/everyday-hacks-docker/)

### Prune

Los nuevos [Comandos de manejo de datos](https://github.com/docker/docker/pull/26108) llegaron a Docker en la versión 1.13

* `docker system prune`
* `docker volume prune`
* `docker network prune`
* `docker container prune`
* `docker image prune`

### df

`docker system df` muestra un resumen del espacio actualmente utilizado por los distintos elementos de Docker.

### Heredoc Docker Container

```sh
docker build -t htop - << EOF
FROM alpine
RUN apk --no-cache add htop
EOF
```

### Últimas IDs

```sh
alias dl='docker ps -l -q'
docker run ubuntu echo hello world
docker commit $(dl) helloworld
```

### Commit con comando (necesita de Dockerfile)

```sh
docker commit -run='{"Cmd":["postgres", "-too -many -opts"]}' $(dl) postgres
```

### Recuperar la dirección IP

```sh
docker inspect $(dl) | grep -wm1 IPAddress | cut -d '"' -f 4
```

O, con [jq](https://stedolan.github.io/jq/) instalado:

```sh
docker inspect $(dl) | jq -r '.[0].NetworkSettings.IPAddress'
```

O, utilizando una [plantilla](https://docs.docker.com/engine/reference/commandline/inspect):

```sh
docker inspect -f '{{ .NetworkSettings.IPAddress }}' <container_name>
```

O, al construir la imágen desde un Dockerfile, cuando quieres pasar argumentos de compilación:

```sh
DOCKER_HOST_IP=`ifconfig | grep -E "([0-9]{1,3}\.){3}[0-9]{1,3}" | grep -v 127.0.0.1 | awk '{ print $2 }' | cut -f2 -d: | head -n1`
echo DOCKER_HOST_IP = $DOCKER_HOST_IP
docker build \
  --build-arg ARTIFACTORY_ADDRESS=$DOCKER_HOST_IP
  -t sometag \
  some-directory/
```

### Recuperar el mapping de puertos

```sh
docker inspect -f '{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' <nombredecontenedor>
```

### Encontrar contenedores mediante expresiones regulares

```sh
for i in $(docker ps -a | grep "REGEXP_PATTERN" | cut -f1 -d" "); do echo $i; done
```

### Recuperar la configuraciín del entorno

```sh
docker run --rm ubuntu env
```

### Detener los contenedores en funcionamiento

```sh
docker kill $(docker ps -q)
```

### Eliminar todos los contenedores (¡FORZANDO! Los borrará estén funcionando o parados)

```sh
docker rm -f $(docker ps -qa)
```

### Eliminar los viejos contenedores

```sh
docker ps -a | grep 'weeks ago' | awk '{print $1}' | xargs docker rm
```

### Eliminar los contenedores detenidos

```sh
docker rm -v $(docker ps -a -q -f status=exited)
```

### Eliminar los contenedores después de pararlos

```sh
docker stop $(docker ps -aq) && docker rm -v $(docker ps -aq)
```

### Eliminar las imágenes colgadas

```sh
docker rmi $(docker images -q -f dangling=true)
```

### Eliminar todas las imágenes

```sh
docker rmi $(docker images -q)
```

### Eliminar los volúmenes colgados

Como en Docker 1.9:

```sh
docker volume rm $(docker volume ls -q -f dangling=true)
```

En 1.90, el filtro `dangling=false` _no_ funciona - es ignorado y mostrará todos los volúmenes

### Mostrar las dependencias de las imágenes

```sh
docker images -viz | dot -Tpng -o docker.png
```

### Reducir el tamaño de los contenedores Docker

- Limpiar el APT en una capa `RUN` - Esto debería hacerse en la misma capa que los otros comandos `apt`. Sino, las capas previas seguirán teniendo la información original y la imágen seguirá siendo pesada.
    ```Dockerfile
    RUN {apt commands} \
      && apt-get clean \
      && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
    ```
- Aplanar una imágen
    ```sh
    ID=$(docker run -d image-name /bin/bash)
    docker export $ID | docker import – flat-image-name
    ```
- Para las copias de seguridad
    ```sh
    ID=$(docker run -d image-name /bin/bash)
    (docker export $ID | gzip -c > image.tgz)
    gzip -dc image.tgz | docker import - flat-image-name
    ```

### Monitorizar los recursos del sistema utilizados por los contenedores en funcionamiento

Para revisar el uso de CPU, memoria y E/S de red para un solo contenedor, puedes utilizar:

```sh
docker stats <container>
```

Para todos los contenedores listados por ID:

```sh
docker stats $(docker ps -q)
```

Para todos los contenedores listados por nombre:

```sh
docker stats $(docker ps --format '{{.Names}}')
```

Para todos los contenedores listados por imágen:

```sh
docker ps -a -f ancestor=ubuntu
```

Eliminar todas las imágenes sin etiquetas:

```sh
docker rmi $(docker images | grep “^” | awk '{split($0,a," "); print a[3]}')
```

Eliminar contenedores mediante una expresión regular:

```sh
docker ps -a | grep wildfly | awk '{print $1}' | xargs docker rm -f
```

Elimina todos los contenedores en estado "Exit":

```sh
docker rm -f $(docker ps -a | grep Exit | awk '{ print $1 }')
```

### Los volúmenes pueden ser ficheros

Ten encuenta que puedes montar ficheros como volúmenes. Por ejemplo, pueden inyectar un archivo de configuración así:

```sh
# copia el archivo del contenedor
docker run --rm httpd cat /usr/local/apache2/conf/httpd.conf > httpd.conf

# edita el archivo
vim httpd.conf

# inicia el contenedor con la configuracion modificada
docker run --rm -it -v "$PWD/httpd.conf:/usr/local/apache2/conf/httpd.conf:ro" -p "80:80" httpd
```

## Contribución

Aquí tienes como contribuír a esta chuleta.

### Open README.md

hack click en el [README.md](https://github.com/wsargent/docker-cheat-sheet/blob/master/README.md) <-- este link

![Click](../images/click.png)

### Edit Page

![Edit](../images/edit.png)

### Make Changes and Commit

![Cambios](../images/change.png)

![Commit](../images/commit.png)
