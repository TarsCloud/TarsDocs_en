# Directory
> * [Dependent Environments](#chapter-1)  
> * [Install develop environment for C++](#chapter-2)  
> * [Tars framework Installation](#chapter-3)  
> * [Tars-web Description ](#chapter-4)

This document describes the steps to deploy, run, and test Tars framework.

If you use Tars for production environment, the deployment steps are similar, but you need pay attention to fault-tolerance. You can join us for discussion in the QQ group code 579079160.  
 
## 1. <a id="chapter-1"></a>Dependent environments  
  
Software | Software requirements  
------|--------  
linux kernel version: | 2.6.18 or later (Dependent OS)  
gcc version: | 4.8.2 or later、glibc-devel（Dependent c++ framework tools）  
bison version: | 2.5 or later（Dependent c++ framework tools）  
flex version: | 2.5 or later（Dependent c++ framework tools）  
cmake version: | 2.8.8 or later（Dependent c++ framework tools）  
mysql version: | 4.1.17 or later（dependency of framework running）  
rapidjson version: | 1.0.2 or later（dependency of C++ framework）  
nvm version: | 0.35.1 or later（Dependent web management system, auto install while deploying）  
node version: | 12.13.0 or later（Dependent web management system, auto install while deploying）  
  
Hardware requirements: a machine running Linux.  

## 1.2. MySQL dependency library installation

The compilation of tars code depends on MySQL header file and static library, and the path is as follows:

- Include: /usr/local/mysql/include
- Lib: /usr/local/mysql/lib

If MySQL header file and static library already exist in the system, you can skip the next step, otherwise it is recommended to:

```
rpm -ivh https://repo.mysql.com/mysql57-community-release-el7.rpm
yum install -y mysql-devel 
mkdir -p /usr/local/mysql && ln -s /usr/lib64/mysql /usr/local/mysql/lib && ln -s /usr/include/mysql /usr/local/mysql/include && echo "/usr/local/mysql/lib/" >> /etc/ld.so.conf && ldconfig 
```

## 1.3. MySQL client installation

Deployment of tars environment depends on MySQL client

```
which mysql
```

If the MySQL client does not exist, execute:

```
rpm -ivh https://repo.mysql.com/mysql57-community-release-el7.rpm
yum install -y mysql 
```

## 1.4. Mysql installation

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
cd ${source_folder}/TarsFramework/build
chmod u+x build.sh
./build.sh prepare
./build.sh all
```

**MySQL development library path used by default at compile time:include path: /usr/local/mysql/include，lib path: /usr/local/mysql/lib/**


Be aware that the default mysql lib path that Tars use is /usr/local/mysql/ . If mysql is installed in a different path, please modify the files `TarsFramework/CMakeLists.txt` and `TarsFramework/tarscpp/CMakeLists.txt` directory before compiling. (You might change the mysql paths to:"/usr/include/mysql";"/usr/lib64/mysql")  

Recompile if needed.  
```
./build.sh cleanall
./build.sh all
```

Change to user root and create the installation directory.  
```
cd /usr/local
mkdir tars
chown ${normal user}:${normal user} ./tars/  
 
Installation:
  
```  bash
cd ${source_folder}/build  
./build.sh install or make install  
```  

**The default install path is /usr/local/tars/cpp。**  
  
If you want to install on different path:  
```  
**modify tarscpp/CMakeLists.txt**  
**modify TARS_PATH in tarscpp/servant/makefile/makefile.tars**  
**modify DEMO_PATH in tarscpp/servant/script/create_tars_server.sh**  
```  

# 3 <a id="chapter-3"></a>Tars framework Installation 

## 3.1. 框架安装模式

**There are two Installation mode of TarsFramework:**

- centos7 automatic deploy, During the installation process, the network needs to download resources from the outside
- Make a docker image to complete the installation. The process of making a docker requires network download resources, but no external network is needed to start and run the docker image

**Tars Framework Installation Attentions:**

- During the installation process, because the tar web relies on nodejs, it will automatically download nodejs, NPM, PM2 and related dependencies, and set the environment variables to ensure that nodejs takes effect
- The version of nodejs currently downloads v12.13.0 by default
- If you have nodejs installed, you'd better uninstall it first

**Note: the compilation and installation of tarsframework needs to be completed**

Download tarsweb and copy to /usr/local/tars/cpp/deploy  (change dir name TarsWeb to web)

```
git clone https://github.com/TarsCloud/TarsWeb.git
mv TarsWeb web
cp -rf web /usr/local/tars/cpp/deploy/
```

for example, this is my files in /usr/local/tars/cpp/deploy:
```
[root@cb7ea6560124 deploy]# ls -l
total 52
-rw-rw-r-- 1 tars tars 1923 Nov  2 17:31 centos7_base.repo
-rwxrwxr-x 1 tars tars 1515 Nov  5 18:21 Dockerfile
-rwxrwxr-x 1 tars tars 2844 Nov  5 18:21 docker-init.sh
-rwxrwxr-x 1 tars tars  215 Nov  5 18:21 docker.sh
-rw-rw-r-- 1 tars tars  664 Nov  2 17:31 epel-7.repo
drwxrwxr-x 4 tars tars   30 Nov  2 17:31 framework
-rwxrwxr-x 1 tars tars 4599 Nov  8 09:41 linux-install.sh
-rw-rw-r-- 1 tars tars  191 Nov  2 17:31 MariaDB.repo
-rwxrwxr-x 1 tars tars  565 Nov  8 09:23 README.md
-rwxrwxr-x 1 tars tars  539 Nov  8 09:23 README.zh.md
-rwxrwxr-x 1 tars tars 9713 Nov  7 09:42 tars-install.sh
drwxrwxr-x 2 tars tars   44 Nov  7 10:04 tools
drwxr-xr-x 11 tars tars  4096 Oct 31 11:01 web
```

## 3.2. Basic description of TarsFramework

The framework can be deployed on a single machine or multiple machines. Multiple machines are in the mode of one master and many slaves. Generally, one master and one slave are enough:

- There can only be one master node and multiple slave nodes
- The master node will install by default: tarsadminregistry, tarspatch, tarsweb, and tarslog. These services will not be installed on the slave node
- The tarsAdminRegistry can only be a single point because it has publishing status)
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

**If not, manually execute in centos: source ~/.bashrc or in ubuntu: source ~/.profile**
 
## 3.3. (centos or ubuntu) deploy

enter /usr/local/tars/cpp/deploy, run:
```
chmod a+x linux-install.sh
./linux-install.sh MYSQL_HOST MYSQL_ROOT_PASSWORD INET REBUILD(false[default]/true) SLAVE(false[default]/true)
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

## 3.4. Make docker

Objective: make the framework into a docker, and start the docker

make docker:
```
chmod a+x docker.sh
./docker.sh v1
```
docker finished, you can see the docker:tar-docker:v1

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
        tars-docker:v1
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

## 3.5. mysql权限问题

The above installation of MySQL requires root permission by default, but in some scenarios without root user or when root user has to input password interactively (such as Tencent cloud CDB), you can install it as follows:

- First, create users in MySQL (which may be assigned to you by the administrator), such as:admin
- The admin user has the following permissions :
```
SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER, CREATE TABLESPACE
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
        tars-docker:v
```

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

- for master node: tarsAdminRegistry tarsnode tarsregistry tars-web must be alive, Other Tar services will be automatically pulled up by tarsnode
- For the slave node: tarsnode tarsregistry to be alive, other Tars services will be pulled by tarsnode起
- Tars web is a service implemented by nodejs, which consists of two services. For details, see the following chapters
- To ensure that the core service is started, it can be controlled through check.sh and configured in crontab

master:(add to contab)

```
* * * * * /usr/local/app/tars/check.sh master
```

slave:(add to contab)
```
* * * * * /usr/local/app/tars/check.sh 
```

**If you configure check.sh, you don't need to configure the tarsnode monitoring in the following chapters**

# 4. <a id="chapter-4"></a>Tars-web Description

## 4.1 Module description

After the tars framework is deployed, the tar web will be installed on the host node (the slave node will not be installed). The tar web is implemented by nodejs and consists of two services

view the modules of the tar Web:

```
pm2 list
```

Output:
```
[root@8a17fab70409 data]# pm2 list
┌────┬─────────────────────────┬─────────┬─────────┬──────────┬────────┬──────┬──────────┬──────────┬──────────┬──────────┬──────────┐
│ id │ name                    │ version │ mode    │ pid      │ uptime │ ↺    │ status   │ cpu      │ mem      │ user     │ watching │
├────┼─────────────────────────┼─────────┼─────────┼──────────┼────────┼──────┼──────────┼──────────┼──────────┼──────────┼──────────┤
│ 0  │ tars-node-web           │ 0.2.0   │ fork    │ 1602     │ 2m     │ 0    │ online   │ 0.1%     │ 65.1mb   │ root     │ disabled │
│ 1  │ tars-user-system        │ 0.1.0   │ fork    │ 1641     │ 2m     │ 0    │ online   │ 0.1%     │ 60.1mb   │ root     │ disabled │
└────┴─────────────────────────┴─────────┴─────────┴──────────┴────────┴──────┴──────────┴──────────┴──────────┴──────────┴──────────┘
```

**If PM2 cannot be found, the environment variable does not take effect. Please execute: CentOS: source ~ /. Bashrc or Ubuntu: source ~ /. Profile first. This file will be written during installation**

- tars-node-web: Tar Web homepage service, default binding 3000 port, Source code corresponding web directory

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

If the web page cannot be opened after installation, please refer to [web](web.md), check the problem section and locate the problem.


