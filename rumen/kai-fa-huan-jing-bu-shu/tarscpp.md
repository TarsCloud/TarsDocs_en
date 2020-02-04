# Directory
> * [Intro](#chapter-1)
> * [Dependent environment](#chapter-2)
> * [Construction of development environment](#chapter-3)

# 1 <a id="chapter-1"></a>Intro

This section mainly introduces the development environment required for the development and compilation of the tars CPP service based on Centos7.

**Note: if Tars has been compiled and deployed on the machine before, the development environment of tars CPP will work automatically**

# 2 <a id="chapter-2"></a>Dependent environment

| Software | Software requirements |
| :--- | :--- |
| linux kernel:   | >=2.6.18 |
| gcc version:    | >=4.8、glibc-devel |
| bison version:  | >=2.5|
| flex version:   | >=2.5   |
| cmake version： | >=2.8.8|
| mysql version： | >=4.1.17|


## 2.1. Download and install build package dependency

Source compilation needs to be installed: gcc, glibc, bison, flex, cmake

for example，in Centos7:
```
yum install glibc-devel gcc gcc-c++ bison flex cmake
```

## 2.2. MySQL dependency library installation

The compilation of tars code depends on MySQL header file and static library, and the path is as follows:

- Include: /usr/local/mysql/include
- Lib: /usr/local/mysql/lib

If MySQL header file and static library already exist in the system, you can skip the next step, otherwise it is recommended to:

```
rpm -ivh https://repo.mysql.com/mysql57-community-release-el7.rpm
yum install -y mysql-devel 
mkdir -p /usr/local/mysql && ln -s /usr/lib64/mysql /usr/local/mysql/lib && ln -s /usr/include/mysql /usr/local/mysql/include && echo "/usr/local/mysql/lib/" >> /etc/ld.so.conf && ldconfig 
```

**Note: Tars uses MySQL static link by default to avoid requiring MySQL dynamic library on each machine**

# <a id="chapter-3"></a>Construction of development environment

```text
git clone https://github.com/TarsCloud/TarsCpp.git --recursive
cd TarsCpp
cmake .
make
make install
```

At this point, the compilation environment of tars CPP has been completed, and the next step is to implement the tars service of CPP.