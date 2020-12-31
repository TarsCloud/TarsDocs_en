# TarsCpp
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
| cmake version： | >=3.2|
| mysql version： | >=5.6|


## 2.1. Download and install build package dependency

Source compilation needs to be installed: gcc, glibc, bison, flex, cmake

for example，in Centos7:
```
yum install glibc-devel gcc gcc-c++ bison flex cmake
```

# 3 <a id="chapter-3"></a>Construction of development environment

```text
git clone https://github.com/TarsCloud/TarsCpp.git --recursive
cd TarsCpp
cmake .
make
make install
```

At this point, the compilation environment of tars CPP has been completed, and the next step is to implement the tars service of CPP.

if you want open ssl & http2 support:

```
cmake .. -DTARS_SSL=ON -DTARS_HTTP2=ON
make
make install
```

close support:
```
cmake .. -DTARS_SSL=OFF -DTARS_HTTP2=OFF
make
make install
```

Note that the demo services of examples are not compiled by default. If you want to compile these demo services, please:
```
cmake .. -DONLY_LIB=OFF
```
