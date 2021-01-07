# Directory

> - [1. Intro](#chapter-1)
> - [2. Deploy Tars framework by Docker](#chapter-2)
> - [3. Deploy Tars Node by Docker](#chapter-3)
> - [4. Trouble Shot](#chapter-4)
> - [5. docker-compose](#chapter-5)
> - [5. docker mirror version](#chapter-5)

# 1 <a id="chapter-1"></a>Intro

This section mainly introduces the use of docker to complete the deployment of the framework. There are two types of docker available:

- framework: The tars framework docker scripts contains the framework core services and the web management platform.
- tars: It adds Java, nodejs and other runtime support. That is, it can publish Java and nodejs services to docker (the docker is installed with JDK, node, PHP environment)

First, make sure you have installed the docker environment on your machine. If not, please refer to [docker install](docker-install.md)

## 2 <a id="chapter-2"></a>Deploy Tars framework by Docker

**If you want the source code to compile docker by yourself, see [Install](source.md)**

**Note: the difference is whether you want to deploy the business service in the image (not recommended, not convenient for upgrading the tars framework))**

### 2.1 create a docker network

First, create a docker network to make sure this guide work properly on each OS platform. Tars will work exactly like it works on a VM or a cloud hosting machine.

```sh
# Create a bridge virtual network and set the name, subnet and gateway.
docker network create -d bridge --subnet=172.25.0.0/16 --gateway=172.25.0.1 tars
```

### 2.2 Start MySQL

- Deploy MySQL service. You can skip this if you have a running MySQL instance. We highly suggest you to separate framework DB with business DB.

```
docker run -d \
    --net=tars \
    -e MYSQL_ROOT_PASSWORD="123456" \
    --ip="172.25.0.2" \
    -v {PATH_ON_YOUR_COMPUTER}:/var/lib/mysql \
    -v /etc/localtime:/etc/localtime \
    --name=tars-mysql \
    mysql:5.6
```
{PATH_ON_YOUR_COMPUTER}: it is a folder that you should create by yourself where tars database will be deployed.

### 2.3 run tarscloud/framework docker

1. Pull docker image

```sh
docker pull tarscloud/framework:v2.4.0
```
We highly recommended you to use a specific framework version tag to deploy your environment. Thus, you won't be affected by a remote image update.

2. Run docker image

```sh
# Mount timezone file /etc/localtime to container. Remove it if you don't have.
# expose port 3000 for web entry
# expose port 3001 for web authorization
docker run -d \
    --name=tars-framework \
    --net=tars \
    -e MYSQL_HOST="172.25.0.2" \
    -e MYSQL_ROOT_PASSWORD="123456" \
    -e MYSQL_USER=root \
    -e MYSQL_PORT=3306 \
    -e REBUILD=false \
    -e INET=eth0 \
    -e SLAVE=false \
    --ip="172.25.0.3" \
    -v {PATH_ON_YOUR_COMPUTER}:/data/tars \
    -v /etc/localtime:/etc/localtime \
    -p 3000:3000 \
    -p 3001:3001 \
    tarscloud/framework:v2.4.14
```

{PATH_ON_YOUR_COMPUTER}: it is a folder that you should create by yourself where tars framework will be deployed.

You can access `http://localhost:3000` to access the Tars web management platform.

3. Directory
   During creation, the directory / data / tars of docker will be mapped to the host directory / data / tars. After starting docker, please check the host directory / data / tars. Normally, the following directories will be created:

- app_log: Log directory of the tar service
- tarsnode-data: tarsnode/data directory (to store the business services published to docker) to ensure that docker is restarted and the data is not lost
- web_log: Logs of the tars-node-web module in the web (only available to the host)
- demo_log: Logs of the tars-user-system module in the web (only available to the host)
- patchs: Uploaded release package (only available for the host)

If these directories are not created, you can create them manually and restart docker

4. Parameter interpretation

MYSQL_IP: IP of MySQL DB instance

MYSQL_ROOT_PASSWORD: Root password for MySQL

INET: The name of the network interface (as you can see in ifconfig, such as eth0) indicates the native IP bound by the framework. Note that it cannot be 127.0.0.1

REBUILD: Whether to rebuild the database is usually false. If there is an error in the intermediate installation and you want to reset the database, you can set it to true

SLAVE: Is this a slave node

MYSQL_USER: MySQL user. Will use root as default.

MYSQL_PORT: MySQL port

5. Run platform replica

**If you want to deploy multiple nodes, just execute docker run... On different machines. Pay attention to the parameter settings!**

```sh
docker run -d \
    --name=tars-framework-slave \
    --net=tars \
    -e MYSQL_HOST="172.25.0.2" \
    -e MYSQL_ROOT_PASSWORD="123456" \
    -e MYSQL_USER=root \
    -e MYSQL_PORT=3306 \
    -e REBUILD=false \
    -e INET=eth0 \
    -e SLAVE=true \
    --ip="172.25.0.4" \
    -v /data/framework-slave:/data/tars \
    -v /etc/localtime:/etc/localtime \
    tarscloud/framework:v2.4.14
```

**Attention: SLAVE environment is true**

## 3 <a id="chapter-3"></a>Docker Deploy Tars Node by Docker

1. Pull image

```sh
docker pull tarscloud/tars-node:latest
```

2. Run Node image

```sh
docker run -d \
    --name=tars-node \
    --net=tars \
    -e INET=eth0 \
    -e WEB_HOST="http://172.25.0.3:3000" \
    --ip="172.25.0.5" \
    -v /data/node:/data/tars \
    -v /etc/localtime:/etc/localtime \
    -p 9000-9010:9000-9010 \
    tarscloud/tars-node:latest
```

- Ports 9000 to 9010 are for your applications. You can add more ports if necessary
- Node will register itself to Tars framework `172.25.0.3`.You can find it in the node management part of your platform web.


**Note that if you use --net=host on the same machine and start the framework and tars-node images at the same time, it is not possible because the framework also contains a tarsnode, which will cause port conflicts and fail to start**

## 4 <a id="chapter-4"></a>Trouble Shot

If the docker still cannot open the management platform after running, you can check as follows:

- Close docker. Note that you may need to use docker stop
- Start the image, do not use the -d parameter

```sh
docker --name=tars-framework \
    --net=tars \
    -e MYSQL_HOST="172.25.0.2" \
    -e MYSQL_ROOT_PASSWORD="123456" \
    -e MYSQL_USER=root \
    -e MYSQL_PORT=3306 \
    -e REBUILD=false \
    -e INET=eth0 \
    -e SLAVE=false \
    --ip="172.25.0.3" \
    -v /data/framework:/data/tars \
    -v /etc/localtime:/etc/localtime \
    -p 3000:3000 \
    tarscloud/framework:v2.4.14
```

- Check whether there is obvious problem with docker output
- You can use the same way to diagnosis node.

If the web platform is open, but the error is displayed, you need to check the web problems. You can enter docker, please refer to check the web problems in[check the web problem](web.md)

## 5 <a id="chapter-5"></a>docker-compose

- We provided a docker-compose.yml for you too. The subnet is set to `172.25.1.0/16` in case conflict with the guide in chapter 2 to 4

```yaml
version: "3"

services:
  mysql:
    image: mysql:5.6
    container_name: tars-mysql
    ports:
      - "3307:3306"
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: "123456"
    volumes:
      - ./mysql/data:/var/lib/mysql:rw
      - ./source/Shanghai:/etc/localtime
    networks:
      internal:
        ipv4_address: 172.25.1.2
  framework:
    image: tarscloud/framework:v2.4.14
    container_name: tars-framework
    ports:
      - "3000:3000"
    restart: always
    networks:
      internal:
        ipv4_address: 172.25.1.3
    environment:
      MYSQL_HOST: "172.25.1.2"
      MYSQL_ROOT_PASSWORD: "123456"
      MYSQL_USER: "root"
      MYSQL_PORT: 3306
      REBUILD: "false"
      INET: eth0
      SLAVE: "false"
    volumes:
      - ./framework/data:/data/tars:rw
      - ./source/Shanghai:/etc/localtime
    depends_on:
      - mysql
  node:
    image: tarscloud/tars-node:latest
    container_name: tars-node
    restart: always
    networks:
      internal:
        ipv4_address: 172.25.1.5
    volumes:
      - ./node/data:/data/tars:rw
      - ./source/Shanghai:/etc/localtime
    environment:
      INET: eth0
      WEB_HOST: http://172.25.1.3:3000
    ports:
      - "9000-9010:9000-9010"
    depends_on:
      - framework
networks:
  internal:
    driver: bridge
    ipam:
      config:
        - subnet: 172.25.1.0/16
```


## 7 <a id="chapter-7"></a>docker mirror version

Notice:
- docker mirror include : https://github.com/TarsCloud/TarsFramework and https://github.com/TarsCloud/TarsWeb.
- TarsFramework and TarsWeb are submodule,  included by https://github.com/TarsCloud/Tars.
- For better manage docker mirror version, docker mirror tag version is matching tags of https://github.com/TarsCloud/Tars 
- when https://github.com/TarsCloud/Tars  build tag, it will build docker mirror and push to docker hub.

The above execution mode is from the tarscloud/framework:v2.4.0