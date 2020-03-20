# HTTP 2 Support

Tars uses the [nghttp2 library] (https://www.nghttp2.org/) to support the http2 protocol. It encapsulates the protocol library with RPC, making it almost transparent to business development and very convenient to use. Because of reusing tars RPC, it also has the characteristics of synchronization, asynchrony and timeout, and can use tars stat to report call quality.

This article briefly introduces the methods and steps of using http2, including synchronous call and asynchronous call. 

You can find demo: cpp/example/HttpDemo

## Open http2

### Compiling tar framework, supporting nghttp2

By default, the tars framework does not turn on http2. Turn on http2:

```text
cmake .. -DTARS_HTTP2=ON
```

rebuild tarscpp.

## http2 server 

see demo: cpp/example/HttpDemo/Http2Server

Custom protocol with http2:

```

void HttpServer::initialize()
{
    addServant<Http2Imp>(ServerConfig::Application + "." + ServerConfig::ServerName + ".Http2Obj");
    addServantProtocol(ServerConfig::Application + "." + ServerConfig::ServerName + ".Http2Obj", &parseHttp2);
}

```

Protocol resolver function. Note that each connection creates and stores a TC_Http2server as the session controller of http2

```

TC_NetWorkBuffer::PACKET_TYPE parseHttp2(TC_NetWorkBuffer&in, vector<char> &out)
{
    TC_Http2Server*sessionPtr = (TC_Http2Server*)(in.getContextData());

    if(sessionPtr == NULL)
    {
    	shared_ptr<TC_Http2Server> session(new TC_Http2Server());
	    in.setContextData(session.get());

	    session->settings(3000);

        TC_EpollServer::Connection *connection = (TC_EpollServer::Connection *)in.getConnection();
        Http2Imp::addHttp2(connection->getId(), session);

	    sessionPtr = session.get();
    }

	return sessionPtr->parse(in, out);
}

```

In the service implementation, the session (TC_Http2Server object) of http2 is obtained according to the connection ID and the protocol is parsed. 
```

int Http2Imp::doRequest(TarsCurrentPtr current, vector<char> &buffer)
{
    shared_ptr<TC_Http2Server> session = getHttp2(current->getUId());

	vector<shared_ptr<TC_Http2Server::Http2Context>> contexts = session->decodeRequest();

	for(size_t i = 0; i< contexts.size(); ++i)
	{
		shared_ptr<TC_Http2Server::Http2Context> context = contexts[i];

		vector<char> data;

		context->response.setHeader("X-Header", "TARS");
		context->response.setResponse(200, "OK", context->request.getContent());

		int ret = session->encodeResponse(context, data);
		if(ret != 0)
		{
			cout << "encodeResponse error:" << session->getErrMsg() << endl;
		}
		buffer.insert(buffer.end(), data.begin(), data.end());
	}

    return 0;
}

int Http2Imp::doClose(TarsCurrentPtr current)
{
    delHttp2(current->getUId());

    return 0;
}
```

## http2 rpc use

see demo: cpp/example/HttpDemo/Http2Client


### Get client agent and set http2 encoding and decoding function

```text
CommunicatorPtr& comm = Application::getCommunicator();
YourPrx prx = comm->stringToProxy<YourPrx>("test.server.yourobj");

// set protocol
ProxyProtocol proto;
proto.requestFunc = ProxyProtocol::http2Request;
proto.responseExFunc = ProxyProtocol::http2Response;
prox->tars_set_protocol(proto);
```

### sync call

```text
map<string, string> headers;
headers[":authority"] = "domain.com";
headers[":scheme"] = "http";

map<string, string> rspHeaders;
string rspBody;

prx->http_call("GET", "/", headers, "", rspHeaders, rspBody);
```

### async call

```text
// callback
class MyHttpCb : public HttpCallback
{
    public:
            int onHttpResponse(const map<string, string>& reqHeaders,
                               const map<string, string>& rspHeaders,
                               const vector<char>& rspBody)
            {
               // your process
               return 0;
            }
            int onHttpResponseException(const map<string, string>& reqHeaders, int eCode)
            {
                //  process exception
                return 0;
            }
};

// call
header[":authority"] = "domain.com";
header[":scheme"] = "http";
prx->http_call_async("GET", "/", headers, "", new MyHttpCb());
```

