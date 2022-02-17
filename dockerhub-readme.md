# What this repository is about

This docker image is a project realized in the context of my studies at IMT Nord Europe.

You can use it freely but don't expect a real node.js application.

# How to use this image

This image is the front end of this Node.js application. 

When running, It connects to the database and then when requesting localhost on port 8080, it will return the connection state.

If you want to streamline this process,  feel free to use docker-compose.

## Starting database

This app is designed to run with MySQL2 database. You can use the official [MySQL container](https://hub.docker.com/_/mysql/) 

run the following command to start your database container : 

```bash
 docker pull mysql
 docker run -d -p 3306:3306 --name [Container_Name] --env MYSQL_ROOT_PASSWORD=[Custom_Password] mysql
```

> **note** : Please notice that the `MYSQL_ROOT_PASSWORD` is customizable so  please define a strong one

## Start the  docker-db-connection container

You will need the database IP address if you did not customize it. 

you can get it by taping the following command : 

```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [Container_Name]
```

Once your database is started, you can run the docker-db-connection image with the following command :

```bash
docker run -dt -e 'HOST=[DB-IP-Address]' \
-e 'MYSQL_ROOT_PASSWORD=[Custom_Password]' \
-p 8080:8080 \
--name [Container_Name] webapp-with-db:1.0.0 
```
