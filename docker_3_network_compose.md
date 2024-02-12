# Réseau, docker-compose

Exemple de la vidéo [Demo Project Overview - Docker in Practice](https://www.youtube.com/watch?v=3c-iBn73dDE&t=3999s)

[Projet démo à récupérer sur github](https://gitlab.com/nanuchi/techworld-js-docker-demo-app)


 ![workflow with docker](./images/workflow\ with\ docker.png "workflow with docker")

 ![docker in sofware development](./images/docker\ in\ sofware\ development.png "docker in sofware development")


## Images

[hub.docker.com mongo](https://hub.docker.com/_/mongo)

[hub.docker.com mongo-express](https://hub.docker.com/_/mongo-express)

```sh
docker pull mongo
docker pull mongo-express
docker images
```

## Réseau

mongo-express <-- connection --> mongo

docker network
communication juste avec le nom du container si même réseau docker


```sh
docker network ls
docker network create mongo-network
docker network ls
```

## Conteneurs

mongo

```sh
docker run \
-d \
-p 27017:27017 \
--network mongo-network \
--name mongo-demo \
-e MONGO_INITDB_ROOT_USERNAME=admin \
-e MONGO_INITDB_ROOT_PASSWORD=mdp \
mongo
```

```sh
docker logs mongo-demo
```

mongo-express


```sh
docker run \
-d \
--network mongo-network \
--name mongo-express-demo \
-e ME_CONFIG_MONGODB_SERVER=mongo-demo \
-e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
-e ME_CONFIG_MONGODB_ADMINPASSWORD=mdp \
-p 8081:8081 \
mongo-express
```
Remarque: dans le cas où le localhost demande de se connecter mettre un login et mdp pour mongoexpress (ici `test`/ `test`)

```sh
docker run \
-d \
--network mongo-network \
--name mongo-express-demo \
-e ME_CONFIG_MONGODB_SERVER=mongo-demo \
-e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
-e ME_CONFIG_MONGODB_ADMINPASSWORD=mdp \
-e ME_CONFIG_BASICAUTH_USERNAME=test \
-e ME_CONFIG_BASICAUTH_PASSWORD=test \
-p 8081:8081 \
mongo-express
```

## node

Pour la démo dans Mongo Express, créer (optionnel)

    Database:
    	user-account
    Collection:
    	users

dans `docker-demo-app/app`

s'assurer que dans `server.js` on utilise bien `let mongoUrl = mongoUrlLocal` à la ligne 32 pour que les connexions se fassent correctement.


```sh
node server.js
```
Navigateur: `http://localhost:3000/`


## docker compose

 Automatisation du lancement des conteneurs

[documentation docker compose](https://docs.docker.com/compose/)

s'occupe du réseau local

### 1 conteneur

Créer un fichier `test-docker-compose-1.yaml`

```yaml
services:
    db:
        image: redis
        container_name: redis-test
        ports:
            - 6379:6379
```

lancer avec docker-compose

```sh
docker-compose -f test-docker-compose-1.yaml up
```

Au lieu de `docker remove redis-test`:

```sh
docker-compose -f test-docker-compose-1.yaml down
docker ps -a
```

### plusieurs conteneurs

Créer un fichier `test-docker-compose-2.yaml`

```yaml
services:
    mongodb:
        image: mongo
        container_name: mongo-dc
        ports:
            - 27017:27017
        environment:
            - MONGO_INITDB_ROOT_USERNAME=admin
            - MONGO_INITDB_ROOT_PASSWORD=mdp
    mongo-express:
        image: mongo-express
        container_name: mongo-express-dc
        restart: always # fixes MongoNetworkError when mongodb is not ready when mongo-express starts
        ports:
            - 8081:8081
        environment:
            - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
            - ME_CONFIG_MONGODB_ADMINPASSWORD=mdp
            - ME_CONFIG_MONGODB_SERVER=mongo-dc
```

lancer avec docker-compose

```sh
docker-compose -f test-docker-compose-2.yaml up
```

> L'arrêt d'un conteneur entraîne la perte de toutes ses données


```sh
docker-compose -f test-docker-compose-2.yaml down
```
