# Docker Cheat Sheet

**Deseja melhorar este *cheat sheet*? Veja a seção de [Contribuição](#contribution)**

## Conteúdo

* [Porque usar docker](#why-docker)
* [Pré-requisitos](#prerequisites)
* [Instalação](#installation)
* [Containers](#containers)
* [Imagens](#images)
* [Redes](#networks)
* [Registro e Repositório](#registry--repository)
* [Dockerfile](#dockerfile)
* [Camadas](#layers)
* [Links](#links)
* [Volumes](#volumes)
* [Expondo portas](#exposing-ports)
* [Boas praticas](#best-practices)
* [Docker-Compose](#docker-compose)
* [Segurança](#security)
* [Dicas](#tips)
* [Contribuição](#contributing)

## Porque usar Docker

"Com Docker, desenvolvedores podem construir qualquer app em qualquer linguagem usando quanquer conjunto de ferramentas. Apps *Dockerizados* são completamente portáveis e podem rodar em qualquer lugar -- OS X e Windows laptops, servidores QA rodando Ubuntu na nuvem e *data centers* em produção rodando Red Hat em Máquinas Virtuais.

Desenvolverodes podem iniciar a utilizá-lo rapidamente com um dos mais de 13.000 apps disponíveis no Docker hub. Docker gerencia e rastreia alterações e dependências, tornando mais fácil para *sysadmins* o entendimento do funcionamento de apps construido pelos desenvolvedores. Além disso, com Docker Hub, desenvolvedores podem automatizar o pipeline de *build* e compartilhar *artifacts* com colaboradores através de repositórios públicos ou privados.

Docker auxilia desenvilvedores a construir e entregar aplicações de alta qualidade de maneira mais rápida." -- [O que é Docker (*What is Docker*)](https://www.docker.com/what-docker#copy1)

## Pré-requisitos

Eu utilizo [Oh My Zsh](https://github.com/ohmyzsh/oh-my-zsh) com [Docker plugin](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins#docker) para autocompletar os comandos do docker.

### Linux

O kernel 3.10.x é [o requisito mínimo](https://docs.docker.com/engine/installation/binaries/#check-kernel-dependencies) para o uso do Docker.

### MacOS

É necessário *“Mountain Lion”* 10.8 ou mais recente.

### Windows 10

É necessário que o *Hyper-V* esteja habilitado na BIOS. Além disso, para precessadores intel, o VT-D também precisa estar habilidado caso esteja disponível.

### Windows Server

Windows Server 2016 é a versão mínima necessária para instalar o docker e o docker-compose. Existem limitações nessa versão, como por exemplo múltiplas redes virtuais e *containers* Linux. Windows Server 2019 ou posterior é recomendado.

## Instalação

### Linux

Um simples, fácil e rápido *script* é disponibilizado pelo Docker:

```
curl -sSL https://get.docker.com/ | sh
```

Se você não deseja rodar um *shell script* aleatório de internet, basta acessar as [instruções de instalação](https://docs.docker.com/engine/installation/linux/) para a sua distribuição.

Se você não sabe nada sobre Docker, provavelmente você deveria seguir essa [série de tutoriais](https://docs.docker.com/engine/getstarted/) antes de continuar.

### macOS

Baixe e instale o [*Docker Community Edition*](https://www.docker.com/community-edition). Se você possui o Homebrew-Cask, apenas utilize o comando `brew cask install docker`. Ou baixe e instale o [*Docker Toolbox*](https://docs.docker.com/toolbox/overview/).  [Docker para Mac](https://docs.docker.com/docker-for-mac/) é ok, mas ele ainda não é tão pronto quanto a instalação da VirtualBox. [Veja a comparação](https://docs.docker.com/docker-for-mac/docker-toolbox/).

> **NOTA** *Docker Toolbox* está no estado *legacy*. Você deveria usar o *Docker Community Edition*. Veja o [*Docker Toolbox*](https://docs.docker.com/toolbox/overview/).

Assim que você instalar o *Docker Community Edition*, clique no ícone do Docker no *Launchpad*. Em seguida inicie um *container*:

```
docker run hello-world
```

É isso! Agora você tem um Docker *container* rodando.

Se você não sabe nada sobre Docker, provavelmente você deveria seguir essa [série de tutoriais](https://docs.docker.com/engine/getstarted/) antes de continuar.

### Windows 10

Você encontra instruções para instalar o Docker Desktop para Windows [neste link](https://hub.docker.com/editions/community/docker-ce-desktop-windows).

Uma vez instalado, abra o *powershell* como administrador

```powershell
#Exibe a versão do docker instalado
docker version

#Todos comandos pull, create, e run 'hello-world' em apenas um:
docker run hello-world

```

Para continuar as instruções neste *cheat sheet*, clique com botão direito do mouse no ícone do Docker -- no menu iniciar ou onde quer que seja -- e vá em configurações. Para montar volumes, você precisa habilitar o disco C:/ para que as informaçõe sejam transmitidas para os *containers* (que ainda será explicado neste artigo).

Para trocar entre *containers* Windows e Linux, clique com o botão direito no icone do Docker e, na sequência, clique no botão para trocar sistema operacional dos *containers*. Após fazer isso, todos os *containers* ques estiveram rodando serão desligados e ficaram inacessíveis até que o SO do *container* ser trocado novamente.


Além disso, se você possui WSL ou WSL2 instalado no seu desktop, você pode instalar o Kernel do Linux para Windows. Instruções para executar tal tarefa podem ser encontradas [aqui] [here](https://techcommunity.microsoft.com/t5/windows-dev-appconsult/using-wsl2-in-a-docker-linux-container-on-windows-to-run-a/ba-p/1482133). Atente-se ao fato de que para isso, é necessário o recurso do Subsistema Windows para Linux. Isso permitirá que os *containers* sejam acessados pelos sistemas operacionais WSL, bem como o ganho de eficiêcia da execução dos sistemas operacionais WSL no Docker. Por fim, tamém é preferível o uso do [terminal Windows](https://docs.microsoft.com/en-us/windows/terminal/get-started) para tal tarefa.

### Windows Server 2016 / 2019

Siga as instruções da Microsoft disponíveis [aqui](https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/deploy-containers-on-server#install-docker)


Se estiver usando a última versão de 2019, esteja preparado para trabalhar com o *powershell*, uma vez que esta versão não possui interface desktop. Quando inciar a máquina, ela vai logar e ir direto para um janela *powershell*. É recomendado instalar um editor de texto dentre outras ferramentas utilizando [Chocolatey](https://chocolatey.org/install).

Após a instalação, esses comandos devem funcionar:

```powershell
#Exibe a versão do docker instalado
docker version

#Todos comandos pull, create, e run 'hello-world' em apenas um:
docker run hello-world

```

O Windows Server 2016 não é capar de rodar images Linux.

O Windows Server Build 2004 é capar de rodar *containers* Linux e Windows simultâneamente através do isolamento *Hyper-V*. Quando rodar os *containers*, utilize o comando ```--isolation=hyperv``` que vai isolar o *container* utilizando uma instância de kernel separada.

 
### Checando a versão

É muito importante que você sempre saiba a versão do Docker que você está utilizando. Isso é muito útil porque você vai saber quais *features* são compatíveis com aquilo que você está rodado. Além disso, isso também é importante pois você saberá quais *containers* você deve rodar a partir da *Docker store* quando você estiver tentando usar *containers templates*. Sendo assim, vamos dar um olhar em como saber a versão do Docker que você está rodando no momento.


* [`docker version`](https://docs.docker.com/engine/reference/commandline/version/): mostra a versão do Docker que você está rodando

Obtendo a versão do servidor:

```
$ docker version --format '{{.Server.Version}}'

1.8.0
```

Você também pode fazer um *dump* dos dados em JSON:

```
$ docker version --format '{{json .}}'

{"Client":{"Version":"1.8.0","ApiVersion":"1.20","GitCommit":"f5bae0a","GoVersion":"go1.4.2","Os":"linux","Arch":"am"}
```

## Containers

[O processo básico isolado do Docker](http://etherealmind.com/basics-docker-containers-hypervisors-coreos/). *Containers* são para máquinas virtuais o que *threads* são para processos. Ou você pode imaginá-los como *chroots* com esteróides.

### Ciclo de vida

* [`docker create`](https://docs.docker.com/engine/reference/commandline/create) cria um *container* mas não o inicia.
* [`docker rename`](https://docs.docker.com/engine/reference/commandline/rename/) permite renomear um *container*.
* [`docker run`](https://docs.docker.com/engine/reference/commandline/run) cria e inicia um *container* em uma única operação
* [`docker rm`](https://docs.docker.com/engine/reference/commandline/rm) deleta um *container*
* [`docker update`](https://docs.docker.com/engine/reference/commandline/update/) atualiza os limites de recurso de um *container*.

Normalmente, se você rodar um *container* sem utilizar nenhuma opção ele vai iniciar e parar imediatamente. Se você deseja que ele continue rodando você pode usar o comando `docker run -td <container_id>`. A opção `-t` vai alocar uma sessão pseudo-TTY e o `-d` vai desacomplar o *container* automaticamente (ou seja, vai rodar o *container* em background e imprimir o ID do container).

Se você deseja um container transiente, `docker run --rm` vai remover o container assim que ele parar.

Se você deseja mapear um diretório da máquina *host* para o *container* do Docker, `docker run -v $HOSTDIR:$DOCKERDIR`. Saiba mais em [Volumes](https://github.com/wsargent/docker-cheat-sheet/#volumes).

Se você também deseja remover o  volume associado ao *container*, ao deletar o *container* você deve incluir a opção `-v`, por exemplo, `docker rm -v`.

Também existe o [*logging driver*](https://docs.docker.com/engine/admin/logging/overview/), disponível para *containers* individuais no docker 1.10. Para rodar o docker com um *log driver* customizado (ou seja, para syslog), use `docker run --log-driver=syslog`.

Outra opção muito útil é o `docker run --name <yourname> <docker_image>` porque você pode especificar o `--name` dentro do comando `run` que vai lhe permite iniciar e parar o container através do nome que você especificou quando o criou.


### Iniciando e parando 

* [`docker start`](https://docs.docker.com/engine/reference/commandline/start) inicia um *container*, então ele passa a rodar.
* [`docker stop`](https://docs.docker.com/engine/reference/commandline/stop) para um *container* que esteja rodando.
* [`docker restart`](https://docs.docker.com/engine/reference/commandline/restart) para e inici um *container*.
* [`docker pause`](https://docs.docker.com/engine/reference/commandline/pause/) pausa um *container* que esteja rodando, "congelando" ele da maneira que está.
* [`docker unpause`](https://docs.docker.com/engine/reference/commandline/unpause/) vai despausar um *container* que estava rodando.
* [`docker wait`](https://docs.docker.com/engine/reference/commandline/wait) bloqueia o *container* até que ele seja parado.
* [`docker kill`](https://docs.docker.com/engine/reference/commandline/kill) envia um SIGKILL para um *container* que esteja rodando.
* [`docker attach`](https://docs.docker.com/engine/reference/commandline/attach) vai se conectar a um *container* que esteja rodando.

Se você deseja desacoplar um *container* que esteja rodando, utilize `Ctrl + p, Ctrl + q`. Se você deseja integrar um *container* com o [gerenciador de processos do host](https://docs.docker.com/engine/admin/host_integration/), inicialize o daemon com `-r=false` e depois use `docker start -a`.

Se você deseja expor portas do *container* través do *host*, veja a seção [expondo portas](#exposing-ports).

Políticas de reinicialização para instâncias "crashadas" do docker são [cobridas aqui](http://container42.com/2014/09/30/docker-restart-policies/).

#### Restrições de CPU 

Você pode limitar o uso da CPU, seja usando uma porcentagem de todas as CPUs ou usando *cores* específicos.

Por exemplo, você pode usar a configuração [`cpu-shares`](https://docs.docker.com/engine/reference/run/#/cpu-share-constraint). A configuração é um pouco estranha -- 1024 significa 100% da CPU, então se você quer um container que toma 50% de todos os *cores*, você deve utilizar 512. Veja <https://goldmann.pl/blog/2014/09/11/resource-management-in-docker/#_cpu> para mais:

```
docker run -it -c 512 agileek/cpuset-test
```

Você também pode usar alguns *cores* de uma CPU com o comando [`cpuset-cpus`](https://docs.docker.com/engine/reference/run/#/cpuset-constraint).  Veja <https://agileek.github.io/docker/2014/08/06/docker-cpuset/> para mais detalhes e alguns vídeos bem legais:

```
docker run -it --cpuset-cpus=0,4,6 agileek/cpuset-test
```

Observe que o Docker ainda pode **enxergar** todas as CPUs de dentro do *container* -- ele apenas não está usando todas elas. Veja <https://github.com/docker/docker/issues/20770> para mais detalhes.


#### Restrições de memória

Você também pode setar [restrições de memória](https://docs.docker.com/engine/reference/run/#/user-memory-constraints) no Docker:

```
docker run -it -m 300M ubuntu:14.04 /bin/bash
```

#### *Capabilities*

Linux *capabilities* podem ser setadas utilizand as opções `cap-add` e `cap-drop`. Veja See <https://docs.docker.com/engine/reference/run/#/runtime-privilege-and-linux-capabilities> para mais detalhes. Elas devem ser utilizadas para aumentar a seguraça do sistema.

Para montar um *filesystem* baseado no FUSE, você precisa combinar tanto `--cap-add` quanto `--device`:

```
docker run --rm -it --cap-add SYS_ADMIN --device /dev/fuse sshfs
```

Para dar acesso a um único *device*:

```
docker run -it --device=/dev/ttyUSB0 debian bash
```

Para dar acesso a todos os *devices*:

```
docker run -it --privileged -v /dev/bus/usb:/dev/bus/usb debian bash
```

Para mais informações sobre privilégios em *containers* [clique aqui](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities).

### Info

* [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps) motra os containers que estão rodando.
* [`docker logs`](https://docs.docker.com/engine/reference/commandline/logs) obtém um log dos containers. (Você pode usar um log customizado, mas eles estão disponíveis apenas para `json-file` e `journald` na versão 1.10).
* [`docker inspect`](https://docs.docker.com/engine/reference/commandline/inspect) olha para todas as informações de um container (incluindo o endereço IP).
* [`docker events`](https://docs.docker.com/engine/reference/commandline/events) obtém os eventos de um container.
* [`docker port`](https://docs.docker.com/engine/reference/commandline/port) mostra a porta pública de um container.
* [`docker top`](https://docs.docker.com/engine/reference/commandline/top) mostra os processos rodando dentro de um container.
* [`docker stats`](https://docs.docker.com/engine/reference/commandline/stats) mostra uma estatística dos recursos que o container está utilizando.
* [`docker diff`](https://docs.docker.com/engine/reference/commandline/diff) mostra os arquivos alterados pelo FS de um container.

`docker ps -a` mostra os containers que estão rodando e os que foram parados.

`docker stats --all` mostra uma lista de todos os containers. O padrão é mostrar apenas os que estão rodando.

### Importar / Exportar

* [`docker cp`](https://docs.docker.com/engine/reference/commandline/cp) copia arquivos ou pastas entre o container e o *filesystem* local.
* [`docker export`](https://docs.docker.com/engine/reference/commandline/export) transforma o *filesystem* do container em um fluxo de arquivo *tarball* para STDOUT.


### Executando comandos

* [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec) executa um comando dentro do container.

Por exemplo, para entrar em um container fictício, que esteja rodando, chamado foo, inclua um shell a ele da seguinte maneira: `docker exec -it foo /bin/bash`.

## Imagens

Imagens são apenas [templates de um container docker](https://docs.docker.com/engine/understanding-docker/#how-does-a-docker-image-work).

### Ciclo de vida

* [`docker images`](https://docs.docker.com/engine/reference/commandline/images) mostra todas as imagens.
* [`docker import`](https://docs.docker.com/engine/reference/commandline/import) cria uma imagem a partir de um *tarball*.
* [`docker build`](https://docs.docker.com/engine/reference/commandline/build) cria uma imagem a partir de um Dockerfile.
* [`docker commit`](https://docs.docker.com/engine/reference/commandline/commit) cria uma imagem a partir de um container, pausando ele temporariamente caso ele esteja rodando.
* [`docker rmi`](https://docs.docker.com/engine/reference/commandline/rmi) remove uma imagem.
* [`docker load`](https://docs.docker.com/engine/reference/commandline/load) carrega uma imagem a partir de um arquivo tar no STDIN, incluindo imagess and tags (a partir da versão 0.7).
* [`docker save`](https://docs.docker.com/engine/reference/commandline/save) salva uma imagem em um arquivo tar através do STDOUT com todas as camadas pais, tags e versões (a partir do 0.7).

### Info

* [`docker history`](https://docs.docker.com/engine/reference/commandline/history) mostra o histórico de todas as imagens.
* [`docker tag`](https://docs.docker.com/engine/reference/commandline/tag) dar uma tag a uma imagem (local ou *registry*)


### Fazendo uma limpeza

Você pode utilizar o comando `docker rmi` para remover imagens específicas, porém, existe uma ferramenta chamada [docker-gc](https://github.com/spotify/docker-gc), que de maneira segura, limpa as imagens que não está sendo utilizada por nenhum container. 

 that will safely clean up images that are no longer used by any containers. A partir do docker 1.13, o comando `docker image prune` também está disponível para remover imagens que não estão sendo usadas. Veja a seção [Prune](#prune).

### Carregar/Salvar imagens

Carregue uma imagem a partir do arquivo:
```
docker load < my_image.tar.gz
```

Salve uma imagem existente usando:
```
docker save my_image:my_tag | gzip > my_image.tar.gz
```

### Importar/Exportar containers


Importe um container com uma imagem a partir de um arquivo:
```
cat my_container.tar.gz | docker import - my_image:my_tag
```

Exporte um container existente usando:
```
docker export my_container | gzip > my_container.tar.gz
```

### Diferenças entre carregar uma imagem salva e importar um container exportado como uma imagem

Carregar uma imagem usanfo o comando `load` cria uma nova imagem, incluindo o seu histórico. Importar um container como uma imagem usando o comando `import` cria uma nova imagem excluindo o seu histórico, o que resulta em uma imamgem de tamanho menor do que usando o comando anterior.

## Rede

O Docker possui *features* de [rede](https://docs.docker.com/engine/userguide/networking/). Automaticamente, ele cira 3 interfaces de rede quando você o instala (*bridge*, *host*, *none*). Um novo container é inicializado, por padrão, dentro da rede *bridge*. Para habilitar a comunicação entre multiplos containers, você pode criar uma nova rede e iniciarlizar o mesmo com ela. Isso vai habilitar a comunicação entre os containers dentro dela ao mesmo tempo que os isola dos outros containers que não estejam conectados nesta rede. Além disso, isso permite mapear os nomes dos containers com o seus respectivos endereços IP. Veja [trabalhando com redes](https://docs.docker.com/engine/userguide/networking/work-with-networks/) para mais detalhes.

### Ciclo de vida

* [`docker network create <name>`](https://docs.docker.com/engine/reference/commandline/network_create/)  cria uma nova rede (tipo padrão: *bridge*).
* [`docker network rm <name>`](https://docs.docker.com/engine/reference/commandline/network_rm/) remode uma ou mais redes especificadas pelo nome ou identificador. Nenhum container pode se conectar em uma rede quando deletada.

### Info

* [`docker network ls`](https://docs.docker.com/engine/reference/commandline/network_ls/) lista todas as redes.
* [`docker network inspect <name>`](https://docs.docker.com/engine/reference/commandline/network_inspect/) mostra informações detalhadas de uma ou mais redes.

### Conexão

* [`docker network connect <network> <container>`](https://docs.docker.com/engine/reference/commandline/network_connect/) Conecta um container a uma rede
* [`docker network disconnect <network> <container>`](https://docs.docker.com/engine/reference/commandline/network_disconnect/) Desconecta um container de uma rede

Você pode especificar um [endereço IP para um container](https://blog.jessfraz.com/post/ips-for-all-the-things/):

```
# cria uma nova rede bridge com sua subnet e gateway para seu bloco de endereço IP
docker network create --subnet 203.0.113.0/24 --gateway 203.0.113.254 iptastic

# roda um container nginx com um IP específico para o dado bloco
$ docker run --rm -it --net iptastic --ip 203.0.113.2 nginx

# da um culr no IP a partir de qualquer outro local (assumindo que este seja um IP público)
$ curl 203.0.113.2
```

## Registry & Repositório

Um repositório é uma coleção *hosteada* de imagens com tagas que juntas criam um sistema de arquivo para um container;

Um *registry* é um *host* -- ou seja, um servidor que armazena repositórios e disponibiliza um API HTTP para [gerencias o upload e download dos repositórios](https://docs.docker.com/engine/tutorials/dockerrepos/).

O Docker.com *hostea* seus prórpios [índices](https://hub.docker.com/) em uma central de *registries* qie contém um grande número de repositórios. Sendo assim, essa central [não é muito boa em verificar a procedência das imagens](https://titanous.com/posts/docker-insecurity) e deve ser evitada caso segurança seja algo crítico para você.
 

* [`docker login`](https://docs.docker.com/engine/reference/commandline/login) efetua login em um *registry*.
* [`docker logout`](https://docs.docker.com/engine/reference/commandline/logout) efetua logout de um *registry*.
* [`docker search`](https://docs.docker.com/engine/reference/commandline/search) busca imagens dentro do *registry*.
* [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull) efetua um *pull* de uma imagem do *registry* para sua máquina local.
* [`docker push`](https://docs.docker.com/engine/reference/commandline/push) efetua um *push* de uma imagem para o *registry* a partir da sua máquina local.

### Rodando um *registry* local

Você pode rodar um *registry* local utilizando o projeto de [distribuição docker](https://github.com/docker/distribution) e seguindo as instruções de [*deploy* local](https://github.com/docker/docker.github.io/blob/master/registry/deploying.md).

Além disso, você pode se interessar pela [lista de emails](https://groups.google.com/a/dockerproject.org/forum/#!forum/distribution).

## Dockerfile

[O arquivo de configuração](https://docs.docker.com/engine/reference/builder/). Prepara um container Docker quando você executa o comando `docker build`. A maioria das pessoas preferem este comando do que o `docker commit`.  

Estes são alguns dos editores de texto que dão suporte, em termos de módulos que destacam a sintaxe, para criar Dockerfiles:
* Se você utiliza o [jEdit](http://jedit.org), eu adicionei um módulo para destacar de sintaxe para o [Dockerfile](https://github.com/wsargent/jedit-docker-mode). Sinta-se livre para usar.
* [Sublime Text 2](https://packagecontrol.io/packages/Dockerfile%20Syntax%20Highlighting)
* [Atom](https://atom.io/packages/language-docker)
* [Vim](https://github.com/ekalinin/Dockerfile.vim)
* [Emacs](https://github.com/spotify/dockerfile-mode)
* [TextMate](https://github.com/docker/docker/tree/master/contrib/syntax/textmate)
* [VS Code](https://github.com/Microsoft/vscode-docker)
* Veja também [Docker meets the IDE](https://domeide.github.io/)

### Instruções

* [.dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)
* [FROM](https://docs.docker.com/engine/reference/builder/#from) Prepara a imagem base para as instruções subsequentes.
* [MAINTAINER (depreciado - use a tag LABEL)](https://docs.docker.com/engine/reference/builder/#maintainer-deprecated) Define o autor que gerou a imagem.
* [RUN](https://docs.docker.com/engine/reference/builder/#run) executa qualquer comando em uma nova camada em cima de uma imagem e *comita* o resultado.
* [CMD](https://docs.docker.com/engine/reference/builder/#cmd) fornecer padrões para um container em execução.
* [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) informa o Docker que o container pode escutar uma determinada porta de rede durante o tempo de execução. NOTA: isso não faz com que a porta seja acessível.
* [ENV](https://docs.docker.com/engine/reference/builder/#env) define uma variável de ambiente.
* [ADD](https://docs.docker.com/engine/reference/builder/#add) copia novos arquivos, diretórios, ou arquivos remotos em um container. Invalida cache. Evite usar `ADD` e use o comando `COPY`.
* [COPY](https://docs.docker.com/engine/reference/builder/#copy) copia um novo arquivo ou diretórios para dentro do container. Por padrão copia como root independente das configurações do USER/WORKDIR. Utilize `--chown=<user>:<group>` para poderes de acesso a outros usuários/grupos. (o mesmo é válido para o comando `ADD`.)
* [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) configura um container que vai rodar como um executável.
* [VOLUME](https://docs.docker.com/engine/reference/builder/#volume) cria um ponto de montagem para montar volumes externos ou outros containers.
* [USER](https://docs.docker.com/engine/reference/builder/#user) define o nome de usuário para os seguintes comandos: RUN / CMD / ENTRYPOINT.
* [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) define o diretório de trabalho.
* [ARG](https://docs.docker.com/engine/reference/builder/#arg) define uma variável que existe durante o tempo de execução do *build*.
* [ONBUILD](https://docs.docker.com/engine/reference/builder/#onbuild) adicionar uma instrução alarme que dispara quando a imagem está sendo usada como base para outra *build*.
* [STOPSIGNAL](https://docs.docker.com/engine/reference/builder/#stopsignal) define o sinal de alerta do sistema que vai ser enviado para sair do container.
* [LABEL](https://docs.docker.com/config/labels-custom-metadata/) aplica uma chave/valor para suas imagens, containers, ou *daemons*.
* [SHELL](https://docs.docker.com/engine/reference/builder/#shell) sobrecarrega o shell padrão para rodar os comandos do docker.
* [HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) informa docker como testar o container para testar se tudo está funcionando adequadamente.

### Tutorial

* [Tutorial do Flux7 para o Dockerfile](https://www.flux7.com/tutorial/docker-tutorial-series-part-3-automation-is-the-word-using-dockerfile/)

### Exemplos

* [Exemplos](https://docs.docker.com/engine/reference/builder/#dockerfile-examples)
* [Boas práticas para escrever Dockerfiles](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)
* [Michael Crosby](http://crosbymichael.com/) tem mais algumas [boas práticas para criar Dockerfiles](http://crosbymichael.com/dockerfile-best-practices.html) / [parte 2](http://crosbymichael.com/dockerfile-best-practices-take-2.html).
* [Construindo boas imagens Docker](http://jonathan.bergknoff.com/journal/building-good-docker-images) / [Construindo imagens Dockers ainda melhores](http://jonathan.bergknoff.com/journal/building-better-docker-images)
* [Gerenciando a configuração de um container com metadados](https://speakerdeck.com/garethr/managing-container-configuration-with-metadata)
* [Como escrever excelentes Dockerfiles](https://rock-it.pl/how-to-write-excellent-dockerfiles/)

## Camadas

O versionamento dos arquivos de sistema do docker é feito em camadas. Elas funcionam como [comits no git ou *changesets* para arquivos de sistemas](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/).

## Links

Links são como os containers Docker conversam uns com os outros [através de portas TCP/IP](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/). [Atlassian](https://blogs.atlassian.com/2013/11/docker-all-the-things-at-atlassian-automation-and-wiring/) mostra alguns exemplos funcionais. Você também pode determinar [links pelo hostname](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/#/updating-the-etchosts-file).

Este está depreciado para estender algumas [redes definidas por usuário](https://docs.docker.com/network/).

NOTA: se você deseja que os containers comuniquem uns com os outros apenas utilizando links, inicie o do docker daemon com `-icc=false` para desebilitar os processor de intra-comunicação. 

Se você possui um container com nome CONTAINER (especificado por `docker run --name CONTAINER`) e em um Dockerfile, ele possui uma porta exposta:

```
EXPOSE 1337
```

Então, se nós criarmos qualquer outro container chamado LINKED como este:

```
docker run -d --link CONTAINER:ALIAS --name LINKED user/wordpress
```

Então, as portas expostas e os *aliases* do CONTAINER serão mostrados em LINKED com as seguintes variáveis de ambiente:

```
$ALIAS_PORT_1337_TCP_PORT
$ALIAS_PORT_1337_TCP_ADDR
```

Você pode se conectar nele dessa maneira.

Para deletar os links, utilize o comando `docker rm --link`.

Geralmente, a *linkagem* entre serviços docker é um subconjunto de um "descobrimento de serviço", um grande problema caso você esteja planejando usar Docker para escalar em produção. Você pode se referir ao livro [The Docker Ecosystem: Service Discovery and Distributed Configuration Stores](https://www.digitalocean.com/community/tutorials/the-docker-ecosystem-service-discovery-and-distributed-configuration-stores) para mais informações.

## Volumes

Os volumes no Docker são [arquivos de sistemas flutuantes](https://docs.docker.com/engine/tutorials/dockervolumes/). Eles não possuem uma conexão particular com um container. Você pode usar volumes montados a partir de [container somente de dados](https://medium.com/@ramangupta/why-docker-data-containers-are-good-589b3c6c749e) para fins de portabilidade. A partir do Docker 1.9.0, o Docker passou a nomear volumes que substituem containers apenas de dados. Considere usar volumes nomeados para implementar isso ao invés de containers de dados.

### Ciclo de vidar

* [`docker volume create`](https://docs.docker.com/engine/reference/commandline/volume_create/)
* [`docker volume rm`](https://docs.docker.com/engine/reference/commandline/volume_rm/)

### Informações

* [`docker volume ls`](https://docs.docker.com/engine/reference/commandline/volume_ls/)
* [`docker volume inspect`](https://docs.docker.com/engine/reference/commandline/volume_inspect/)

Volumes são úteis em situações em que você não quer usar links (que são apenas TCP/IP). Por exemplo, se você precisar ter duas instancias docker comunicando através de algo deixado no arquivo de sistema.

Você pode montar vários containers docker de uma vez usando o comando `docker run --volumes-from`.

Como os volumes são sistemas de arquivos isolados, frequentemente, eles são usados para armazenar estados de alguma computação que é transiente no container. Isto é, você pode ter um container sem estado e transiente continuado de ponto que útimo container deixou.

Veja [volumes avançados](http://crosbymichael.com/advanced-docker-volumes.html) para mais detalhes. [Container42](http://container42.com/2014/11/03/docker-indepth-volumes/) também é bem útil.

Você pode [mapear diretórios hosts do MacOS como volumes docker](https://docs.docker.com/engine/tutorials/dockervolumes/#mount-a-host-directory-as-a-data-volume):

```
docker run -v /Users/wsargent/myapp/src:/src
```

Você pode usar columes NFS remotos se você estiver se 
You can use remote NFS volumes if you're [sentindo corajoso](https://docs.docker.com/engine/tutorials/dockervolumes/#/mount-a-shared-storage-volume-as-a-data-volume).

Você também pode considerar rodar containers apenas de dados como descrito [aqui](http://container42.com/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/) para obter uma certa portabilidade de dados.

Saiba que você pode [montar arquivos como volumes](#volumes-can-be-files).

## Exposing ports

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

## Docker-Compose

Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration. To learn more about all the features of Compose, see the [list of features](https://docs.docker.com/compose/overview/#features).

By using the following command you can start up your application:

```
docker-compose -f <docker-compose-file> up
```

You can also run docker-compose in detached mode using -d flag, then you can stop it whenever needed by the following command:

```
docker-compose stop
```

You can bring everything down, removing the containers entirely, with the down command. Pass `--volumes` to also remove the data volume.

## Security

This is where security tips about Docker go. The Docker [security](https://docs.docker.com/engine/security/security/) page goes into more detail.

First things first: Docker runs as root. If you are in the `docker` group, you effectively [have root access](https://web.archive.org/web/20161226211755/http://reventlov.com/advisories/using-the-docker-command-to-root-the-host). If you expose the docker unix socket to a container, you are giving the container [root access to the host](https://www.lvh.io/posts/dont-expose-the-docker-socket-not-even-to-a-container/).

Docker should not be your only defense. You should secure and harden it.

For an understanding of what containers leave exposed, you should read [Understanding and Hardening Linux Containers](https://www.nccgroup.trust/globalassets/our-research/us/whitepapers/2016/april/ncc_group_understanding_hardening_linux_containers-1-1.pdf) by [Aaron Grattafiori](https://twitter.com/dyn___). This is a complete and comprehensive guide to the issues involved with containers, with a plethora of links and footnotes leading on to yet more useful content. The security tips following are useful if you've already hardened containers in the past, but are not a substitute for understanding.

### Security Tips

For greatest security, you want to run Docker inside a virtual machine. This is straight from the Docker Security Team Lead -- [slides](http://www.slideshare.net/jpetazzo/linux-containers-lxc-docker-and-security) / [notes](http://www.projectatomic.io/blog/2014/08/is-it-safe-a-look-at-docker-and-security-from-linuxcon/). Then, run with AppArmor / seccomp / SELinux / grsec etc to [limit the container permissions](http://linux-audit.com/docker-security-best-practices-for-your-vessel-and-containers/). See the [Docker 1.10 security features](https://blog.docker.com/2016/02/docker-engine-1-10-security/) for more details.

Docker image ids are [sensitive information](https://medium.com/@quayio/your-docker-image-ids-are-secrets-and-its-time-you-treated-them-that-way-f55e9f14c1a4) and should not be exposed to the outside world. Treat them like passwords.

See the [Docker Security Cheat Sheet](https://github.com/konstruktoid/Docker/blob/master/Security/CheatSheet.adoc) by [Thomas Sjögren](https://github.com/konstruktoid): some good stuff about container hardening in there.

Check out the [docker bench security script](https://github.com/docker/docker-bench-security), download the [white papers](https://blog.docker.com/2015/05/understanding-docker-security-and-best-practices/).

Snyk's [10 Docker Image Security Best Practices cheat sheet](https://snyk.io/blog/10-docker-image-security-best-practices/)

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

or with [jq](https://stedolan.github.io/jq/) installed:

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

Remove all untagged images:
```
docker rmi $(docker images | grep “^” | awk '{split($0,a," "); print a[3]}')
```

Remove container by a regular expression:
```
docker ps -a | grep wildfly | awk '{print $1}' | xargs docker rm -f
```

Remove all exited containers:
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
docker run --rm -it -v "$PWD/httpd.conf:/usr/local/apache2/conf/httpd.conf:ro" -p "80:80" httpd
```

## Contributing

Here's how to contribute to this cheat sheet.

### Open README.md

Click [README.md](https://github.com/wsargent/docker-cheat-sheet/blob/master/README.md) <-- this link

![Click This](images/click.png)

### Edit Page

![Edit This](images/edit.png)

### Make Changes and Commit

![Change This](images/change.png)

![Commit](images/commit.png)
