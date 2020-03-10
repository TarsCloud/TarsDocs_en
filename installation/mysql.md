# Mysql Installation

## Directory

> * [Intro](mysql.md#chapter-1)
> * [Install by source](mysql.md#chapter-2)
> * [Install by yum](mysql.md#chapter-3)
> * [Install by docker](mysql.md#chapter-4)

## 1 Intro <a id="chapter-1"></a>

The data of the current version of the tars framework is ultimately stored in mysql, so MySQL needs to be installed.

There are several ways to install mysql. You can choose one of them.

## 2 Install by source <a id="chapter-2"></a>

This step installs mysql via source code in order to set up configurations manually.  

Before installation, check whether ncurses and zlib have been installed. Execute these commands if not exist:  

```text
yum install -y ncurses-devel zlib-devel cmake make gcc gcc-c++
```

set the installation directory, switch to user root.  

```text
cd /usr/local
mkdir mysql-5.6.26
chown ${owner}:${owner} ./mysql-5.6.26
ln -s /usr/local/mysql-5.6.26 /usr/local/mysql
```

Download mysql source (mysql-5.6.26), set charset to utf-8.  

```text
cd ${mysql dir}
wget https://dev.mysql.com/get/Downloads/MySQL-5.6/mysql-5.6.26.tar.gz
tar -zxvf mysql-5.6.26.tar.gz
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql-5.6.26 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DMYSQL_USER=mysql -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci
make
make install
```

**Attention: If you use C++ to develop Tars service, please compile mysql as static library**


Now you can compile Tars framework of C++ version.  
  
If you need build runtime environment for Tars framework, pelease switch to mysql administrator user and config mysql with following steps:  

**The following script has delete action \(rm -rf /usr/local/mysql/data\), please note!!**

```bash  
yum install perl  
cd /usr/local/mysql  
useradd mysql  
rm -rf /usr/local/mysql/data  
mkdir -p /data/mysql-data  
ln -s /data/mysql-data /usr/local/mysql/data  
chown -R mysql:mysql /data/mysql-data /usr/local/mysql/data  
cp support-files/mysql.server /etc/init.d/mysql  
yum install -y perl-Module-Install.noarch  
perl scripts/mysql_install_db --user=mysql  
vim /usr/local/mysql/my.cnf  
```  
Here is an example of my.cnf:  
**Tips: As the system will load /etc/  
rm -rf /etc/my.cnf  
my.cnf first,you may delect the /etc/my.cnf or copy the essential information from the following example and paste to /ect/my.cnf. Otherwise it will not work.  
  
```bash 

# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
innodb_buffer_pool_size = 128M

# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
log_bin

# These are commonly set, remove the # and set as required.
basedir = /usr/local/mysql
datadir = /usr/local/mysql/data
# port = .....
# server_id = .....
socket = /tmp/mysql.sock

bind-address=${your machine ip}

# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
join_buffer_size = 128M
sort_buffer_size = 2M
read_rnd_buffer_size = 2M

sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
```

**Note that change the bind address to the IP address of the deployment machine IP**

Start mysql

```text
service mysql start
chkconfig mysql on
```

Stop mysql

```text
service mysql stop
```

Add mysql execution path to environment variable for path.  

```text
vim /etc/profile
PATH=$PATH:/usr/local/mysql/bin
export PATH
```

Modify root's password  

```text
./bin/mysqladmin -u root password 'root@appinside'
./bin/mysqladmin -u root -h ${主机名} password 'root@appinside'
```

Add mysql dynamic library path to environment variable for path.  

```text
vim /etc/ld.so.conf
/usr/local/mysql/lib/
ldconfig
```

The master-slave configuration for mysql can be found in the Internet.  

Grant authority to master  

```text
GRANT REPLICATION SLAVE ON *.* to 'mysql-sync'@'%' identified by 'sync@appinside'
```

Configure slave for replication:  

```text
change master to master_host='${master Ip}',master_user='mysql-sync',master_password='sync@appinside' ,master_log_file='iZ94orl0ix4Z-bin.000004',master_log_pos=611;
stop slave
start slave
show master status\G;
show slave status\G;
```

## 3 Install by yum <a id="chapter-3"></a>

This step allows you to download the mysql official yum repository.
  
The install scripts named mysql_install_db.sh included in version 5.6 have been deleted after version 5.7. Therefore, here comes the yum installation.  

The installation via yum is an easier way but with that you cannot adjust configs manually. If that is not what you want, the source code method is recommended.  

You can download mysql using wget. Below is an example downloading version 5.7, but you can change it to the desired version.
```  bash
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm  
yum -y install mysql57-community-release-el7-10.noarch.rpm  
yum -y install mysql-community-server  
yum -y install mysql-devel  
```  
If you have problems to install mysql with the above step, add the new mysql repository to local server with this yum command and then re-run the previous commands.  

```  bash
sudo yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm  
```  

**Configure mysql  **

After installing mysql, start and check its status.  

```text
systemctl start  mysqld.service
systemctl status mysqld.service
```

mysql started and it's using port 3306 for the connection.  
  
Configure the mysql root password. mysql will generate a strong default password when it is started for the first time. The default password is shown in mysqld.log file. You can use the grep command below for showing the default mysql password.  

```text
grep "password" /var/log/mysqld.log
```

Connect to the mysql shell with the default password.  

You can't operate until changing the password. If your mysql version is later than 5.7, there are two ways to configure the strong password rules as follows:  

1. Set up a strong password  
2. Change the password rules as follows  
   
```  bash
set global validate_password_policy=0;  
set global validate_password_length=1;  
```  

Now replace the default password with a new password with a six-character minimum restriction.  

```  sql
ALTER USER 'root'@'localhost' IDENTIFIED BY '${your passwd}';  
flush privileges;  
```  
  
## 4 Install by docker <a id="chapter-4"></a>

For CentOS installation of docker, please refer to [docker](docker.md)

Use docker installation to install MySQL \(currently only Linux is considered, time and local synchronization \)


```bash
docker pull mysql:5.6
docker run --name mysql --net=host -e MYSQL_ROOT_PASSWORD='root@appinside' -d -p 3306:3306 \
        -v/etc/localtime:/etc/localtime \
        -v /data/mysql-data:/var/lib/mysql mysql:5.6
```

**Note: --net=host indicates that the docker network is the same as the local one**

