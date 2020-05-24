# Content

> - [APP](#main-chapter-1)
> - [Server](#main-chapter-2)
> - [Servant](#main-chapter-3)
> - [Module](#main-chapter-4)
> - [Tars protocol directory](#main-chapter-5)
> - [Server development mode](#main-chapter-6)
> - [Client development mode](#main-chapter-7)
> - [Template config](#main-chapter-8)
> - [Bussiness Config](#main-chapter-9)
> - [Log System](#main-chapter-10)
> - [Development and debugging release](#main-chapter-11)

This paper mainly introduces some common basic concepts in the tars platform, which is helpful for the use of the platform. This document can be read repeatedly. If you can't understand it for the first time, it doesn't matter. After writing HelloWorld, you will have different feelings.

## 1. <a id="main-chapter-1"></a> APP

App is the application name, which identifies a small set of services. Developers can define it according to their own needs, usually indicating the implementation of a business system name.

- In the tars system, the application name must be unique, for example: TestApp
- Usually the application name corresponds to a namespace or package name in the code
- The tars application name is used by the framework. Please do not use it for business services

## 2. <a id="main-chapter-2"></a> Server

Server is the service name, the name of the program providing business services

- The server name is named according to the business service function. It will be displayed on the left service tree of the tars web platform
- A server must belong to an app, and the server names under the app are unique
- General name: xxserver, such as LogServer, TimerServer, etc
- A server represents an independent program, binds at least one IP, and implements a set of related interfaces

## 3. <a id="main-chapter-3"></a> Servant

Servant is a service provider, which provides a number of specific interfaces for clients to call

- Servant corresponds to a class in the service code, which inherits from the interface (multiple specific functions) in the tars protocol file, and is implemented by the business developer
- A server must belong to a server. The server names under the server are unique
- Servant needs a name, such as: HelloObj, When provided to the client, the full name is: App.Server.Servant, such as: Test.HelloServer.HelloObj
- When the client calls the server, it only needs to specify the name of the server to complete the remote communication (how to implement it will be described later)

**Tars adopts this three-tier structure to avoid the conflict between service name and service name developed by different business developers**

## 4. <a id="main-chapter-4"></a> module

Module is the key word in the tars protocol file, which defines the protocol space and also corresponds to the language namespace (c + +) or package name (Java, go) or module (nodejs, go)

## 5. <a id="main-chapter-5"></a> Tars file directory specification

The tars file is the protocol communication interface of the tars service, especially when the client calls the server, it needs to rely on the tars protocol file of the server, so it is very important. In terms of management, we recommend the following management methods (of course, you can build your own appropriate open mode without changing the mode):

- In principle, the tars file is put together with the corresponding server;
- Each server is established on the development machine: /home/tarsproto/\[namespace\]/\[server\] sub directory;
- All tars files need to be updated to the directory of the corresponding server under /home/tarsproto
- When using the tars files of other servers, directly refer to the corresponding directory /home/tarsproto, which cannot be copied to this directory;
- In this way, when developing services on the same server, you only need to release the tars file to this directory, which is convenient for other callers to use
- In principle, the interface of tar can only be added, not reduced or modified;
- The tars service framework provided by each language provides quick release tars files to: /home/tarsproto/\[namespace\]/\[server\]

## 6 <a id="main-chapter-6"></a> Server development mode

The development mode of tars server and client in any language is basically the same:

- Determine the name of App, Server and Servant ;
- Write the tars file, define the interface provided by the service and the functions under the interface. The main interface must have one, and the functions under the interface can have more than one, see [tars protocol](tars-protocol.md)
- Use the tars2xxx tool (different tools in different languages) to generate code in different languages from the tars file
- Implement the tars service (refer to the documents of different languages), inherit the servant class in the generated file, and implement the interface of servant
- Compile the service and publish it on the management platform (APP, Server, Servant obj name, etc.) need to be configured on the management platform). Please refer to the following chapters
- Of course, your service can also run locally. After you start the service on the platform , `ps -ef`After you see the start mode of the service, you can execute it locally (note that there may be a configuration file, and you need to modify the port and other information)

Under normal circumstances, the services are ultimately running on each node server of the tars platform through tarsweb publishing, but in the process of debugging, you want to run on the local machine, how to deal with it?

Service startup is actually a command line. For example, C + + service is:

```
HelloServer --config=xxxxx.conf
```

Here, config indicates the configuration file for service startup. On the tars platform, tarsnode generated the config through template config and helloserver is pulled up. If you want to run the service locally, you must have this configuration file locally.

Note:

- It is recommended to make clear the meaning of the main configuration items of config, [refer to the development guide](../dev/tarscpp/tars-guide.md)
- bind IP in config should pay attention to providing cost machine
- `node=tars.tarsnode.ServerObj@xxxx`, Indicates the address of the connected tarsnode. If there is no tarsnode locally, this configuration can be removed
- `local=...`, Indicates the open port that the native machine connects to the tarsnode. If there is no tarsnode, this configuration can be dropped
- `locator=...`, Indicates the address (frame address) of the main control center, which is used to obtain the IP list according to the service name
- If you are an independent client, with this configuration, you can access it without specifying other service addresses

**Note that this configuration file is not a business configuration, but a service framework configuration, corresponding to the templates on the tars platform!**

How do I get this profile?

You can first publish the service to a node of the platform, and then log in to the node server to run:

```
ps -efww | grep ${your server name}
```

You can see the command line of the service startup, copy the corresponding configuration file to the local, open the configuration file, modify the corresponding IP port and related path in the configuration file, and then use the same command line to run locally!

Other languages are similar!

## 7. <a id="main-chapter-7"></a> Client development mode

After completing the writing and starting of the server, the client can be written. The client code generated by referencing the tars file is used to build the communicator. The communicator is used to obtain the proxy object of the corresponding service according to the servant name, and the proxy object is used to complete the communication

- Communicator is a class of client management thread and network. It has corresponding classes in each major language. Usually, the service framework will provide an initialized class for you to use (usually you can use this object). If it is a simple client, you need to create your own communicator
- If your client is another service and deployed on the framework, your communicator can use the communicator provided by the framework (refer to the documentation of each language). When calling the server, you do not need to specify the IP port, only need the obj name of the server, and the framework will automatically address and complete the call
- If your client is an independent client program and is not deployed on the framework, you can create your own communicator. There are two ways to call the service:
  > - The way to directly specify the IP port of the server (you can specify multiple ports, and the framework will automatically switch between disaster recovery). All languages are basically the same, such as C + + Language:

```
Communicator *communicator = new Communicator();
HelloPrx helloPrx = communicator->stringToProxy<HelloPrx>("Test.HelloServer.HelloObj@tcp -h xxx -p yyy:tcp -h www -p zzz");
helloPrx->call();
```

> - The communicator can also be assigned to the main control of the corresponding framework, so there is no need to specify the corresponding IP port. All languages are basically the same, such as C + + Language:

```
Communicator *communicator = new Communicator();
communicator->setProperty("locator", "tars.tarsregistry.QueryObj@tcp -h xxxx -p 17890");
HelloPrx helloPrx = communicator->stringToProxy<HelloPrx>("Test.HelloServer.HelloObj");
helloPrx->call();
```

In this way, although the service can address and recover the disaster, it does not report information. If it needs to report information, it needs to specify other related attributes, such as:

```
communicator->setProperty("stat", "tars.tarsstat.StatObj");
communicator->setProperty("property", "tars.tarspropery.PropertyObj");
```

Of course, you can use the configuration file to initialize the communicator directly. Refer to the client part of the web platform template configuration. In addition, the reporting service is similar here. If you do not have the locator to specify the tarsregistry address of the framework, you need to specify the IP port

## 8 <a id="main-chapter-8"></a> Template configuration

On the web platform, there is template configuration in the operation and maintenance management. Template configuration is very important for the framework, so we need to understand the role of template configuration

Every service deployed in the tars framework is actually published to the corresponding node by the framework. How does the tarsnode know the port of service binding, the number of threads started and other information when it pulls up the service? The answer is through: template configuration

Tarsnode will go to the platform to pull the template corresponding to the service (configured during service deployment), then generate the corresponding configuration of the service according to the template, and start the service according to the configuration of the user

Note that the template configuration files used in different languages are different. You can refer to the following documents in different languages

**It is strongly recommended that you do not need to modify the template provided by the framework, because the content of these templates may be modified in the subsequent framework upgrade. If you need to modify, you can inherit the template and let your service use the inherited template**

## 9 <a id="main-chapter-9"></a> Bussiness Config

As mentioned in the previous section, template configuration is actually a public configuration used by RPC server. It is generated by tarsnode. However, for business services, there are generally their own configuration information, which we call business configuration

The business configuration is generally managed by the configuration center on the web platform. The usage is as follows:

- On the web platform, under the configuration center of the corresponding service, add the business configuration file
- In the service code, load the configuration file (the RPC Library of each language provides the API for loading the configuration file)
- If necessary, you can also push configuration files to specific servers on the web platform (PHP's swoole does not support it)

For specific usage of business configuration, please refer to [business configuration](../dev/tars-config.md)

## 10 <a id="main-chapter-10"></a> Log System

Business services usually need to print logs, and the framework also provides a centralized log center.

Generally, there are two types of logs:

- One is debug log (cyclic log / single file control size / multiple log file cycles)
- One is daily / hour logs, which usually record important log information, which can be output to remote log center

Through the log API, you can output the log directly to the remote log center, that is, the machine where the tarslog is located (the log path is: /usr/local/app/tars/remote_app_log)

All language frameworks provide APIs for remote logging.

In addition, on the web platform, click the service details interface, click the service name, and you can also quickly view the local log of the service

## 11 <a id="main-chapter-11"></a> Development and debugging release

If in the development process, it needs to be manually published to the web platform for debugging every time, the debugging efficiency is very low, so the tars platform provides a way to publish services to the tars framework with one click:

- This requires a version of Web >= 2.0.0 and tarscpp > 2.1.0 to support
- Login web, enter user center and create token
- You can use curl command on Linux to upload and publish services. you cmake_tars_server.sh to create server, [see cmake spec(c++)](../dev/tarscpp/tars-spec.md), as Test/HelloServer as an example:

```
curl http://${your-web-host}/api/upload_and_publish?ticket=${token} -Fsuse=@HelloServer.tgz -Fapplication=Test -Fmodule_name=HelloServer -Fcomment=dev
```

The C++ version of cmake has embedded the command line in CMakeLists.txt of the service. The user only needs to:

```
cd build
cmake .. -DTARS_WEB_HOST=${WEB_HOST} -DTARS_TOKEN=${TOKEN}
make HelloServer-tar
make HelloServer-upload
```

complete upload and publish the service.

Note:

- replace your WEB_HOST and token
- HelloServer.tgz is the release package of C + +, Java is the war package, and other languages are similar, corresponding to the release package you uploaded to the web platform
