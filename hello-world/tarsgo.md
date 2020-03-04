# TarsGo
> * [Create service](#chapter-1)
> * [Define interface file](#chapter-2)
> * [Server development](#chapter-3)
> * [Client development](#chapter-4)
> * [HTTP Server development](#chapter-5)

## <a id="chapter-1"></a> Create service

Run create\_tars\_server.sh，create go service, If there is a syntax error during execution, try use:
`dos2unix create_tars_server.sh`

```text
sh $GOPATH/src/github.com/TarsCloud/TarsGo/tars/tools/create_tars_server.sh [App] [Server] [Servant]
for example： 
sh $GOPATH/src/github.com/TarsCloud/TarsGo/tars/tools/create_tars_server.sh TestApp HelloGo SayHello
```

After the command is executed, the code will be generated to gopath, and the directory will be named as `APP/Server`. The specific path will also be prompted in the generated code.

```text
[root@1-1-1-1 ~]# sh $GOPATH/src/github.com/TarsCloud/TarsGo/tars/tools/create_tars_server.sh TestApp HelloGo SayHello
[create server: TestApp.HelloGo ...]
[mkdir: $GOPATH/src/TestApp/HelloGo/]
>>>Now doing:./start.sh >>>>
>>>Now doing:./Server.go >>>>
>>>Now doing:./Server.conf >>>>
>>>Now doing:./ServantImp.go >>>>
>>>Now doing:./makefile >>>>
>>>Now doing:./Servant.tars >>>>
>>>Now doing:client/client.go >>>>
>>>Now doing:vendor/vendor.json >>>>
# runtime/internal/sys
>>> Great！Done! You can jump in $GOPATH/src/TestApp/HelloGo
>>> After editing the tars file, use the following to automatically generate the go file
>>>       $GOPATH/bin/tars2go *.tars
```

## <a id="chapter-2"></a>Define interface file

The interface document defines the request method, parameter field type, etc. for reference to the description of the interface definition document, ref: kai-fa/tars_protocol.md

n order to test our definition of an echoHello interface, the client request parameter is a short string such as "tars", and the service response is "Hello tars"

```text
# cat $GOPATH/src/TestApp/HelloGo/SayHello.tars 
module TestApp{
interface SayHello{
     int echoHello(string name, out string greeting); 
   };
};
```

**Note: the out modifier key in the parameter identifies the output parameter**

## <a id="chapter-3"></a>Server development

First, the tars protocol file is transformed into the form of golang language.

```text
$GOPATH/bin/tars2go SayHello.tars
```

Now start to implement the logic of the server: the client sends a name, and the server responds to Hello name.

```text
cat $GOPATH/src/TestApp/HelloGo/SayHelloImp.go
```

```text
package main

type SayHelloImp struct {
}

func (imp *SayHelloImp) EchoHello(name string, greeting *string) (int32, error) {
     *greeting = "hello " + name
     return 0, nil
}
```

**Note: The function name should be capitalized, and the rules for exporting methods in go language should be followed**

Compile the main function, the initial code and the implementation of the tars framework.

cat $GOPATH/src/TestApp/HelloGo/HelloGo.go

```text
package main

import (
	"github.com/TarsCloud/TarsGo/tars"

	"TestApp"
)

func main() { //Init servant
	imp := new(SayHelloImp)                                    //New Imp
	app := new(TestApp.SayHello)                                 //New init the A JCE
	cfg := tars.GetServerConfig()                               //Get Config File Object
	app.AddServant(imp, cfg.App+"."+cfg.Server+".SayHelloObj") //Register Servant
	tars.Run()
}
```

Compile the build executable and package the release package.

```text
cd $GOPATH/src/TestApp/HelloGo/ && make && make tar
```

The executable HelloGo and distribution package HelloGo.tgz will be generated

## <a id="chapter-4"></a>Client development

```text
package main

import (
        "fmt"
        "github.com/TarsCloud/TarsGo/tars"

        "TestApp"
)

//Only need to initialize once, global
var comm *tars.Communicator
func main() {
        comm = tars.NewCommunicator()
        obj := "TestApp.HelloGo.SayHelloObj@tcp -h 127.0.0.1 -p 3002 -t 60000"
        app := new(TestApp.SayHello)
        /*
         // if your service has been registered at tars registry
         comm = tars.NewCommunicator()
         obj := "TestApp.HelloGo.SayHelloObj"
         // tarsregistry service at 192.168.1.1:17890 
         comm.SetProperty("locator", "tars.tarsregistry.QueryObj@tcp -h 192.168.1.1 -p 17890")
        */
    
        comm.StringToProxy(obj, app)
        reqStr := "tars"
        var resp string
        ret, err := app.EchoHello(reqStr, &resp)
        if err != nil {
                fmt.Println(err)
                return
        }
        fmt.Println("ret: ", ret, "resp: ", resp)
}
```

* import/TestApp is generate by tars2go prev step
* Obj specifies the address port of the server. If the server is not registered in the master, you need to know the address and port of the server and specify it in obj. In the example, the protocol is TCP, the address of the server is local address, and the port is 3002. If there are multiple servers, you can write 'TestApp.HelloGo.SayHelloObj@tcp -h 127.0.0.1 -p 9985:tcp -h 192.168.1.1 -p 9983' so that the request can be distributed to multiple nodes.
* If the service has been registered with the master, you do not need to write the server address and port, but you need to specify the master address when initializing the communicator. 
* comm is communicator，For communication with the server, it needs to be global


Compile:

```text
# go build client.go
# ./client
ret:  0 resp:  hello tars 
```

## <a id="chapter-5"></a>HTTP Server development

Tarsgo supports HTTP services. Follow the above steps to create a good service. The processing of HTTP requests in tarsgo is encapsulated in go native, so it is very easy to use.

```text
package main

import (
	"net/http"
	"github.com/TarsCloud/TarsGo/tars"
)

func main() {
	mux := &tars.TarsHttpMux{}
	mux.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("Hello tars"))
	})
        cfg := tars.GetServerConfig()
	tars.AddHttpServant(mux, cfg.App+"."+cfg.Server+".HttpSayHelloObj") //Register http server
	tars.Run()
}
```

In addition, you can directly call other tar services in the same way as mentioned in "client development".




