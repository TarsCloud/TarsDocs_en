
# Directory
> * [Deploy TarsNode](#chapter-1)
> * [Web platform online deployment](#chapter-2)
> * [Script deployment in node machine](#chapter-3)
> * [Docker deployment](#chapter-4)

# 1 <a id="chapter-1"></a>Deploy TarsNode

After the tars framework is deployed, if you want the business service to be published to the node server, you need to connect the node server to the framework. This step is to install tarsnode on the node server.

There are three modes to install tarsnode:
- Web platform online deployment
- Script deployment in node machine
- Docker deployment

The user running tarsnode may not be root. After installation, you can switch the directory to another user, for example, the current user is root, and you want to switch to the tars user.

```
#Under root, mask crontab and comment out tarsnode monitoring
#* * * * * /usr/local/app/tars/tarsnode/util/monitor.sh

#Stop tarsnode (note that crontab monitoring should also be shielded, otherwise it will be automatically pulled up)
/usr/local/app/tarsnode/util/stop.sh

#Modify directory permissions
chown -R tars:tars /usr/local/app/tarsnode

##Switch to tars users
su tars

#Start
/usr/local/app/tarsnode/util/start.sh

#Add crontab monitor
* * * * * /usr/local/app/tars/tarsnode/util/monitor.sh
```

# 2 <a id="chapter-2"></a>Web platform online deployment

Web provides the function of online installation of tarsnode. When installing, you need to input the IP, password and other information of the node machine to complete the installation of automatic tarsnode (you need to add crontab to monitor tarsnode)

- When the tarsnode.tgz installation package is deployed, the installation script is automatically copied to the web/files directory
- If not, you need to generate tarsnode.tgz yourself, as follows
>- compile framework, make install
```
cd /usr/local/tars/cpp/framework/servers
tar czf tarsnode.tgz tarsnode
cp tarsnode.tgz yourweb/files
```

Configure a process monitoring in crontab to ensure that the tars framework service can be restarted after an exception occurs.
```
* * * * * /usr/local/app/tars/tarsnode/util/monitor.sh
```

**Note: the node machine needs to support the wget command, otherwise the tarsnode cannot be pulled from the web to the local machine**

# 3 <a id="chapter-3"></a>Script deployment in node machine

Tarsnode can also be installed automatically on the node machine, provided that the node can access the web normally and the web supports online installation.

Run on node machine:
```
wget http://webhost/get_tarsnode?ip=xxx&runuser=root
chmod a+x get_tarsnode
./get_tarsnode
```

Parameter description:
- ip: local node machine ip
- runuser: Users running tarsnode

Complete the installation of tarsnode, and then add monitoring:

Configure a process monitoring in crontab to ensure that the tars framework service can be restarted after an exception occurs.
```
* * * * * /usr/local/app/tars/tarsnode/util/monitor.sh
```

# 4 <a id="chapter-4"></a>Docker deployment

If you want the business service to run in a docker, you can use this method:

```sh
docker pull tarscloud/tars-node:stable
```

```sh
docker run -d --net=host -eINET=eth0 -eWEB_HOST=xxxxx \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/tars-node:stable

#for example:
docker run -d --net=host -eINET=eth0 -eWEB_HOST=http://172.16.0.7:3000 \
        -v/data/tars:/data/tars \
        -v/etc/localtime:/etc/localtime \
        tarscloud/tars-node:stable     
```

**Note: http://172.16.0.7:3000 is the access address of web**

Script of making tarsnode docker refer to [Dockerfile](https://github.com/TarsCloud/TarsDocker/blob/master/tarsnode/Dockerfile)

The docker has built-in Java, nodejs and other runtime support, that is, Java, nodejs services can be published to the docker (the docker has installed JDK, node, PHP environment)

This method is usually used in k8s deployment. At this time, --net=host is not needed, and docker is managed by k8s

