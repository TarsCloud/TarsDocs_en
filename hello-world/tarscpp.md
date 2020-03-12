# TarsCPP
> * [Create Server](#chapter-1)
> * [Server Implement](#chapter-2)
> * [Server Compiler](#chapter-3)
> * [Extension](#chapter-4)
> * [Rpc Call](#chapter-5)
> * [Other](#chapter-6)

## 1 <a id="chapter-1"></a> Create Server

Before you start, please must read [concept](../../base/tars-concept.md) and [spec](tars-spec.md)

```text
/usr/local/tars/cpp/script/cmake_tars_server.sh [App] [Server] [Servant]
```

In this example: /usr/local/tars/cpp/script/cmake\_tars\_server.sh TestApp HelloServer Hello

After execute, It will create files in TestApp/HelloServer/src:

```text
HelloServer.h HelloServer.cpp Hello.tars HelloImp.h HelloImp.cpp CMakeLists.txt
```

These files already contain the basic service framework and the default test interface implementation.

You can build server like this:
```
cd build
cmake ..
make -j4
```

You can also add other server in this directory.

## 2 <a id="chapter-3"></a> Server Implement

### tars protocol file

Firstly, the protocol file is compiled with reference to [tars protocol language document](https://github.com/TarsCloud/TarsDocs_en/blob/master/base/tars-protocol.md) to specify the data structure and calling interface of the communication between the two sides

如下：

Hello.tars：

```text
module TestApp
{

interface Hello
{
    int test();
};

}; 

```

use tars2cpp to create c++ source file: /usr/local/tars/cpp/tools/tars2cpp hello.tars

It will create hello.h, It contains the code of client and server. 

### Helloimp is the interface implementation class of servant

Implement the interface in the tar file defined by the service, as follows:

HelloImp.h

```text
#ifndef _HelloImp_H_
#define _HelloImp_H_

#include "servant/Application.h"
#include "Hello.h"

/**
 * HelloImp inherits the Hello Object defined in hello.h
 *
 */
class HelloImp : public TestApp::Hello
{
public:
    /**
     *
     */
    virtual ~HelloImp() {}

    /**
     * Initialization, Hello's virtual function, called when HelloImp initializes
     */
    virtual void initialize();

    /**
     * Destruct, a virtual function of Hello, called when the service destruct HelloImp exits
     */
    virtual void destroy();

    /**
     * Implement the test interface defined in the tars file
     */
    virtual int test(tars::TarsCurrentPtr current) { return 0;};

};
/////////////////////////////////////////////////////
#endif
```

HelloImp.cpp:

```text
#include "HelloImp.h"
#include "servant/Application.h"

using namespace std;

//////////////////////////////////////////////////////
void HelloImp::initialize()
{
    //initialize servant here:
    //...
}

//////////////////////////////////////////////////////
void HelloImp::destroy()
{
    //destroy servant here:
    //...
}
```

### HelloServer is the implementation class of the service

HelloServer.h:

```text
#ifndef _HelloServer_H_
#define _HelloServer_H_

#include <iostream>
#include "servant/Application.h"

using namespace tars;

/**
 * HelloServer inherits the Application class of the framework
 **/
class HelloServer : public Application
{
public:
    /**
     *
     **/
    virtual ~HelloServer() {};

    /**
     * Initialization interface of service
     **/
    virtual void initialize();

    /**
     * Clean up interface on service exit
     **/
    virtual void destroyApp();
};

extern HelloServer g_app;

////////////////////////////////////////////
#endif
```

HelloServer.cpp

```text
#include "HelloServer.h"
#include "HelloImp.h"

using namespace std;

HelloServer g_app;

/////////////////////////////////////////////////////////////////
void
HelloServer::initialize()
{
    //initialize application here:

    //Add the service interface implementation class HelloImp and route Obj binding relationship
    addServant<HelloImp>(ServerConfig::Application + "." + ServerConfig::ServerName + ".HelloObj");
}
/////////////////////////////////////////////////////////////////
void
HelloServer::destroyApp()
{
    //destroy application here:
    //...
}
/////////////////////////////////////////////////////////////////
int
main(int argc, char* argv[])
{
    try
    {
        g_app.main(argc, argv);
        g_app.waitForShutdown();
    }
    catch (std::exception& e)
    {
        cerr << "std::exception:" << e.what() << std::endl;
    }
    catch (...)
    {
        cerr << "unknown exception." << std::endl;
    }
    return -1;
}
/////////////////////////////////////////////////////////////////
```

**Each servant (obj) object corresponds to a business processing thread. Therefore, if it is a member variable of helloimp and only processed by the current helloimp object, it does not need to be locked**

## <a id="chapter-3"></a> Server Compiler

enter server directory:

```text
cd build
cmake ..
make -j4
```

## <a id="chapter-4"></a> Extension 

The tars framework provides the function of interface definition language. You can add interface and method to the tars file to extend the function of service.

You can modify Hello.tars，add function:

```text
module TestApp
{

interface Hello
{
    int test();
    int testHello(string sReq, out string sRsp);
};

}; 
```

Use `/usr/local/tars/cpp/tools/tars2cpp hello.tars` recreate hello.h

Modify HelloImp.h/HelloImp.cpp, Implement new interface code. 

In HelloImp.h, the testHello method inherits the Hello class:

```text
virtual int testHello(const std::string &sReq, std::string &sRsp, tars::TarsCurrentPtr current);
```

HelloImp.cpp implement testHello function:

```text
int HelloImp::testHello(const std::string &sReq, std::string &sRsp, tars::TarsCurrentPtr current)
{
    TLOGDEBUG("HelloImp::testHellosReq:"<<sReq<<endl);
    sRsp = sReq;
    return 0;
}
```

make cleanall;make;make tar，create HelloServer.tgz package.

## <a id="chapter-5"></a> Rpc Call

On the development environment, create /home/tarsproto/\[APP\]/\[Server\] directory for tars protocol file release

for example: /home/tarsproto/TestApp/HelloServer

In the code directory of the server just written, execute `make release` 

Then h、tars and mk files will be generated in /home/tarsproto/TestApp/HelloServer

In this way, when a service needs to access HelloServer, it directly refers to the content of HelloServer service make release, and does not need to copy the tars of HelloServer (that is, the tars file of HelloServer does not need to be stored in the code directory).

Create the client code directory, for example: TestHelloClient/

Write main.cpp, Create an instance and call the newly written interface function for testing.

Synchronization mode:

```text
#include <iostream>
#include "servant/Communicator.h"
#include "Hello.h"

using namespace std;
using namespace TestApp;
using namespace tars;

int main(int argc,char ** argv)
{
    Communicator comm;

    try
    {
        HelloPrx prx;
        comm.stringToProxy("TestApp.HelloServer.HelloObj@tcp -h 10.120.129.226 -p 20001" , prx);

        try
        {
            string sReq("hello world");
            string sRsp("");

            int iRet = prx->testHello(sReq, sRsp);
            cout<<"iRet:"<<iRet<<" sReq:"<<sReq<<" sRsp:"<<sRsp<<endl;

        }
        catch(exception &ex)
        {
            cerr << "ex:" << ex.what() << endl;
        }
        catch(...)
        {
            cerr << "unknown exception." << endl;
        }
    }
    catch(exception& e)
    {
        cerr << "exception:" << e.what() << endl;
    }
    catch (...)
    {
        cerr << "unknown exception." << endl;
    }

    return 0;
}
```

Asynchronous mode:

```text
#include <iostream>
#include "servant/Communicator.h"
#include "Hello.h"

using namespace std;
using namespace TestApp;
using namespace tars;

class HelloCallBack : public HelloPrxCallback
{
public:
    HelloCallBack(){}

    virtual ~HelloCallBack(){}

    virtual void callback_testHello(tars::Int32 ret,  const std::string& sRsp)
    {
        cout<<"callback_testHello ret:"<< ret << "|sRsp:" << sRsp <<endl; 
    }

    virtual void callback_testHello_exception(tars::Int32 ret)
    {
        cout<<"callback_testHello_exception ret:"<< ret <<endl;
    }
};

int main(int argc,char ** argv)
{
    Communicator comm;

    try
    {
        HelloPrx prx;
        comm.stringToProxy("TestApp.HelloServer.HelloObj@tcp -h 10.120.129.226 -p 20001" , prx);

        try
        {
            string sReq("hello world");
            HelloPrxCallbackPtr cb = new HelloCallBack();
            prx->async_testHello(cb, sReq);
            cout<<" sReq:"<<sReq<<endl;
        }
        catch(exception &ex)
        {
            cerr<<"ex:"<<ex.what() <<endl;
        }
        catch(...)
        {
            cerr<<"unknown exception."<<endl;
        }
    }
    catch(exception& e)
    {
        cerr<<"exception:"<<e.what() <<endl;
    }
    catch (...)
    {
        cerr<<"unknown exception."<<endl;
    }

    getchar();

    return 0;
}
```

Write the makefile, Write the makefile, which contains the mk file under the directory /home/tarsproto/APP/server just generated through make release, as follows:

```text
#-----------------------------------------------------------------------
APP         :=TestApp
TARGET      :=TestHelloClient
CONFIG      :=
STRIP_FLAG  := N

INCLUDE     += -I/home/tarsproto/TestApp/HelloServer/
LIB         +=
#-----------------------------------------------------------------------
include /usr/local/tars/cpp/makefile/makefile.tars
#-----------------------------------------------------------------------
```
Make the target file and upload it to the environment that can access the server for running test

## 6 <a id="chapter-6"></a> Others

- There are several very important calling examples under *examples*, including synchronous, asynchronous, coroutine, proxy mode, push mode, HTTP service support, etc.

- The Communicator in the code manages client resources. It is recommended to make it global. If it is not an independent Client, but in the service framework, directly obtain the good Communicator provided by the framework. See ProxyServer

- In the above Client example, `comm.stringToProxy (" TestApp.HelloServer.HelloObj@tcp -h 10.120.129.226 -p 20001 ", prx);` specifies the HelloServer's ip: port. In practice, when your service is deployed On the framework, when you need to call another service, you only need to: `comm.stringToProxy (" TestApp.HelloServer.HelloObj ")`, the framework will automatically address the HelloServer service on the back end and automatically complete the disaster recovery switch.
