# TarsBenchmark
> * [Intro](#chapter-1)
> * [Dependent environment](#chapter-2)
> * [Construction](#chapter-3)
> * [Quick release](#chapter-4)


# 1 <a id="chapter-1"></a>Intro

This section mainly introduces the development environment required for tb tool based on Centos7.

**Note: If the TarsCpp environment has been installed on this machine before, you need to upgrade to the latest TarsCpp version**

# 2 <a id="chapter-2"></a>Dependent environment

| Software | Software requirements |
| :--- | :--- |
| linux kernel:   | >=2.6.18 |
| gcc version:    | >=4.8、glibc-devel |
| bison version:  | >=2.5|
| flex version:   | >=2.5   |
| cmake version： | >=2.8.8|
| TarsCpp version: | >=2.4.0|

## 2.1. Download and install build package dependency


Source compilation needs to be installed: gcc, glibc, bison, flex, cmake

for example，in Centos7:
```
yum install glibc-devel gcc gcc-c++ bison flex cmake
```

## 2.2. Construction of TarsCpp development environment

```text
git clone https://github.com/TarsCloud/TarsCpp.git --recursive
cd TarsCpp
mkdir build
cd build
cmake ..
make
make install
```

Now the compilation environment of TarsCpp has been completed, and the tb can be constructed in the next step.


# 3 <a id="chapter-3"></a>Construction TarsBenchmark

The main compilation steps are as follows. After successful compilation, the tb tool is generated in the build/bin directory. It is an executable program that can directly test the service of [TARS](tars-guide.md) or [HTTP](http-guide.md).
```
git clone https://github.com/TarsCloud/TarsBenchmark.git
cd TarsBenchmark && mkdir build && cd build
cmake ..
make
```

In addition, two executable files of benchmark service will be generated (NodeServer and AdminServer), can be released to the Web platform through the following steps, so that the online benchmark can be realized.


# 4 <a id="chapter-4"></a>Quick release

The online benchmark service can be implemented with the latest version of [TarsWeb](https://github.com/TarsCloud/TarsWeb). Quick release steps are as follows:
```shell
./install.sh webhost token adminsip nodeip
```

Description:
```text
webhost                  Host or ip:port on the TarsWeb management side
token                    Which can obtain the http://webhost/auth.html#/token through the management side
adminsip                 The IP address of the AdminServer deployment, it must be deployed at a single point. It is recommended to deploy together with tarsregistry.。
nodeip                   The IP address of the NodeServer deployment, it should be separated from the AdminServer, it is recommended to expand the capacity on the management side. The more machines deployed, the stronger the ability to support parallel benchmark.
```