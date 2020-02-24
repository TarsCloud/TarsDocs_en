# TarsCpp 2.0 Update

## 1. Intro

Tarscpp2.0 has made major updates for version 1.X, mainly including the following aspects:
- Multi platform support, tarscpp2.0 completes the support of Linux, MAC, windows (> = win7) three OS systems
- If mysql, SSL, http2 support is enabled, cmake will automatically pull dependency packages and compile them (Linux, MAC). At present, windows needs to be improved
- Remove the promise function and recommend using the co process. The co process has completed the multi platform and multi CPU adaptation (if you have any problems, please submit the issues
- To remove TC_Atomic, please use std::atomic
- Modify the interface of the protocol resolver to greatly improve the efficiency of the network parsing package, [thirdparty protocol support](tars-thirdparty-protocol.md), Note: the interface is no longer downward compatible
- Modify the implementation of TC_ThreadPool to make it more convenient to use
- Improve SSL support, reduce memory copy times, improve efficiency, and the server and client can specify different ports to use different certificates
- Modify the encryption algorithm of authentication to 3DES, which is the basis for multi platform support
- Improve http2 support and fix bugs
- To improve various demos, see: cpp/examples

## 2. Multi platform support

Tarscpp2.0 supports Linux, MAC and windows (> = win7). You can write and debug programs on these platforms

Note: 
- tarscpp2.0 has minor changes at the interface level. Some code may need to be modified before compilation

### 2.1 cmake installation

Tarscpp2.0 is compiled through cmake on all three OS systems. Please check the version number of cmake first

```
cmake --version
```

**Please use cmake > 3.2**

cmake donwload web site: https://cmake.org/download/

Note that during the compilation of cmake, packages dependent on tars will be downloaded automatically according to the switch, such as my cmake output:

```
mkdir build
cd build
cmake ..
```

```
jarodruan-mb:cmake-build-debug jarod$ cmake ..
----------------------------------------------------
CMAKE_BUILD_TYPE:          Debug
PLATFORM:                  mac
INSTALL_PREFIX:            /usr/local/tars/cpp
----------------------------------------------------
TARS_MYSQL:                ON
TARS_HTTP2:                OFF
TARS_SSL:                  OFF
TARS_PROTOBUF:             OFF
----------------------------------------------------
CMAKE_SYSTEM_NAME:         Darwin
CMAKE_SYSTEM_PROCESSOR:    x86_64
CMAKE_CXX_COMPILER_ID:     Clang
ABI_STR:                   sysv
BF_STR:                    macho
CPU_STR:                   x86_64
JUMP_SRC:                  asm/jump_x86_64_sysv_macho_gas.S
MAKE_SRC:                  asm/make_x86_64_sysv_macho_gas.S
CMAKE_C_SIZEOF_DATA_PTR:   8
----------------------------------------------------
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/jarod/centos/TarsCpp/cmake-build-debug
```

Among them, TARS_MYSQL, TARS_HTTP2, TARS_SSL, TARS_PROTOBUF can be turned on or off as needed, for example, to turn on SSL support:

```
mkdir build
cd build 
cmake .. -DTARS_SSL=ON
```

Thus, your compiled tars supports SSL, [tars-ssl](../tars-tls.md)

**Note that there are bugs in cmake-3.16.4. It is recommended not to use it!**

### 2.2 linux & mac Compiler

After cmake installation or upgrade, download the source code:

```
git clone https://github.com/TarsCloud/TarsCpp.git --recursive
cd TarsCpp;
mkdir build;
cd build;
cmake ..
make -j4
make install
```

### 2.3 window Compiler

download source:
```
git clone https://github.com/TarsCloud/TarsCpp.git --recursive
```

Windows strongly recommends vs2019. Vs2019 is integrated with cmake automatically. Open CMakeLists.txt with vs2019 to complete compilation

If compiling in the form of vs2019 IDE, please pay attention to modifying CMakeSettings.json (generated when vs2019 loads CMakeLists.txt)
Modify: generator: Ninja -> Visual Studio 16 2019

If your vs is lower than vs2019, compile as follows:

```
cd TarsCpp;
mkdir vs_build;
cd vs_build;
cmake .. -G "Visual Studio 16 2019"
cmake --build . --config release
#install
cmake --build . --config release --target install
```

Note: "Visual Studio 16 2019", You can select the version you need and view it with cmake.. -G

In addition: at present, there are still some problems in the Windows version, can not open TARS_MYSQL, TARS_HTTP2, TARS_SSL, TARS_PROTOBUF, which are still being solved

