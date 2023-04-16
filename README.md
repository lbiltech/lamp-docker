# Welcome to Local Docker Config for LAMP Stack

:whale: This is a simple Docker Compose workflow that sets up a LAMP network of containers for local development. This also allows customizing the optional PHP version :elephant:


> Other docker config: [LEMP Stack (Nginx, PHP, MariaDB, Redis)](https://github.com/tanhongit/lemp-docker.git) :whale:

## Configuration requirements

To use the optional PHP image, you need an additional web server, such as apache, that can proxy http-request to the apache-pot of the container. This is not a requirement for the fpm image, but is for the web server.

 - Multi-site integration
 - **PHP optional version with custom in .env file** (example: 7.4, 8.0, 8.1, 8.2)
 - Web-server: Apache
 - DBMS (database management system): mariadb
 - In-memory database: Redis
 - SSL Certificate (using mkcert)
 
## Installation and Setup

### 1. Install ssl certificate
Using mkcert to create ssl certificate

#### For Ubuntu

```shell
sudo apt install libnss3-tools

sudo wget https://github.com/FiloSottile/mkcert/releases/download/v1.4.3/mkcert-v1.4.3-linux-amd64 && \
sudo mv mkcert-v1.4.3-linux-amd64 mkcert && \
sudo chmod +x mkcert && \
sudo cp mkcert /usr/local/bin/
```
#### For Mac or Windows

Please check and install mkcert at: https://github.com/FiloSottile/mkcert

Now that the mkcert utility is installed, run the command below to generate and install your local CA:

```shell
mkcert -install
```

### 2. Create ssl certificate for this project

Run:

```shell
cd docker/server/certs
mkcert local.localhost.com
mkcert local.lamp.com
cd ../../../
```

### 3. Copy .env.example to .env

```shell
cp .env.example .env
```

### 4. Edit .env file

```dotenv
PHP_VERSION_SELECTED=8.2 # choose PHP version for your project

APP_NAME=lamp-local # name of your docker project
APP_PORT=91 # port for docker server (apache)
SSL_PORT=445 # port for docker server (apache) with ssl
DB_PORT=13393 # port for database (mariadb)

MYSQL_ROOT_PASS=root
MYSQL_USER=root
MYSQL_PASS=root
MYSQL_DB=lamp-local # name of your database

PHPMYADMIN_PORT=19011 # port for phpmyadmin (database admin)
IP_DB_SERVER=127.0.0.2

REDIS_PORT=16379 # port for redis (in-memory database)
```

### 4. Run to start the containers

```shell
docker-compose up -d
```

## Check the network ID and connect Database

### 1. Check CONTAINER_ID
- Run `docker ps` to check the Container ID of **APP_NAME-db**
- Run the command `docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_ID>`

```shell
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_ID>
```

![image](https://imgur.com/eXqHQVb.png)

### 2. Connect to Database

Use the IP address to connect to the database using the database management system (DBMS) of your choice. For example, using MySQL Workbench:

![image](https://user-images.githubusercontent.com/35853002/232210044-7dd5aafa-352f-45d8-ba99-82cb792b1066.png)

You can also connect to the database using the DB_PORT in .env file

For example, using MySQL Workbench: DB_PORT=13393

![image](https://user-images.githubusercontent.com/35853002/232210171-af56d440-c9f0-4477-a1a7-338b86995cd7.png)

## Demo

This is a demo of the project with the environment variable **PHP_VERSION_SELECTED=7.4**

![image](https://user-images.githubusercontent.com/35853002/184285134-88e43cd9-d9dd-4110-bda3-c7fb8840835d.png)
