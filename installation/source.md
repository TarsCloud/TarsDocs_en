# Directory
> * [Dependent Environments](#chapter-1)  
> * [Install develop environment for C++](#chapter-2)  
> * [Tars framework Installation](#chapter-3)  
> * [Tars-web Description ](#chapter-4)
> * [Scripts](#chapter-5)
> * [Proposed deployment plan](#chapter-6)

This document describes the steps to deploy, run, and test Tars framework.

If you use Tars for the production environment, the deployment steps are similar, but you need pay attention to fault-tolerance. You can join us for discussion in the QQ group code 579079160.  
 
## 1. <a id="chapter-1"></a>Dependent environments  
  
Software | Software requirements  
------|--------  
linux kernel version: | 2.6.18 or later (Dependent OS)  
gcc version: | 4.8.2 or later、glibc-devel（Dependent c++ framework tools）  
bison version: | 2.5 or later（Dependent c++ framework tools）  
flex version: | 2.5 or later（Dependent c++ framework tools）  
cmake version: | 3.2 or later（Dependent c++ framework tools）  
mysql version: | 5.6 or later（dependency of framework running）  
nvm version: | 0.35.1 or later（Dependent web management system, auto install while deploying）  
node version: | 12.13.0 or later（Dependent web management system, auto install while deploying）  
  
Hardware requirements: a machine running Linux or Mac.  

## 1.1. download and install build package dependency and tools

Source compilation needs to be installed :gcc, glibc, bison, flex, cmake, which, psmisc, ncurses-devel zlib

for example in CentoOS:
```
yum install glibc-devel gcc gcc-c++ bison flex cmake psmisc ncurses-devel zlib-devel
```

在ubuntu下执行:
```
sudo apt-get install build-essential bison flex cmake psmisc libncurses5-dev zlib1g-dev
```

For Mac installation, please install brew first (how to install brew on mac, please search by yourself)

```
brew install bison flex cmake
```

## 1.2. Mysql installation

when deploy the tars, your MySQL can be installed on other machines.

Tars framework installation needs to read and write data in mysql, so you need to install mysql. If you already have mysql, you can ignore this step.

For MySQL installation, please refer to[mysql installation](mysql.md)

# 2. <a id="chapter-2"></a>Tars C + + development environment (required for source installation framework)

**The source code installation framework needs to do this step. If you only write services in C + +, just download the tarscpp code**

Download tarsframework source code

```
cd ${source_folder}
git clone https://github.com/TarsCloud/TarsFramework.git --recursive
```

Then enter the build source directory:

```
cd TarsFramework
git submodule update --remote --recursive
cd build
cmake ..
make -j4
```

By default, compiling tars will automatically download MySQL client source code, and compile libmyqlclient.a

Recompile if needed.  
```
cd build
make clean
make -j4
```

Change to user root and create the installation directory.  
```
cd /usr/local
mkdir tars
mkdir app
 
Installation:
  
```  bash
cd build
make install 
```  

**The path of installation package after compilation is /usr/local/tars/cpp, that is, the compiled framework & the installation script is in this directory**
**The default path after installation is /usr/local/app, which is the path after installation**

After install, the dependent libraries (MySQL static library) and header files will also be installed in this directory(/usr/local/tars/cpp/thirdparty). If SSL or nghttp2 is enabled, install is the same

If you want to install on different path:  
```  
**modify tarscpp/cmake/Common.cmake**  
**modify TARS_PATH in tarscpp/servant/makefile/makefile.tars**  
**modify TARS_PATH in tarscpp/servant/makefile/tars-tools.cmake**
**modify DEMO_PATH in tarscpp/servant/script/create_tars_server.sh**  
```  

# 3 <a id="chapter-3"></a>Tars framework Installation 

## 3.1. Installation

**There are two Installation modes of TarsFramework:**

- centos/ubuntu/mac automatic deploy, During the installation process, the network needs to download resources from the outside
- Make a docker image to complete the installation. The process of making a docker requires network download resources, but no external network is needed to start and run the docker image

**Attentions:**

- During the installation process, because the tar web relies on nodejs, it will automatically download nodejs, NPM, PM2 and related dependencies, and set the environment variables to ensure that nodejs takes effect
- The version of nodejs currently downloads v12.13.0 by default
- If you have lower version nodejs installed, you'd better uninstall it first

**Note: the compilation and installation of tarsframework needs to be completed**

Download tarsweb and copy to /usr/local/tars/cpp/deploy  (change dir name TarsWeb to web)

```
git clone https://github.com/TarsCloud/TarsWeb.git
mv TarsWeb web
cp -rf web /usr/local/tars/cpp/deploy/
```

for example, this is my files in /usr/local/tars/cpp/deploy:
```
ubuntu@VM-0-14-ubuntu:/usr/local/tars/cpp/deploy$ ls -l
total 10304
-rw-r--r--  1 root root  443392 Apr  3 17:22 busybox.exe
-rw-r--r--  1 root root    1922 Apr  3 17:22 centos7_base.repo
-rw-r--r--  1 root root    1395 Apr  3 17:22 Dockerfile
-rwxr-xr-x  1 root root    3260 Apr  4 11:31 docker-init.sh
-rwxr-xr-x  1 root root     319 Apr  3 22:13 docker.sh
drwxr-xr-x  7 root root    4096 Apr  3 17:57 framework
-rwxr-xr-x  1 root root    4537 Apr  4 11:31 linux-install.sh
-rwxr-xr-x  1 root root 9820288 Apr  3 22:16 mysql-tool
-rwxr-xr-x  1 root root     811 Apr  4 11:31 tar-server.sh
-rwxr-xr-x  1 root root   16449 Apr  3 17:22 tars-install.sh
-rwxr-xr-x  1 root root     320 Apr  4 11:31 tars-stop.sh
drwxr-xr-x  2 root root    4096 Apr  3 17:57 tools
drwxr-xr-x 12 root root    4096 Apr  3 21:07 web
-rwxr-xr-x  1 root root    3590 Apr  3 17:22 web-install.sh
-rwxr-xr-x  1 root root    1476 Apr  3 17:22 windows-install.sh
```

## 3.2. Basic description of TarsFramework

The framework can be deployed on a single machine or multiple machines. Multiple machines are in the mode of one master and many slaves. Generally, one master and one slave are enough:

- There can only be one master node and multiple slave nodes
- The master node will install by default: tarsadminregistry, tarspatch, tarsweb, tarstat, tarsproperty and tarslog. These services will not be installed on the slave node
- The tarslog can only be a single point, otherwise the remote logs will be scattered on multiple machines
- In principle, tarspatch and tarsweb can be multi-point. If they are deployed to multi-point, the /usr/local/app/patches directory needs to be shared among multiple computers (for example, through NFS). Otherwise, the service cannot be published normally
- You can later deploy the tarslog to a large hard disk server
- In practice, even if the master and slave nodes are hung, the normal operation of services on the framework will not be affected, only the publishing will be affected

After install, there will be five databases created: db_tars, db_tars_web, db_user_system, tars_stat and tars_property.  
  
- db_tars is the core database for framework, it consists of services info, service templates and service configuration, etc.  
- db_tars_web is the core database for tars web
- db_user_sysetm is user auth system for web
- tars_stat is the database for service monitor data.  
- tars_property is the database for service properties monitor data.  

After install, you will see the output of the install script:

```
 2019-10-31 11:06:13 INSTALL TARS SUCC: http://xxx.xxx.xxx.xxx:3000/ to open the tars web. 
 2019-10-31 11:06:13 If in Docker, please check you host ip and port. 
 2019-10-31 11:06:13 You can start tars web manual: cd /usr/local/app/web; npm run prd 
```
Open browser: http://xxx.xxx.xxx.xxx:3000/, If it goes well, you can see the web management platform.

**Note: after execution, you can check whether the nodejs environment variable is effective: node -- version. If the output is not v12.13.0, it means that the nodejs environment variable is not effective**

## 3.3. (centos/ubuntu/mac) deploy

enter /usr/local/tars/cpp/deploy, run:
```
chmod a+x linux-install.sh
./linux-install.sh MYSQL_HOST MYSQL_ROOT_PASSWORD INET REBUILD(false[default]/true) SLAVE(false[default]/true) MYSQL_USER MYSQL_PORT
```

MYSQL_HOST: mysql ip address

MYSQL_ROOT_PASSWORD: mysql root password, note that root should not have special characters, for example: !, otherwise, there is a problem with shell script recognition, because it is a special character.

INET: The name of the network interface (as you can see in ifconfig, such as eth0) indicates the native IP bound by the framework. Note that it cannot be 127.0.0.1

REBUILD: Whether to rebuild the database is usually false. If there is an error in the intermediate installation and you want to reset the database, you can set it to true

SLAVE: slave node

For example, install three machines and one mysql(suppose: Master [192.168.7.151], slave [192.168.7.152], MySQL: [192.168.7.153])

Execute on the master node (192.168.7.151)

```
chmod a+x linux-install.sh
./linux-install.sh 192.168.7.153 tars2015 eth0 false false
```

Execute on the slave node (192.168.7.152)

```
chmod a+x linux-install.sh
./linux-install.sh 192.168.7.153 tars2015 eth0 false true
```

Refer to screen output for errors during execution. If there is an error, it can be executed repeatedly (usually download resource error)

Note:
- If it is ubuntu, you should: sudo linux-install.sh ...
- Note: after execution, you can check whether the nodejs environment variable is effective: node --version
- After installation, the nodejs related environment variables will be written in /etc/profile
- If it doesn't work, execute it manually: source /etc/profile. If it's Ubuntu, please pay attention to the permission

## 3.4. Make docker

Objective: make the framework into a docker, and start the docker


First git clone TarsWeb in TarsFramework source directory, then execute:
```
git clone https://github.com/TarsCloud/TarsWeb.git web
#x64
sudo ./deploy/docker.sh v1 amd64
#arm64
sudo ./deploy/docker.sh v1 arm64
```


make docker:
```
chmod a+x docker.sh
#x64
sudo ./deploy/docker.sh v1 amd64
#arm64
sudo ./deploy/docker.sh v1 arm64
```
docker finished, you can see the docker: tarscloud/framework:v1

```
docker ps
```

You can publish the docker image to your machine and execute:

```
docker run -d --net=host -e MYSQL_HOST=xxxxx -e MYSQL_ROOT_PASSWORD=xxxxx \
        -e MYSQL_USER=root -e MYSQL_PORT=3306 \
        -eREBUILD=false -eINET=enp3s0 -eSLAVE=false \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/framework:v1
```

MYSQL_HOST: mysql ip address

MYSQL_ROOT_PASSWORD: mysql root password

INET: The name of the network interface (as you can see in ifconfig, such as eth0) indicates the native IP bound by the framework. Note that it cannot be 127.0.0.1

REBUILD: Whether to rebuild the database is usually false. If there is an error in the intermediate installation and you want to reset the database, you can set it to true

SLAVE: slave node

MYSQL_USER: mysql user

MYSQL_PORT: mysql port

Map three directories to the host:
- -v/data/tars:/data/tars, include tars application log, web log, patch directory

**If you want to deploy multiple nodes, just execute docker run... On different machines. Pay attention to the parameter settings**

**Here, you must use --net=host to indicate that the docker and the host are on the same network**
**Note, mac not support --net=host**

## 3.5. mysql privilege

The above installation of MySQL requires root permission by default, but in some scenarios without root user or when root user has to input password interactively (such as Tencent cloud CDB), you can install it as follows:

- First, create users in MySQL (which may be assigned to you by the administrator), such as:admin
- The admin user has the following permissions :
```
GRANT, SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER, CREATE TABLESPACE
```
- execute install script

```
./linux-install.sh 192.168.7.153 tars2015 eth0 false true admin 3306

```

```
docker run -d --net=host -e MYSQL_HOST=xxxxx -e MYSQL_ROOT_PASSWORD=xxxxx \
        -eMYSQL_USER=admin -eMYSQL_PORT=3306 \
        -eREBUILD=false -eINET=enp3s0 -eSLAVE=false \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/framework
```

**During the actual framework installation process, another user will be created and used to connect DB. Please refer to the tars-install.sh script**


## 3.6. Core module

Tars Framework is ultimately made up of several core modules, such as:
```
[root@VM-0-7-centos deploy]# ps -ef | grep app/tars | grep -v grep
root       368     1  0 09:20 pts/0    00:00:25 /usr/local/app/tars/tarsregistry/bin/tarsregistry --config=/usr/local/app/tars/tarsregistry/conf/tars.tarsregistry.config.conf
root      9245 32687  0 09:29 ?        00:00:13 /usr/local/app/tars/tarsstat/bin/tarsstat --config=/usr/local/app/tars/tarsnode/data/tars.tarsstat/conf/tars.tarsstat.config.conf
root     32585     1  0 09:20 pts/0    00:00:10 /usr/local/app/tars/tarsAdminRegistry/bin/tarsAdminRegistry --config=/usr/local/app/tars/tarsAdminRegistry/conf/tars.tarsAdminRegistry.config.conf
root     32588     1  0 09:20 pts/0    00:00:20 /usr/local/app/tars/tarslog/bin/tarslog --config=/usr/local/app/tars/tarslog/conf/tars.tarslog.config.conf
root     32630     1  0 09:20 pts/0    00:00:07 /usr/local/app/tars/tarspatch/bin/tarspatch --config=/usr/local/app/tars/tarspatch/conf/tars.tarspatch.config.conf
root     32653     1  0 09:20 pts/0    00:00:14 /usr/local/app/tars/tarsconfig/bin/tarsconfig --config=/usr/local/app/tars/tarsconfig/conf/tars.tarsconfig.config.conf
root     32687     1  0 09:20 ?        00:00:22 /usr/local/app/tars/tarsnode/bin/tarsnode --locator=tars.tarsregistry.QueryObj@tcp -h 172.16.0.7 -p 17890 --config=/usr/local/app/tars/tarsnode/conf/tars.tarsnode.config.conf
root     32695     1  0 09:20 pts/0    00:00:09 /usr/local/app/tars/tarsnotify/bin/tarsnotify --config=/usr/local/app/tars/tarsnotify/conf/tars.tarsnotify.config.conf
root     32698     1  0 09:20 pts/0    00:00:14 /usr/local/app/tars/tarsproperty/bin/tarsproperty --config=/usr/local/app/tars/tarsproperty/conf/tars.tarsproperty.config.conf
root     32709     1  0 09:20 pts/0    00:00:12 /usr/local/app/tars/tarsqueryproperty/bin/tarsqueryproperty --config=/usr/local/app/tars/tarsqueryproperty/conf/tars.tarsqueryproperty.config.conf
root     32718     1  0 09:20 pts/0    00:00:12 /usr/local/app/tars/tarsquerystat/bin/tarsquerystat --config=/usr/local/app/tars/tarsquerystat/conf/tars.tarsquerystat.config.conf
```

- for master node: tarsnode tars-web must be alive, Other Tars services will be automatically pulled up by tarsnode
- For the slave node: tarsnode to be alive, other Tars services will be pulled by tarsnode
- Tars web is a service implemented by nodejs, which consists of two services. For details, see the following chapters
- To ensure that the core service is started, it can be controlled through monitor.sh and configured in crontab

add to contab

```
* * * * * /usr/local/app/tars/tarsnode/util/monitor.sh 
```

# 4. <a id="chapter-4"></a>Tars-web Description

## 4.1 Module description

After the tars framework is deployed, the tar web will be installed on the host node (the slave node will not be installed). The tar web is implemented by nodejs and consists of two services

view the modules of the tars Web:

```
pm2 list
```

Output (web < v2.4.7):
```
[root@8a17fab70409 data]# pm2 list
┌────┬─────────────────────────┬─────────┬─────────┬──────────┬────────┬──────┬──────────┬──────────┬──────────┬──────────┬──────────┐
│ id │ name                    │ version │ mode    │ pid      │ uptime │ ↺    │ status   │ cpu      │ mem      │ user     │ watching │
├────┼─────────────────────────┼─────────┼─────────┼──────────┼────────┼──────┼──────────┼──────────┼──────────┼──────────┼──────────┤
│ 0  │ tars-node-web           │ 0.2.0   │ fork    │ 1602     │ 2m     │ 0    │ online   │ 0.1%     │ 65.1mb   │ root     │ disabled │
│ 1  │ tars-user-system        │ 0.1.0   │ fork    │ 1641     │ 2m     │ 0    │ online   │ 0.1%     │ 60.1mb   │ root     │ disabled │
└────┴─────────────────────────┴─────────┴─────────┴──────────┴────────┴──────┴──────────┴──────────┴──────────┴──────────┴──────────┘
```

Output (web >= v2.4.7):
```
[root@8a17fab70409 data]# pm2 list
┌────┬─────────────────────────┬─────────┬─────────┬──────────┬────────┬──────┬──────────┬──────────┬──────────┬──────────┬──────────┐
│ id │ name                    │ version │ mode    │ pid      │ uptime │ ↺    │ status   │ cpu      │ mem      │ user     │ watching │
├────┼─────────────────────────┼─────────┼─────────┼──────────┼────────┼──────┼──────────┼──────────┼──────────┼──────────┼──────────┤
│ 0  │ tars-node-web           │ 2.4.7   │ fork    │ 1602     │ 2m     │ 0    │ online   │ 0.1%     │ 65.1mb   │ root     │ disabled │
└────┴─────────────────────────┴─────────┴─────────┴──────────┴────────┴──────┴──────────┴──────────┴──────────┴──────────┴──────────┘
```

**If PM2 cannot be found, the environment variable does not take effect. Please execute: source /etc/profile. This file will be written during installation**

Due to permission problem under Ubuntu, if PM2 is executed in error (environment variable is not effective):
```
sudo -s source /etc/profile
sudo chown ubuntu:ubuntu /home/ubuntu/.pm2/rpc.sock /home/ubuntu/.pm2/pub.sock
pm2 list
```

### tars-web < v2.4.7

**Tars web consists of two modules**

- tars-node-web: Tars Web homepage service, default binding 3000 port, Source code corresponding web directory
- tars-user-system: The authority management service is responsible for managing all relevant authorities, and is bound to port 3001 by default, Source code corresponding web/demo directory

tars-node-web calls tars-user-system to complete the relevant permission verification

Both web and demo are implemented by nodejs + Vue. If the viewing module in PM2 list fails to start, you can start it manually to locate the prompt:

**The web is implemented by nodejs + Vue. The final installation and operation directory is as follows:**

```
/usr/local/app/web
```

If pm2 list shows that tars-node-web and tars-user-system fail to start, you can enter the directory to locate the problem:

```
cd /usr/local/app/web/demo; npm run start
cd /usr/local/app/web; npm run start
```

npm run start starts the service. You can observe the output of the console. If there is a problem, you will be prompted

**Suggestions for formal operation: pm2 start tars-node-web; pm2 start tars-user-system**


### tars-web >= v2.4.7

**Tars web one modules**

- tars-node-web: Tars Web homepage service, default binding 3000 port, Source code corresponding web directory
tars-node-web calls tars-user-system to complete the relevant permission verification

Web are implemented by nodejs + Vue. If the viewing module in PM2 list fails to start, you can start it manually to locate the prompt:

**The web is implemented by nodejs + Vue. The final installation and operation directory is as follows:**

```
/usr/local/app/web
```

If pm2 list shows that tars-node-web fail to start, you can enter the directory to locate the problem:

```
cd /usr/local/app/web; npm run start
```

npm run start starts the service. You can observe the output of the console. If there is a problem, you will be prompted

**Suggestions for formal operation: pm2 start tars-node-web **

### Auto Start Tars

Note that after restarting the machine, PM2 module will be lost. Please add the following statement to startup
(for example: /etc/rc.local):
```
/usr/local/app/web/tars-start.sh
```

If the web page cannot be opened after installation, please refer to [web](web.md), check the problem section and locate the problem.

# 5. <a id="chapter-5"></a>Scripts

The framework comes with scripts to control the start and stop of services, such as:

- Start Tars Framework: /usr/local/app/tars/tars-start.sh
- Stop Tars Framework: /usr/local/app/tars/tars-stop.sh
- Start & Stop one server:
>- * /usr/local/app/tars/xxxx/util/start.sh
>- * /usr/local/app/tars/xxxx/util/stop.sh

Note:
- In the core service of the framework, tarsnode must be alive. It will monitor the life and death of other components. Once other components crash, it will automatically pull up
- Web components are monitored by pm2
- After the machine that deployed the framework restarts, it can execute ```/usr/local/app/tars/tars-start.sh``` to restart all servers
- Tarsnode monitoring can be performed regularly in crontab : ```/usr/local/app/tars/tarsnode/util/check.sh```
- The node machine with tarsnode deployed only needs to monitor tarsnode

# 6. <a id="chapter-6"></a>Proposed deployment plan

Although this chapter introduces the scheme of the source code deployment tar framework, in practice, it is not recommended to deploy the source code, which will lead to more trouble in upgrading and maintenance. The following describes the key deployment scheme in the actual use process:

- Adopt docker deployment framework (refer to relevant documents), deploy two machines, master-slave mode, and enable --net=host mode
- The physical deployment scheme is adopted for the node machine, and the tarsnode can be connected to the framework
- In the case of upgrading the framework, you can upgrade the docker, stop the old docker, start the new docker, and pay attention not to select rebuild dB for participation!!!
- Tarsnode can be upgraded remotely on the web. Normally, tarsnode does not need to be upgraded unless there is major function optimization (it will be upgraded automatically in the future)
- Tarslog needs a large hard disk machine. After the framework is deployed, it is recommended to expand the tarslog service of the framework to the node machines of other large hard disks
- If you use windows, you can consider using docker for framework deployment and tarsnode for windows node machine deployment

If you are familiar with k8s, you can also deploy tars on k8s [see k8s deployment document](k8s-docker-1.md)
