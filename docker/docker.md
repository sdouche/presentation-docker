!SLIDE
# Terminologie

Quelques mots clés :

* index : répertoire public
* image : snapshot d'un conteneur
* conteneur : élement manipulable
* run : créer un conteneur (à partir d'une image) et lancer une commande  

!SLIDE
# Installation Ubuntu

```bash
sudo sh -c "curl https://get.docker.io/gpg | apt-key add -"
sudo sh -c "echo deb http://get.docker.io/ubuntu docker main \
            > /etc/apt/sources.list.d/docker.list"
sudo apt-get update
sudo apt-get install lxc-docker
```

*Note* : Docker est écrit en Go, cela veut dire qu'il n'installe qu'un binaire.


!SLIDE
# Lancement de Docker

```bash
$ sudo /usr/bin/docker -d &
```
!SLIDE
# Vérification de l'installation

```bash
$ docker
Usage: docker [OPTIONS] COMMAND [arg...]
A self-sufficient runtime for linux containers.

Commands:
    attach    Attach to a running container
    build     Build a container from a Dockerfile
    commit    Create a new image from a container's changes
    cp        Copy files/folders from the containers filesystem to the host path
    diff      Inspect changes on a container's filesystem
    events    Get real time events from the server
    export    Stream the contents of a container as a tar archive
    history   Show the history of an image
    images    List images
    import    Create a new filesystem image from the contents of a tarball
    info      Display system-wide information
    insert    Insert a file in an image
    inspect   Return low-level information on a container
    kill      Kill a running container
    login     Register or Login to the docker registry server
    logs      Fetch the logs of a container
    port      Lookup the public-facing port which is NAT-ed to PRIVATE_PORT
    top       Lookup the running processes of a container
    ps        List containers
...
 ```

!SLIDE
# Commande search

Rechercher une image avec un mot clé :

```bash
$ sudo docker search ubuntu
Found 25 results matching your query ("ubuntu")
NAME               DESCRIPTION
base               Another general use Ubuntu base image. Tags available are: latest, quantal (12.10)
ubuntu             General use Ubuntu base image. Tags available are: latest, quantal (12.10), precise (12.04)
boxcar/raring      Ubuntu Raring 13.04 suitable for testing vagrant-shell
...
```

!SLIDE
# Commande pull

Récupération d'une image Ubuntu :

```bash
$ sudo docker pull ubuntu
Pulling repository ubuntu
8dbd9e392a96: Download complete
b750fe79269d: Download complete
27cf78414709: Download complete

```

!SLIDE
# Commande images

```bash
$ sudo docker images
REPOSITORY     TAG         ID               CREATED          SIZE
ubuntu         precise     8dbd9e392a96     5 months ago     131.5 MB (virtual 131.5 MB)
ubuntu         quantal     b750fe79269d     6 months ago     24.65 kB (virtual 180.1 MB)


!SLIDE
# Commande run

Lancement d'un shell : 

```bash
sudo docker run -i -t ubuntu /bin/bash
root@419eed6ff306:/# 
```

!SLIDE
# Commande run

Lancer une commande : 

```bash
$ sudo docker run ubuntu /bin/echo hello world
hello world
```

!SLIDE
# Commande run

Lancer une application :

```bash
$ ID=$(sudo docker run -d ubuntu /bin/sh \
                 -c "while true; do echo hello world; sleep 1; done")
$ echo $ID
f684fc88aec3
```

!SLIDE
# Commande ps

Lister les conteneurs qui tournent : 

```bash
$ sudo docker ps
ID                  IMAGE               COMMAND                CREATED
f684fc88aec3        ubuntu:latest       /bin/sh *c while tru   33 seconds ago
```

*Note* : option -a pour voir tous les conteneurs.

!SLIDE
# Commande logs

Afficher la console :

```bash
$ docker logs f684fc88aec3
hello world
hello world
hello world
hello world
hello world
```

!SLIDE
# Commande attach

Attacher un conteneur : 

```bash
$ docker attach f684fc88aec3
hello world
hello world
hello world
```

!SLIDE
# Commande stop

Arrêter un conteneur : 

```bash
$ docker stop f684fc88aec3
```

*Note* : la commande pour lancer est... start.


!SLIDE
# Exposer un port

Associer le port 8888 de la machine hôte avec le port 80 du conteneur :

```bash
$ sudo docker run -d -p 8888:80 ubuntu
```

!SLIDE
# Exposer un volume

Monter le répertoire /etc d'un autre conteneur : 

```bash
$ ID=(sudo docker run -d -v /etc ubuntu)
$ sudo docker run -d -volumes-from=$ID ubuntu
```

!SLIDE
# Commande commit

La commande commit enregistre la différence entre l'image et le conteneur : 

```bash
$ sudo docker run -i -t ubuntu /bin/bash
root@419eed6ff306:/# apt-get install nginx
...
$ sudo docker commit -m "Ubuntu with Nginx" f684fc88aec3 sdouche/nginx
```

!SLIDE
# Dockerfile

Langage de script pour automatiser la création d'images.

Tutorial : http://www.docker.io/learn/dockerfile/

!SLIDE
# Exemple

Une version simple :

```
FROM ubuntu:quantal
MAINTAINER Sebastien Douche sdouche@gmail.com 
RUN echo "deb http://fr.archive.ubuntu.com/ubuntu quantal main restricted universe multiverse" \
          > /etc/apt/sources.list
RUN apt-get update
RUN apt-get install -y nginx
ENTRYPOINT ["nginx"]
EXPOSE 80
```

!SLIDE
# Résultat

```bash
$ sudo docker build -t sdouche/nginx .
Uploading context 10240 bytes
Step 1 : FROM ubuntu
 ---> 8dbd9e392a96
Step 2 : MAINTAINER Sebastien Douche sdouche@gmail.com
 ---> Using cache
 ---> 1ed956422d1e
Step 3 : RUN echo "deb http://fr.archive.ubuntu.com/ubuntu quantal main restricted universe multiverse" > /etc/apt/sources.list
 ---> Running in 0f306df45690
 ---> 769ee79eb920
Step 4 : RUN apt-get update
 ---> Running in ab6e03a4b456
...
 ---> 70c4d6981ec2
Step 5 : RUN apt-get install -y nginx-full
 ---> Running in 4f9b62e09cef
...
 ---> 3fdbac5a8b0b
Step 6 : ENTRYPOINT ["nginx"]
 ---> Running in 887a4e981b5b
 ---> 641d26bd702a
Step 7 : EXPOSE 80
 ---> Running in fedd77ac26f7
 ---> 9d0045d5fa7a
Successfully built 9d0045d5fa7a
```

!SLIDE
# Commande diff

La commande diff affiche les différences avec le conteneur père :

```bash
$ sudo docker diff 769ee79eb920
2013/10/05 03:01:25 GET /v1.5/containers/769ee79eb920/changes
C /dev
A /dev/kmsg
C /etc/apt/sources.list
```

!SLIDE
# Pousser une image sur l'index

Il faut créer un compte sur l'index (ici sdouche) et pousser sous son compte : 

```bash
$ sudo docker push sdouche/nginx
```
!SLIDE
# Commande import / export

Exporter son conteneur (sauvegarde, transfert)

```bash
$ sudo docker export 9d0045d5fa7a > container-nginx.tgz
```

L'importer :

```bash
$ sudo docker import http://example.com/image.tgz example
$ cat example.tgz | sudo docker import - example
$ sudo tar -c . | docker import - exampledir
```


!SLIDE
# Documentation CLI

Liste des commandes :

http://docs.docker.io/en/latest/commandline/cli/

!SLIDE

# API

http://docs.docker.io/en/latest/api/

* API REST
* Feed des evenements
* Websocket

Note : l'API est utilisée par la CLI

!SLIDE
# Clients

* Python (docker-py)
* Ruby (docker-client, docker-api)
* Javascript (docker.io, docker-js, dockerui)
* Java (docker-java)
* Erlang (erldocker)
* Go (go-dockerclient)

!SLIDE
# Exemple avec (docker-py) Python

```python
import docker

# create client
docker_client = docker.Client(base_url='unix://var/run/docker.sock', version="1.4")

# create container
container = docker_client.create_container('ubuntu', None, detach=True)
container_id = container.get('Id')

# start Container
docker_client.start(container_id)

# get Ip address
meta = docker_client.inspect_container(container_id)
ip = meta.get('NetworkSettings').get("IPAddress")

# stop container
docker_client.stop(container_id)
```

!SLIDE
# Docker Index

Dépôt publique des images (+ metadonnées)

https://index.docker.io/

!SLIDE
# Registry

Gestion en interne des images

https://github.com/dotcloud/docker-registry

!SLIDE
# GUI

* Dockland - Yet another docker web UI
  https://github.com/dynport/dockland
* Shipyard – a web UI for Docker
  https://github.com/ehazlett/shipyard
* DockerUI – Web interface to interact with the Docker Remote API
  https://github.com/crosbymichael/dockerui

!SLIDE
# Déploiement

* PAAS : dokku / flynn.io / orchadup.com / deis.io
* IAAS : OpenStack
* SCM : Chef / Puppet / Salt / Ansible
* OS : CoreOS
* Orchestration : Maestro / Shipper

!SLIDE
# Docker 1.0

 Etendre les technologies utilisées : 

* Architecture pluggable
* Gestion des Zones (Solaris FreeBSD), BSD Jails, OpenVZ...
* Support de Btrfs
* Meilleur support des distributions (RedHat...)

!SLIDE
# Fin

Je vais pouvoir boire une bière.
