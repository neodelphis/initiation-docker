# Dockerfile 

Créer une image dédiée à son projet

Suppression d'une image:

```sh
docker rmi rcs-mysql:1.0
```

## mysql avec base et données

[populate docker mysql image](https://forums.docker.com/t/how-best-to-populate-a-mysql-database/66778)

[Creating MySQL Image with Dockerfile](https://jojozhuang.github.io/architecture/creating-mysql-image-with-docker-file/)

Dans le dossier `recycla-score mysql image builder/`

S'il n'existe pas, créer `dump_recycle.sql` après `docker_2_mysql`

et un fichier `Dockerfile`

```
FROM mysql

ADD dump_recycle.sql /docker-entrypoint-initdb.d

RUN chown -R mysql:mysql /docker-entrypoint-initdb.d/

EXPOSE 3306
```

[EXPOSE reference](https://docs.docker.com/engine/reference/builder/#expose)


```sh
docker build -t rcs-mysql:1.0 .
```

docker run --name recycla-score-mysql --publish 3307:3306 --env MYSQL_ROOT_PASSWORD=mdp --detach rcs-mysql:1.0

```sh
docker run \
--name recycla-score-mysql \
--publish 3307:3306 \
--env MYSQL_ROOT_PASSWORD=mdp \
--detach \
rcs-mysql:1.0
```

check Workbench

## projet complet: Dockerfile et docker-compose

[Projet démo à récupérer sur github](https://gitlab.com/nanuchi/techworld-js-docker-demo-app)

dans `docker-demo-app/app`

s'assurer que dans `server.js` on utilise bien `let mongoUrl = mongoUrlDocker` à la ligne 32 pour que les connexions se fassent correctement.

`Dockerfile`:

```sh
FROM node:13-alpine

ENV MONGO_DB_USERNAME=admin \
    MONGO_DB_PWD=mdp

RUN mkdir -p /home/app

COPY ./app /home/app

# set default dir so that next commands executes in /home/app dir
WORKDIR /home/app

# will execute npm install in /home/app because of WORKDIR
RUN npm install

# no need for /home/app/server.js because of WORKDIR
CMD ["node", "server.js"]
```


```sh
docker build -t my-app:1.0 .
```

`docker-compose.yaml`:

```sh
version: '3'
services:
  my-app:
    image: my-app:1.0
    ports:
    - 3000:3000
  mongodb:
    image: mongo
    ports:
      - 27017:27017
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=mdp
    # volumes:
    #   - mongo-data:/data/db
  mongo-express:
    image: mongo-express
    restart: always # fixes MongoNetworkError when mongodb is not ready when mongo-express starts
    ports:
      - 8081:8081
    environment:
      - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
      - ME_CONFIG_MONGODB_ADMINPASSWORD=mdp
      - ME_CONFIG_MONGODB_SERVER=mongodb
# volumes:
#   mongo-data:
#     driver: local

```


```sh
docker-compose -f docker-compose.yaml up
```
Dans le conteneur: 
```
docker exec -it docker-demo-app-my-app-1 sh
cd /home/app
ls
exit
```

Navigateur: `http://localhost:3000/`, `http://localhost:8081/`


```sh
docker-compose -f docker-compose.yaml down
```


Pas de persistance des données lors d'un redémarrage des conteneurs.





