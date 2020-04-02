


# HTTP Support

Tars has built-in support for HTTP and RPC encapsulation for the protocol library, making it almost transparent to business development and very convenient to use. Because of reusing tar RPC, it also has the characteristics of synchronization, asynchrony and timeout, and can use tar stat to report call quality.

This article briefly introduces the methods and steps of using HTTP, including synchronous call and asynchronous call. See examples/HttpDemo for example code.

**注意tarscpp>=2.3.0版本才支持**

## http server

see demo: examples/HttpDemo/HttpServer

Custom protocol, using HTTP. For details, [please refer to the HTTP server documentation](tars-http.md)

```

void HttpServer::initialize()
{
    addServant<HttpImp>(ServerConfig::Application + "." + ServerConfig::ServerName + ".HttpObj");
    addServantProtocol(ServerConfig::Application + "." + ServerConfig::ServerName + ".HttpObj", &TC_NetWorkBuffer::parseHttp);
}

```
```
int HttpImp::doRequest(JceCurrentPtr current, vector<char> &buffer)
{
    TC_HttpRequest request; 
    vector<char> v = current->getRequestBuffer();
    string sBuf;
    sBuf.assign(&v[0],v.size());
    request.decode(sBuf);
    TC_HttpResponse rsp;
    string s="hello";
    rsp.setResponse(s.c_str(),s.size());
    rsp.encode(buffer);
   
    return 0;
}
```
## http rpc

### Get Servant Proxy, Set Protocol

```text
CommunicatorPtr& comm = Application::getCommunicator();
ServantPrx prx = comm->stringToProxy<ServantPrx>("test.server.yourobj");

// set protocol
prx->tars_set_protocol(ServantProxy::PROTOCOL_HTTP1, 10);
```

### About connection reuse

Although HTTP supports long and short connections, HTTP cannot be connected for reuse, that is, only one package (request and response) can run in parallel on one connection, so tars RPC specially supports serial mode

For example:
```
prx->tars_connection_serial(10)
```

It is equivalent to starting 10 HTTP connections and completing HTTP requests on the connection. Do not enable this parameter for normal RPCs of tar!!!

Here, a function is used to enable the support of prx for HTTP. The second parameter 10 is the number of serial connections

```
prx->tars_set_protocol(ServantProxy::PROTOCOL_HTTP1, 10);
```

In fact, this statement is equivalent to:

```
ProxyProtocol proto;
proto.requestFunc = tars::http1Request;
proto.responseExFunc = tars::http1Response;
prx->tars_set_protocol(proto);
prx->tars_connection_serial(10);

```

### Sync Call

```text
	shared_ptr<TC_HttpResponse> rsp;
	shared_ptr<TC_HttpRequest> req = std::make_shared<TC_HttpRequest>();
	req->setPostRequest("http://domain.com/hello", string("helloworld-") + TC_Common::tostr(i), true);
    //set http header, for example: keep-alive
	req->setHeader("Connection", "keep-alive");

	prx->http_call("hello", req, rsp);
```

Note: the first parameter of http_call here has no actual function, but is used for monitoring. That is to say, the function name is "hello" in the interface statistics of web management platform

### Async Call

```text
// callback
class MyHttpCb : public HttpCallback
{
public:
	virtual int onHttpResponse(const shared_ptr<TC_HttpResponse> &rsp)
	{
		return 0;
	}
	virtual int onHttpResponseException(int expCode)
	{
		return 0;
	}
};

shared_ptr<TC_HttpRequest> req = std::make_shared<TC_HttpRequest>();

string buff = string("helloworld-") + TC_Common::tostr(i);
req->setPostRequest("http://domain.com/hello", buff, true);

prx->http_call_async("hello", req, p);

```

Note: the first parameter of http_call_async here has no actual function, but is used for monitoring. That is to say, the function name is "hello" in the interface statistics of web management platform
