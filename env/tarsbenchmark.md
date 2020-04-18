# TarsBenchmark
> * [Intro](#chapter-1)
> * [Dependent environment](#chapter-2)
> * [Construction of tb tool](#chapter-3)


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

# 2 <a id="chapter-2"></a>Download and install build package dependency


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
cmake .. -DTARS_MYSQL=OFF
make
make install
```

Now the compilation environment of TarsCpp has been completed, and the tb can be constructed in the next step.


# 3 <a id="chapter-3"></a>Construction of tb

The main compilation steps are as follows, the tool is generated in the build/bin/tb directory.
```
git clone https://github.com/TarsCloud/TarsBenchmark.git
cd TarsBenchmark && mkdir build && cd build
cmake ..
make
```
