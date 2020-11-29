## Théo Goetzinger

## TP8

## prometheus.yml 

```

scrape_configs:

    - job_name: 'exporter'
      static_configs:
        - targets: ['exporter:9104']


```

## docker-compose.yml : 

```
version: '3.7'

services:
  db:
    image: mariadb
    restart: on-failure
    environment:
      MYSQL_ROOT_PASSWORD: password
    volumes:
        - ./scripts:/docker-entrypoint-initdb.d
        - ./mysql:/var/lib/mysql
    ports:
      - "3306:3306"
    networks:
     - bdd

  exporter:
    image: prom/mysqld-exporter
    restart: on-failure
    environment:
      DATA_SOURCE_NAME: exporter:password@(db:3306)/
    ports:
      - 9104
    networks:
      - bdd
    depends_on:
      - db

  prometheus:
    image: prom/prometheus
    restart: on-failure
    volumes:
      - ./configs/prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    networks:
      - bdd
    depends_on:
      - exporter


networks:
  bdd:

```


## lancement du docker-compose
```
sudo docker-compose up -d

```

##  Connexion à mariadb et création de la db
```
mysql -u root -p

CREATE DATABASE TP8;
USE TP8;
CREATE TABLE donnees(nom VARCHAR(255), prenom VARCHAR(255), ville VARCHAR(255));
INSERT INTO donnees VALUES ("Jean", "Michel", "Nantes");
SELECT * FROM donnees;

```

## connexion au container prometheus puis au service web via un navigateur

```
docker-compose exec prometheus sh

/prometheus $ ip a 

	inet 172.28.0.4

    sur un navigateur : http://172.28.0.4:9090

    j'ai bien accès à l'interface web du prometheus mais je n'ai pas accès au bonne métric pour pouvoir faire le graph 
    vu avec Dennis, le problème venais que le prometheus n'arrivaus pas à faire le lien avec l'exporter :
    j'ai essayé de créer un nouveau dossier Tp8 et de relancer mon docker-compose avec les différents scripts mais je n'arrive pas 
    à résoudre le problème je suis bloqué
```
