# Docker

[Introduction vidéo aux principes de docker](https://www.youtube.com/watch?v=3c-iBn73dDE)


 ![docker vs vm](./images/docker\ vs\ vm.png "docker vs vm")

## container

**container**: a way to package application with all the necessary dependencies and configurations

It's a portable artifact (easily shared and moved around)


container repository: storage for containers (private or public)

[docker hub: public repo for containers](https://hub.docker.com/)

Technicaly a container is a stack of layers which are **images** (often with a light linux based image at the base of the stack, alpine distribution)

## commandes basiques

Récupérer une image sur docker hub

```sh
docker pull redis
```

Récupérer une image spécifique et l'exécuter directement dans un container:

```sh
docker run redis:latest
```

Exécuter en mode détaché (i.e. en toile de fond)

```sh
docker run -d redis:latest
```

Liste des containers actifs sur la machine locale

```sh
docker ps
```

Liste des images présentes sur la machine locale

```sh
docker images
```

Liste de l'ensemble des containers sur la machine locale (actifs ou non)

```sh
docker ps -a
```

```sh
docker run redis
```
`run = pull + start`


```sh
docker stop 64dd3a6ddca1
```

```sh
dockder start 64dd3a6ddca1
```

Assignation de ports

`container port vs host port.png`

 ![workflow with docker](./images/workflow\ with\ docker.png "workflow with docker")

`
local <--> docker container
`  
`-p local_port:container_port`

```sh
docker run -p 6000:6379 -d redis
docker run -d -p 6001:6379 --name redis-older redis:4.0
```

Logs

```sh
docker logs 804704bf2d01
docker logs redis-older
```

Interactive terminal

```sh
docker exec -it fa4b94473e8e /bin/bash
```
Afficher toutes les variables d'environnement: `env`

Sortir du terminal du container: `exit`


Grand nettoyage  
[Remove all unused containers, volumes, networks and images](https://docs.docker.com/engine/reference/commandline/system_prune/#extended-description)
 
 ```
 docker system prune -a --volumes
 ```
 
 