
# Directory
> * [Intro](#chapter-1)
> * [Deploy Tars by Docker](#chapter-2)
> * [Installation example](#chapter-3)
> * [Problem checking](#chapter-4)

# 1 <a id="chapter-1"></a>Intro

This section mainly introduces the use of docker to complete the deployment of the framework. There are two types of docker available:

- framework: The tars framework docker makes scripts, and the docker contains the framework core services and web management platform
- tars: The tars framework docker makes scripts. Compared with the framework, it adds Java, nodejs and other runtime support, that is, it can publish Java, nodejs services to the docker (the docker is installed with JDK, node, PHP environment)

First, make sure you have installed the docker environment on your service. If not, please refer to[docker install](docker-huan-jing-an-zhuang.md)

## 2 <a id="chapter-2"></a>Deploy Tars by Docker

**If you want the source code to compile docker by yourself, see [Install](source.md)**

Using docker to install the tars framework, there are two images to choose from: framework / tars

**Note: the difference is whether you want to deploy the business service in the image (not recommended, not convenient for upgrading the tars framework)**

### 2.1 tarscloud/framework

1. Pull Image
```sh
docker pull tarscloud/framework:stable
```

2. Start Image
```sh
docker run -d --net=host -e MYSQL_HOST=xxxxx -e MYSQL_ROOT_PASSWORD=xxxxx \
        -e MYSQL_USER=root -e MYSQL_PORT=3306 \
        -eREBUILD=false -eINET=eth0 -eSLAVE=false \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/framework:stable
```

3. Directory

During creation, the directory / data / tars of docker will be mapped to the host directory / data / tars. After starting docker, please check the host directory / data / tars. Normally, the following directories will be created:
- app_log: Log directory of the tar service
- tarsnode-data: tarsnode/data directory (to store the business services published to docker) to ensure that docker is restarted and the data is not lost
- web_log: Logs of the tars-node-web module in the web (only available to the host)
- demo_log: Logs of the tars-user-system module in the web (only available to the host)
- patchs: Uploaded release package (only available for the host)

If these directories are not created, you can create them manually and restart docker

### 2.2 tarscloud/tars

1. Pull Image
```sh
docker pull tarscloud/tars:stable
```

2. Start Image
```sh
docker run -d --net=host -e MYSQL_HOST=xxxxx -e MYSQL_ROOT_PASSWORD=xxxxx \
        -e MYSQL_USER=root -e MYSQL_PORT=3306 \
        -eREBUILD=false -eINET=eth0 -eSLAVE=false \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/tars:stable
```

Path mapping is same to tarscloud/framework

### 2.3 Parameter interpretation

MYSQL_HOST: mysql ip address

MYSQL_ROOT_PASSWORD: mysql root password

INET: The name of the network interface (as you can see in ifconfig, such as eth0) indicates the native IP bound by the framework. Note that it cannot be 127.0.0.1

REBUILD: Whether to rebuild the database is usually false. If there is an error in the intermediate installation and you want to reset the database, you can set it to true

SLAVE: slave node

MYSQL_USER: mysql user

MYSQL_PORT: mysql port

Map three directories to the host:
- -v/data/tars:/data/tars, include tars application log, web log, patch directory

**If you want to deploy multiple nodes, just execute docker run... On different machines. Pay attention to the parameter settings!**

**Here, you must use --net=host to indicate that the docker and the host are on the same network** 

For details, see [Install](source.md)

After installation, visit `http://${your_machine_ip}:3000` to open web

## 3 <a id="chapter-3"></a>Installation example]

For example, install two machines, one database (suppose: primary [192.168.7.151], secondary [192.168.7.152], MySQL: [192.168.7.153])

execute in 192.168.7.151:
```
docker run -d --net=host -e MYSQL_HOST=192.168.7.153 -e MYSQL_ROOT_PASSWORD=xxxxx \
        -e MYSQL_USER=root -e MYSQL_PORT=3306 \
        -eREBUILD=false -eINET=eth0 -eSLAVE=false \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/framework:stable

```
After the master node finishes executing, the slave node executes:
```
docker run -d --net=host -e MYSQL_HOST=192.168.7.153 -e MYSQL_ROOT_PASSWORD=xxxxx \
        -e MYSQL_USER=root -e MYSQL_PORT=3306 \
        -eREBUILD=false -eINET=eth0 -eSLAVE=true \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/framework:stable
```

**Note: slave parameters are different**

## 4 <a id="chapter-4"></a>Problem checking

If the docker still cannot open the management platform after running, you can check as follows:
- Close docker. Note that you may need to use docker stop
- Start the image, do not use the -d parameter

```sh
docker run --net=host -e MYSQL_HOST=xxxxx -e MYSQL_ROOT_PASSWORD=xxxxx \
        -e MYSQL_USER=root -e MYSQL_PORT=3306 \
        -eREBUILD=false -eINET=eth0 -eSLAVE=false \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/framework:stable
```
- Check whether there is obvious problem with docker output

If the web platform is open, but the error is displayed, you need to check the web problems. You can enter docker, please refer to check the web problems in [check the web problem](web.MD)
