# Conteneurisation de la base mysql

[image mysql sur docker hub](https://hub.docker.com/_/mysql)

Utilisation d'une variable d'environnemnent `MYSQL_ROOT_PASSWORD` pour initier le mdp root du SGBD

```sh
docker run \
--name recycla-score-mysql \
-e MYSQL_ROOT_PASSWORD=mdp \
-p 3307:3306 \
-d \
mysql:latest
```

Dans le conteneur, vérifions la version de mysql

```sh
docker exec -it recycla-score-mysql bash
bash-4.4# mysql --version
```

Connexion depuis le poste local au conteneur

```sh
/usr/local/mysql-8.0.27-macos11-arm64/bin/mysql \
--host=127.0.0.1 \
--port=3307 \
--user=root \
--password
```

```sql
show databases;
```


Workbench

Créer une nouvelle connexion avec le bon port

Se connecter au conteneur

Création de la db:

```sql
CREATE SCHEMA recycle;
```

Création de la table principale:

```sql
USE recycle;

CREATE TABLE recycle.material (
  id INT NOT NULL AUTO_INCREMENT,
  name VARCHAR(45) NULL,
  recyclable INT NULL,
  PRIMARY KEY (id));
  
SHOW TABLES;
```

Alimentation en données initiales

```sql
USE recycle;

INSERT INTO recycle.material (name, recyclable) VALUES ('bois', '1');
INSERT INTO recycle.material (name, recyclable) VALUES ('plastique', '1');
INSERT INTO recycle.material (name, recyclable) VALUES ('polystyrène expansé', '0');
INSERT INTO recycle.material (name, recyclable) VALUES ('carton', '1');

SELECT * FROM material;
```

Dans le dossier `recycla-score mysql image builder/` créer une sauvegarde de la base et de son contenu `dump_recycle.sql` (création à la main du schéma et de la base utilisée?)

cf `docker_4_Dockerfile.md` pour la création d'une image mysql avec base et données