
# Volumes

Persistance des données grace à une association entre le système de fichiers local et celui virtuel de docker qui réplique automatiquement les données.

![docker volumes](./images/docker\ volumes.png)

Dans `docker run` on peut faire l'association **hôte:docker** `doker run -v /home/mount/data:var/lib/mysql/data`

```sh
docker run \
--name recycla-score-mysql \
--publish 3307:3306 \
--env MYSQL_ROOT_PASSWORD=mdp \
--detach \
--volume /Users/pierrejaumier/mysql/data:/var/lib/mysql/data \
rcs-mysql:1.0
```

Plus simple, et meilleure option, on peut laisser docker gérer le stockage sur la machine hôte. On associe juste un nom au volume: (c'est docker qui gère le lien entre le container et le volume dit "named volume")

```sh
docker run \
	-v name:var/lib/mysql/data
```

Dans docker-compose:

```sh
services:
  mongodb:
    image: mongo
    ports:
      - 27017:27017
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=mdp
    volumes:
      - mongo-data:/data/db
volumes:
  mongo-data:
    driver: local
```

```sh
docker-compose -f test-docker-compose-3.yaml up
http://localhost:8081/
```
créer db `test`, collection et document

```sh
docker-compose -f test-docker-compose-3.yaml down
http://localhost:8081/
```
up & test

down

```sh
docker volume ls
docker volume rm initiation-docker_mongo-data
```
