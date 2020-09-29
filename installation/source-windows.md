# Directory
> * [Dependent Environments](#chapter-1)  
> * [Install develop environment for C++](#chapter-2)  
> * [Tars framework Installation](#chapter-3)  
> * [Tars-web Description ](#chapter-4)

This document describes the steps to deploy, run, and test Tars framework.

If you use Tars for the production environment, the deployment steps are similar, but you need pay attention to fault-tolerance. You can join us for discussion in the QQ group code 579079160.  
 
## 1. <a id="chapter-1"></a>Dependent environments  
  
Software | Software requirements  
------|--------  
windows: | >=win7  
cmake version: | 3.2 or later（Dependent c++ framework tools）  
mysql version: | 5.6 or later（dependency of framework running）  
nvm version: | 0.35.1 or later
node version: | 12.13.0 or later
  
Hardware requirements: a machine running Windows.  

## 1.1. download and install build package dependency and tools

- Install nodejs, nodejs web site: https://nodejs.org/en/

- Install vs(vs2019), vs2019自带cmake, Vs2019 comes with its own cmake. For example, the cmake path of my machine is:
C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin

- If your vs is lower than vs2019, install cmake, cmake web site: https://cmake.org/

- Install git

**Please make sure that your nodejs, cmake, git are in the system environment variables, that is, they can be run on the command line: : cmake, node, git**

## 1.2. Mysql installation

when deploy the tars, your MySQL can be installed on other machines.

Tars framework installation needs to read and write data in mysql, so you need to install mysql. If you already have mysql, you can ignore this step.

For MySQL installation, please refer to[mysql installation](mysql.md)

Install MySQL on windows, please search the tutorial by yourself.

# 2. <a id="chapter-2"></a>Tars C + + development environment (required for source installation framework)

**The source code installation framework needs to do this step. If you only write services in C + +, just download the tarscpp code**

**Note that our operations are all performed on the command line. Please ensure that environment variables such as cmake git nodejs vs take effect**

Download tarsframework source code

```
cd ${source_folder}
git clone https://github.com/TarsCloud/TarsFramework.git --recursive
```

Open the windows console (you may need to run as an administrator) and enter the build source directory

```
cd TarsFramework
git submodule update --remote --recursive
cd build
cmake ..
cmake --build . --config RelWithDebInfo
cmake --build . --config RelWithDebInfo  --target install
```

By default, compiling tars will automatically download MySQL client source code, and compile libmyql.dll

**The path of installation package after compilation is c:\tars\cpp, that is, the compiled framework & the installation script is in this directory**
**The default path after installation is c:\tars-install, which is the path after installation**

After install, the dependent libraries and header files will also be installed in this directory(/usr/local/tars/cpp/thirdparty). If SSL or nghttp2 is enabled, install is the same

If you want to install on different path:  
```  
**modify tarscpp/cmake/Common.cmake**  
**modify TARS_PATH in tarscpp/servant/makefile/makefile.tars**  
**modify TARS_PATH in tarscpp/servant/makefile/tars-tools.cmake**
**modify DEMO_PATH in tarscpp/servant/script/create_tars_server.sh**  
```  

# 3 <a id="chapter-3"></a>Tars framework Installation 

## 3.1. Installation

**Attentions:**

- During the installation process, because the tar web relies on nodejs, it will automatically download nodejs, NPM, PM2 and related dependencies, and set the environment variables to ensure that nodejs takes effect
- The version of nodejs currently downloads v12.13.0 by default
- If you have nodejs installed, you'd better uninstall it first

**Note: the compilation and installation of tarsframework needs to be completed**

Download tarsweb and copy to c:\tars\cpp\deploy  (change dir name TarsWeb to web)

```
cd c:\tars\cpp\deploy
git clone https://github.com/TarsCloud/TarsWeb.git web
```

for example, this is my files in c:\tars\cpp\deploy
```
 C:\tars\cpp\deploy 的目录

2020/03/31  17:20    <DIR>          .
2020/03/31  17:20    <DIR>          ..
2020/03/24  11:38           443,392 busybox.exe
2020/03/24  11:38             1,981 centos7_base.repo
2020/03/28  15:43             3,273 docker-init.sh
2020/03/28  15:43               319 docker.sh
2020/03/28  15:43             2,365 Dockerfile
2020/03/28  14:33    <DIR>          framework
2020/03/30  12:45         4,099,584 libmysql.dll
2020/03/28  15:43             4,829 linux-install.sh
2020/03/30  12:51           110,592 mysql-tool.exe
2020/03/28  15:43             1,349 tar-server.sh
2020/03/30  12:52           635,904 tars-client.exe
2020/03/30  11:28            16,885 tars-install.sh
2020/03/24  11:38               322 tars-stop.sh
2020/03/30  14:23    <DIR>          tools
2020/03/30  14:14    <DIR>          web
2020/03/28  15:43             3,732 web-install.sh
2020/03/28  15:43             1,543 windows-install.sh
              14 个文件      5,326,070 字节
               5 个目录 196,844,564,480 可用字节
```
## 3.2. Basic description of TarsFramework

The framework can be deployed on a single machine or multiple machines. Multiple machines are in the mode of one master and many slaves. Generally, one master and one slave are enough:

- There can only be one master node and multiple slave nodes
- The master node will install by default: tarsadminregistry, tarspatch, tarsweb, tarstat, tarsproperty and tarslog. These services will not be installed on the slave node
- The tarslog can only be a single point, otherwise the remote logs will be scattered on multiple machines
- In principle, tarspatch and tarsweb can be multi-point. If they are deployed to multi-point, the c:\tars-install\patches directory needs to be shared among multiple computers (for example, through NFS). Otherwise, the service cannot be published normally
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

## 3.3. deploy

enter c:\tars\cpp\deploy, run:
```
busybox.exe sh ./windows-install.sh MYSQL_HOST MYSQL_ROOT_PASSWORD HOSTIP REBUILD(false[default]/true) SLAVE(false[default]/true) MYSQL_USER MYSQL_PORT
```

MYSQL_HOST: mysql ip address

MYSQL_ROOT_PASSWORD: mysql root password, note that root should not have special characters, for example: !, otherwise, there is a problem with shell script recognition, because it is a special character.

HOSTIP: local host ip. Note that it cannot be 127.0.0.1

REBUILD: Whether to rebuild the database is usually false. If there is an error in the intermediate installation and you want to reset the database, you can set it to true

SLAVE: slave node

For example, install three machines and one mysql(suppose: Master [192.168.7.151], slave [192.168.7.152], MySQL: [192.168.7.153])

Execute on the master node (192.168.7.151)

```
busybox.exe sh ./windows-install.sh 192.168.7.153 tars2015 192.168.7.151 false false root 3306
```

Execute on the slave node (192.168.7.152)

```
busybox.exe sh ./windows-install.sh 192.168.7.153 tars2015 192.168.7.152 false true root 3306
```

Refer to screen output for errors during execution. If there is an error, it can be executed repeatedly (usually download resource error)

## 3.4. mysql privilege

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

**During the actual framework installation process, another user will be created and used to connect dB. Please refer to the tars-install.sh script**

## 3.5. Core module

Tars Framework is ultimately made up of several core modules, such as:
```
C:\tars\cpp\deploy>busybox.exe ps | busybox.exe grep tars
14592 14400  0:00  5:05   tarsAdminRegist
 8892 14520  0:00  5:02   tarsregistry.ex
 8524  7916  0:00  5:01   tarsconfig.exe
 9400  9624  0:01  4:59   tarsnode.exe
10488  5348  0:00  4:57   tarsnotify.exe
10460  8356  0:01  4:56   tarsproperty.ex
 8536  4384  0:00  4:48   tarsqueryproper
 1268 13708  0:00  4:45   tarsquerystat.e
 7308 10560  0:00  4:37   tarsstat.exe
11760 14236  0:00  4:34   tarslog.exe
 4180 14428  0:00  4:32   tarspatch.exe
```

- for master node: tarsnode tars-web must be alive, Other Tar services will be automatically pulled up by tarsnode
- For the slave node: tarsnode to be alive, other Tars services will be pulled by tarsnode
- Tars web is a service implemented by nodejs, which consists of two services. For details, see the following chapters
- To ensure that the core service is started, it can be controlled through c:\tars-install\tars\tarsnode\util\monitor.bat, Set to windows scheduled task

**If you configure check.sh, you don't need to configure the tarsnode monitoring in the following chapters**

# 4. <a id="chapter-4"></a>Tars-web Description

## 4.1 Module description

After the tars framework is deployed, the tar web will be installed on the host node (the slave node will not be installed). The tar web is implemented by nodejs and consists of two services

view the modules of the tar Web:

```
pm2 list
```

### Output(web < v2.4.7):
```
[root@8a17fab70409 data]# pm2 list
┌────┬─────────────────────────┬─────────┬─────────┬──────────┬────────┬──────┬──────────┬──────────┬──────────┬──────────┬──────────┐
│ id │ name                    │ version │ mode    │ pid      │ uptime │ ↺    │ status   │ cpu      │ mem      │ user     │ watching │
├────┼─────────────────────────┼─────────┼─────────┼──────────┼────────┼──────┼──────────┼──────────┼──────────┼──────────┼──────────┤
│ 0  │ tars-node-web           │ 0.2.0   │ fork    │ 1602     │ 2m     │ 0    │ online   │ 0.1%     │ 65.1mb   │ root     │ disabled │
│ 1  │ tars-user-system        │ 0.1.0   │ fork    │ 1641     │ 2m     │ 0    │ online   │ 0.1%     │ 60.1mb   │ root     │ disabled │
└────┴─────────────────────────┴─────────┴─────────┴──────────┴────────┴──────┴──────────┴──────────┴──────────┴──────────┴──────────┘
```

- tars-node-web: Tar Web homepage service, default binding 3000 port, Source code corresponding web directory

- tars-user-system: The authority management service is responsible for managing all relevant authorities, and is bound to port 3001 by default, Source code corresponding web/demo directory

tars-node-web calls tars-user-system to complete the relevant permission verification

Both web and demo are implemented by nodejs + Vue. If the viewing module in PM2 list fails to start, you can start it manually to locate the prompt:

**The web is implemented by nodejs + Vue. The final installation and operation directory is as follows:**


```
c:\tars-install\web
```

If pm2 list shows that tars-node-web and tars-user-system fail to start, you can enter the directory to locate the problem:

```
cd c:\tars-install\web\demo; npm run start
cd c:\tars-install\web; npm run start
```

### Output(web < >=2.4.7):
```
[root@8a17fab70409 data]# pm2 list
┌────┬─────────────────────────┬─────────┬─────────┬──────────┬────────┬──────┬──────────┬──────────┬──────────┬──────────┬──────────┐
│ id │ name                    │ version │ mode    │ pid      │ uptime │ ↺    │ status   │ cpu      │ mem      │ user     │ watching │
├────┼─────────────────────────┼─────────┼─────────┼──────────┼────────┼──────┼──────────┼──────────┼──────────┼──────────┼──────────┤
│ 0  │ tars-node-web           │ 0.2.0   │ fork    │ 1602     │ 2m     │ 0    │ online   │ 0.1%     │ 65.1mb   │ root     │ disabled │
└────┴─────────────────────────┴─────────┴─────────┴──────────┴────────┴──────┴──────────┴──────────┴──────────┴──────────┴──────────┘
```

- tars-node-web: Tar Web homepage service, default binding 3000 port, Source code corresponding web directory

tars-node-web calls tars-user-system to complete the relevant permission verification

Web is implemented by nodejs + Vue. If the viewing module in PM2 list fails to start, you can start it manually to locate the prompt:

**web >= v2.4.7, only tars-node-web exists **

**The web is implemented by nodejs + Vue. The final installation and operation directory is as follows:**

```
c:\tars-install\web
```

If pm2 list shows that tars-node-web and tars-user-system fail to start, you can enter the directory to locate the problem:

```
cd c:\tars-install\web; npm run start
```

npm run start starts the service. You can observe the output of the console. If there is a problem, you will be prompted

**Suggestions for formal operation: pm2 start tars-node-web**

If the web page cannot be opened after installation, please refer to [web](web.md), check the problem section and locate the problem.


