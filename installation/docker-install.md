# Docker Installation

## Directory

> * [Intro]()
> * [How to Install Docker]()

## 1 Intro <a id="chapter-1"></a>

Because the use of docker will be involved in the following chapters, this section briefly introduces how to install the docker environment in Centos.

For more information about docker, please visit the official website of docker.

If you do not deploy the tars environment with docker, ignore this step


## 2 How to Install Docker <a id="chapter-2"></a>

**This article only introduces the installation of docker environment in CentOS**

Install Docker in Centos:

```bash
sudo su
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install -y docker-ce 
systemctl start docker
systemctl enable docker
```

After installation, check the docker version:

```sh docker version```

