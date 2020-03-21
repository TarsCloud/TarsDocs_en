# Directory
> * [Intro](#main-chapter-1)
> * [cmake spec](#main-chapter-2)
> * [Makefile spec](#main-chapter-3)

# Intro

Before reading this article, you must read [basic concepts](../../base/tars-concept.md)

There are two ways to manage C + + development services: makefile and cmake management

In tarscpp >= 2.1.0, cmake management is recommended as the main mode, and makefile mode is no longer supported by default

## 1. <a id="main-chapter-1"></a> cmake spec

### 1.1. cmake use

For services implemented with tars, it is highly recommended to use the cmake specification.

The tars framework provides a basic tars-tools.cmake (/usr/local/tars/cpp/makefile/tars-tools.cmake), CMakelists.txt of business service can reference this file

The tars framework also provides scripts \(/usr/local/tars/cpp/script/create\_tars\_server.sh\) can create empty bussiness server and CMakeLists.txt

CMakeLists.txt examples:

```
cmake_minimum_required(VERSION 2.8)

project(Demo-DemoServer)

option(TARS_MYSQL "option for mysql" ON)
option(TARS_SSL "option for ssl" OFF)
option(TARS_HTTP2 "option for http2" OFF)

if(WIN32)
    include (c:\\tars\\cpp\\makefile\\tars-tools.cmake)
else()
    include (/usr/local/tars/cpp/makefile/tars-tools.cmake)
endif()

set(TARS_WEB_HOST "http://127.0.0.1:4001")

include_directories(/usr/local/taf/cpp/thirdparty/include)
link_directories(/usr/local/taf/cpp/thirdparty/lib)

#include_directories(/home/tarsprotol/App/OtherServer)

add_subdirectory(src)

#add_subdirectory(other)

#link_libraries(mysqlclient)

```

server source code is in src directory,CMakeLists.txt as follow:
```
cmake_minimum_required(VERSION 2.8)

project(Test-HelloServer)

gen_server(Test HelloServer)

```

compiler:
```
mkdir build
cd build 
cmake ..
make -j4
```

Note:
- TARS_SSL, TARS_HTTP2 other switches that need to match the tars compilation. If the tar compilation is not turned on, they should also be turned off here:
```
#off
cmake .. -DTARS_SSL=OFF -DTARS_HTTP2=OFF
#on
cmake .. -DTARS_SSL=ON -DTARS_HTTP2=ON

```

Turning tars compilation on and off is similar, see [http2](tars-http2.md), [ssl](tars-tls.md)

### 1.2. Manage multiple services

With cmake management service, you can manage multiple services in one directory, one directory for each service. In the root cmakelists.txt, call:

```
add_subdirectory(other)
```

### 1.3. Include tars protocol

When you need to include the tars files of other services, you can reference the tars directory of the corresponding services in CMakelists.txt, for example:

```text
include_directories(/home/tarsprotol/App/OtherServer)
```

### 1.4. Include library

When you need to reference the Lib library, such as MySQL:

```
link_libraries(mysqlclient)
```

If other services in the directory refer to different libraries, you can modify each service's own CMakeLists.txt

### 1.5. release tars protocol

In order to make it convenient for other services to use the tars file of the current service, you can execute the following command (note that it is executed in the build directory):

```
make HelloServer-release
```

### 1.5. Package and upload services

You can package and upload services with one click:

```
make HelloServer-tar
make HelloServer-upload
```

Upload service needs to set the web address correctly

```
set(TARS_WEB_HOST "http://127.0.0.1:4001")
```

may you should set like this:
```
cd build
cmake .. -DTARS_WEB_HOST=http://xxx.xxx.xxx.xxx:3000
```

here, TARS_WEB_HOST is your web address


And open the upload configuration of the web (usually done by the test environment), refer to [basic concepts](../../base/tars-concept.md)

# 2. Makefile specification

It is highly recommended to use the cmake specification when use service that realized with Tars.

But if you use makefile, you can use specification as follows.

The TARS framework provides a basic Makefile for makefile.tars. The service written in Tars contains the Makefile to help you maintain the Makefile.

The TARS framework also provides a script (installation directory /script/create_tars_server.sh) to automatically generate an empty service framework and Makefile;

## 2.1. Makefile usage principle

In principle, a directory can only be a Server or a program, that is, a Makefile can only have one Target;

When you need to include other libraries, include them in the bottom of the Makefile according to the dependency order;

E.g:
```
include /home/tarsproto/TestApp/HelloServer/HelloServer.mk
include /usr/local/tars/cpp/makefile/makefile.tars
```
Makefile.tars must be included.

## 2.2. Makefile template explanation

APP: the name space of the program (ie Application)

TARGET: Server name;

CONFIG: The name of the configuration file. When you make tar, the file is included in the tarball.

INCLUDE: Other paths that need to be included;

LIB: Required Library

The Makefile for Test.HelloServer is as follows:
```
#------------------------------------------------- ----------------------

APP            := TestApp
TARGET         := HelloServer
CONFIG         := HelloServer.conf
STRIP_FLAG     := N
TARS2CPP_FLAG  :=

INCLUDE        +=
LIB            +=

#------------------------------------------------- ----------------------

Include /home/tarsproto/TestApp/HelloServer/HelloServer.mk
Include /usr/local/tars/cpp/makefile/makefile.tars
```
The key variables are usually not used, but the business can add its own values after these variables:
```
RELEASE_TARS: You need to publish the tars file in the /home/tarsproto/ directory. If you need to publish your own .h to /home/tarsproto, you can do the following:
RELEASE_TARS += xxx.h
CONFIG: The name of the configuration file. In fact, you can add the files you need later, so that the file will be included in the tarball when you call make tar.
```
For other variables, please read makefile.tars.

## 2.3. Makefile use

make help: You can see all the functions of the makefile.

make tar: generate a release file

make release: copy the tars file to the /home/tarsproto directory and automatically generate the relevant mk file

make clean: clear

make cleanall: clear all
