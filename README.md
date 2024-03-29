# Welcome to Local Docker Config for LAMP Stack

:whale: This is a simple Docker Compose workflow that sets up a LAMP network of containers for local development. This also allows customizing the optional PHP version :elephant:

This configuration can be used for any PHP project (Laravel, Yii, CodeIgniter, Pure PHP, etc.) :tada:

[![Docker](https://github.com/lbiltech/lamp-docker/actions/workflows/docker-image.yml/badge.svg)](https://hub.docker.com/r/lbiltech/lamp)

## Table of Contents

 - [Configuration requirements](#configuration-requirements)
 - [Add your custom bash script to run docker](#add-your-custom-bash-script-to-run-docker)
 - [Installation and Setup](#installation-and-setup)
 - [Check the network ID and connect Database](#check-the-network-id-and-connect-database)

> Other docker configs: [LEMP Stack (Nginx, PHP, MariaDB, Redis)](https://github.com/tanhongit/lemp-docker.git) :whale:


## Add your custom bash script to run the docker

> **Because maybe my configuration is not enough for the configuration required by your project or purpose, you need to add more configuration to the docker.**

With nature of allowing you to customize the PHP version according to your. We should also allow custom bash scripts to run when starting the docker.

So, you can add your custom bash script to run docker in the file **_docker/bash/custom.sh_** with the following steps:

### 1. Create a file **_docker/bash/custom.sh_**:

Run:

```bash
cd docker/bash
cp custom.sh.example custom.sh
```

### 2. Add your custom bash script to run the docker

Then, you can add your custom bash script to run docker in the file **_docker/bash/custom.sh_**.

For example, you want to install **_ngrok_** extension for your docker container:

```bash
#!/bin/bash

# Install ngrok
snap install ngrok

# Install ngrok for php
docker-php-ext-install ngrok

# You can install other extensions here
# docker-php-ext-install <extension_name>

# etc.
npm install -g localtunnel
```

And now, just follow the steps below and run the docker.

## Configuration requirements

To use the optional PHP image, you need an additional web server, such as Apache, that can proxy HTTP-request to the Apache-pot of the container. This is not a requirement for the fpm image but is for the web server.

 - **Multi-site integration**
 - **PHP optional version with custom in .env file** (example: 7.4, 8.0, 8.1, 8.2, etc.)
 - Web server: **Apache**
 - DBMS (database management system): **Mariadb**
 - In-memory database: **Redis**
 - SSL Certificate (using **mkcert**)
 
## Installation and Setup

> **Warning**: If you don't want to use SSL, you can skip steps 1 and 2 and edit conf files in _**docker/server/apache/sites-enabled/*.conf**_ to remove the SSL configuration.

Please remove the 443 port in _**docker/server/apache/sites-enabled/*.conf**_ file and use 80 port for HTTP:

```apacheconfig
<VirtualHost *:80>
    ServerAdmin webmaster@localhost.com
    ServerName local.localhost.com
    ServerAlias local.com
    DocumentRoot /var/www/local

    ErrorLog /var/www/logs/error-local.log
    CustomLog /var/www/logs/access-local.log combined

    SSLEngine on
	SSLCertificateFile /var/www/certs/local.localhost.com.pem
	SSLCertificateKeyFile /var/www/certs/local.localhost.com-key.pem

    <Directory /var/www/local>
        Options +Indexes +Includes +FollowSymLinks +MultiViews
        AllowOverride All
        Require all granted
  </Directory>
</VirtualHost>
```

If you want to use SSL, please ignore the above warning and follow all the steps below.

### 1. Install SSL certificate
Using **mkcert** to create ssl certificate

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

### 2. Create an SSL certificate for this project

Go to this docker folder and run:

```shell
cd docker/server/certs
mkcert local.localhost.com
mkcert local.lamp.com
```

### 3. Copy .env.example to .env

Go to this docker folder and run:

```shell
cp .env.example .env
```

### 4. Edit .env file

```dotenv
PHP_VERSION_SELECTED=8.2 # Choose the PHP version for your project

APP_NAME=lamp-local # name of your docker project
APP_PORT=91 # port for docker server (apache)
SSL_PORT=445 # port for docker server (apache) with SSL
DB_PORT=13393 # port for a database (MariaDB)

MYSQL_ROOT_PASS=root
MYSQL_USER=root
MYSQL_PASS=root
MYSQL_DB=lamp-local # name of your database

PHPMYADMIN_PORT=19011 # port for phpmyadmin (database admin)
PHPMYADMIN_UPLOAD_LIMIT # upload limit for phpmyadmin
IP_DB_SERVER=127.0.0.1

REDIS_PORT=16379 # port for Redis (in-memory database)
```

### 4. Run to start the containers

Go to this docker folder and run:

```shell
docker-compose up -d
```

## Check the network ID and connect the Database

### 1. Check CONTAINER_ID
- Run `docker ps` to check the Container ID of **APP_NAME-db**
- Run the command `docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_ID>`

```shell
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_ID>
```

![image](https://user-images.githubusercontent.com/35853002/232272286-4dd7cc26-1257-4b1e-9605-7d6ecfd69a37.png)

### 2. Connect to Database

Use the IP address to connect to the database using the database management system (DBMS) of your choice. For example, using MySQL Workbench:

![image](https://user-images.githubusercontent.com/35853002/232210044-7dd5aafa-352f-45d8-ba99-82cb792b1066.png)

You can also connect to the database using the DB_PORT in the .env file

For example, using MySQL Workbench: DB_PORT=13393

![image](https://user-images.githubusercontent.com/35853002/232210171-af56d440-c9f0-4477-a1a7-338b86995cd7.png)

## Demo

This is a demo of the project with the environment variable **PHP_VERSION_SELECTED=8.2**

![image](https://user-images.githubusercontent.com/35853002/235107675-f73e4f5a-2748-41f1-a21c-7419d44b82e8.png)
