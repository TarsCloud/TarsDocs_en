# TarsCpp Third party protocol support

## 1. Intro

Protocol parser is an important design concept of tarscpp service model. It enables the server implemented by tarscpp to support almost any protocol, including your customized network protocol.

In short:
- You can use tarscpp's service framework to support other protocols, such as HTTP, or whatever you define
- Other non tars services can be called through the communicator (client) of tarscpp

## 2. Protocol Parser

Tarscpp2.0 has made great changes to protocol parsing, the main purpose is to reduce the memory copy when processing network packets. After optimization, tarscpp2.0 has a performance improvement of more than 30% compared with 1.X version

## 3 Protocol Parse on server side

We know that we can quickly implement an HTTP service through tarscpp. See[http server](https://github.com/TarsCloud/TarsDocs_en/blob/master/demo/tarscpp/tars_cpp_http_demo.md), the core of it is:

```
void HttpServer::initialize()
{
    addServant<HttpImp>(ServerConfig::Application + "." + ServerConfig::ServerName + ".HttpObj");
    addServantProtocol(ServerConfig::Application + "." + ServerConfig::ServerName + ".HttpObj",&TC_NetWorkBuffer::parseHttp);
}
```

That is, the protocol parsing function of servant (here HttpObj) is specified:  ```TC_NetWorkBuffer::parseHttp```

You can implement this function yourself. In tarscpp1. X version, its function is defined as follows:
```
typedef std::function<int(string &, string &)> protocol_functor;
```
such as:
```
int parseHttp(string &in, string &out)
```

About:
- in: input network buffer
- out: out buffer of Parsing a completed packet
- return: int
>- TC_EpollServer::PACKET_FULL: Parse out a complete package, put the complete package into the out parameter, and truncate in. Out will be obtained in the doRequest of Servant
>- TC_EpollServer::PACKET_LESS: The package has not been received completely, the framework will continue to receive it
>- TC_EpollServer::PACKET_ERR: Package parse error, the framework will auto close the current connection

In this protocol parsing, there are actually several memory copies, such as truncating in, copy to out, which will bring a lot of performance loss. Therefore, tarscpp2.0 has made improvements to this problem

The prototypes of the protocol of tarscpp2.0 are as follows:
```
typedef std::function<PACKET_TYPE(TC_NetWorkBuffer &, vector<char> &)> protocol_functor;
```
such as:
```
TC_NetWorkBuffer::PACKET_TYPE parseHttp(TC_NetWorkBuffer &in, vector<char> &out)
```

About:
- in: TC_NetWorkBuffer manages all input buffers, and chain list management is used internally
- out: Parsing a completed packet (tests show that vector<char> is slightly faster than string in memory processing)
- return: TC_NetWorkBuffer::PACKET_TYPE
>- TC_NetWorkBuffer::PACKET_FULL: Parse out a complete package, put the complete package into the out parameter, and truncate in. Out will be obtained in the doRequest of Servant
>- TC_NetWorkBuffer::PACKET_LESS: The package has not been received completely, the framework will continue to receive it
>- TC_NetWorkBuffer::PACKET_ERR: Package parse error, the framework will auto close the current connection

To avoid memory copy, several functions are designed in TC_NetworkBuffer, which you may need to use:
- getBufferLength: Get the length of the data in the current buffer 
- getBufferPointer: Get the number (pointer and length) of the first valid buffer on the linked list
- getBuffers: Get all buffers (there will be memory copy, please note when using)
- getHeader: Get the first few bytes of binary stream (note that the pointer does not move, that is, does not change the data content)
- moveHeader: Pointer moves backward, usually used with getHeader
- getValueOf[1-4]: Get the first few bytes and convert them into specific types in byte order
- parseHttp: http protocol parser function


### Demo(cpp/examples/CustomDemo)

The input binary protocol is: the total length of the first four bytes (byte order) packets + specific content

```

static TC_NetWorkBuffer::PACKET_TYPE pushResponse(TC_NetWorkBuffer &in, vector<char> &out)
{
	size_t len = sizeof(tars::Int32);

    //The total length of the packet is less than 4 bytes, and the packet is not received completely
	if (in.getBufferLength() < len)
	{
		return TC_NetWorkBuffer::PACKET_LESS;
	}

	string header;
    //Get the first 4 bytes
	in.getHeader(len, header);

	assert(header.size() == len);

	tars::Int32 iHeaderLen = 0;

    //Convert the first four bytes into byte order and store them in iHeaderLen
	::memcpy(&iHeaderLen, header.c_str(), sizeof(tars::Int32));

	iHeaderLen = ntohl(iHeaderLen);

	//If the package is too small or too long, throw an exception directly, and the framework will close the connection
	if (iHeaderLen > 100000 || iHeaderLen < sizeof(unsigned int))
	{
		throw TarsDecodeException("packet length too long or too short,len:" + TC_Common::tostr(iHeaderLen));
	}

    //The length of the received package is less than iheaderlen, and it has not been collected completely
	if (in.getBufferLength() < (uint32_t)iHeaderLen)
	{
		return TC_NetWorkBuffer::PACKET_LESS;
	}

    //It has been collected. Get the buffer of iheaderlen and put it in out
	in.getHeader(iHeaderLen, out);

    //Move iHeaderLen bytes back
    in.moveHeader(iHeaderLen);

    //Returned package has been received
    return TC_NetWorkBuffer::PACKET_FULL;
}

```

**Actually, this protocol has been implemented and can be used directly: TC_NetWorkBuffer::parseBinary4**

Note: enable the third-party protocol. On the web management platform, the protocol needs to be selected during deployment: NOT TARS protocol!!

## 4 Protocol parser on client side

For the tars framework, the client callers are managed by the communicator class (all languages in this class). The remote server agent is created by the communicator to complete the communication with the server

Under normal circumstances, both the clients and the servers communicate through the tars protocol, but in some cases, your service may need to call another service(not tars service). At this time, the common practice is that you need to implement protocol parser and network communication by yourself, and the communicator of the tars can set protocol parser to complete the communication between the clients and other services

see demo: cpp/example/CustomDemo/CustomClient

```
string sObjName = "TestApp.CustomServer.TestPushServantObj@tcp -h 127.0.0.1 -t 60000 -p 9300";

_prx = _comm.stringToProxy<ServantPrx>(sObjName);

```

Then set the protocol parser of request package and response package:

```
	ProxyProtocol prot;
    prot.requestFunc = pushRequest;
    prot.responseFunc = pushResponse;

    _prx->tars_set_protocol(prot);
```

Use when sending a request ```rpc_call_async```:
```
ResponseRequest rsp;
_prx->rpc_call(_prx->tars_gen_requestid(), "doCustomFunc", buf.c_str(), buf.length(), rsp);
```

Aboult:
- Both synchronous (RPC call) and asynchronous (RPC call async) are supported. The above is the method of synchronous call. The asynchronous call will be introduced later
- buf is the specific request package content
- pushRequest & pushResponse used for parser encode & decode
- tars_gen_requestid, This function generates an ID. here is the ID of the request package. It needs to correspond to the ID in the response package to complete the matching between the request package and the response package

The function prototype of pushRequest is as follows:

```
typedef std::function<vector<char> (const RequestPacket &, Transceiver *)> request_protocol_functor;
```

About:
- RequestPacket is the underlying request structure of the tars service. For details, see
[tars_protorol](https://github.com/TarsCloud/TarsDocs_en/blob/master/base/tars-protocol.md)
- The purpose of Protocol parser is to serialize requestpacket into vector<char>
- Transceiver is a connection class, which usually does not need to be managed. However, for some protocols that may be related to the current connection (such as http2), you can obtain the related info through this object
- The member function of _prx agent: rpc_call & rpc_call_async is used to send the data of custom protocol to the server. Its function prototype is as follows
```
    virtual void rpc_call(uint32_t requestId, const string& sFuncName, const char* buff, uint32_t len, ResponsePacket &rsp);

    virtual void rpc_call_async(uint32_t requestId, const string& sFuncName, const char* buff, uint32_t len, const ServantProxyCallbackPtr& callback, bool bCoro = false);
```  

Note: the parameters requestId, sFuncName and sBuffer are assigned to iRequestId, sFuncName and sBuffer of RequestPacket

for example:

//The overall package structure is: 4 bytes length + 4 bytes requestid + data buffer
```
static vector<char> pushRequest(const RequestPacket& request, Transceiver*)
{
    //Total packet length
    unsigned int net_bufflength = htonl(request.sBuffer.size()+8);
    unsigned char * bufflengthptr = (unsigned char*)(&net_bufflength);

	vector<char> buffer;
	buffer.resize(request.sBuffer.size()+8);

	memcpy(buffer.data(), bufflengthptr, sizeof(unsigned int));

    //requestId
    unsigned int netrequestId = htonl(request.iRequestId);
    unsigned char * netrequestIdptr = (unsigned char*)(&netrequestId);

	memcpy(buffer.data() + sizeof(unsigned int), netrequestIdptr, sizeof(unsigned int));
	memcpy(buffer.data() + sizeof(unsigned int) * 2, request.sBuffer.data(), request.sBuffer.size());

	return buffer;
}

```

Next, look at the processing of response packet reception. The response packet is the definition of function:
```
typedef std::function<PACKET_TYPE(TC_NetWorkBuffer &, ResponsePacket &)> response_protocol_functor;
```

About:
- ResponsePacket is the underlying response structure of the tars service. For details, see [tars_protorol](https://github.com/TarsCloud/TarsDocs_en/blob/master/base/tars-protocol.md)
- The purpose of Protocol parser is to parse the input buffer into ResponsePacket

Take customResponse in CustomClient as example:
```

//The response packet decoding function decodes the data received from the server according to the specific format and resolves it to theResponsePacket
static TC_NetWorkBuffer::PACKET_TYPE customResponse(TC_NetWorkBuffer &in, ResponsePacket& rsp)
{
	size_t len = sizeof(tars::Int32);

	if (in.getBufferLength() < len)
	{
		return TC_NetWorkBuffer::PACKET_LESS;
	}

	string header;
	in.getHeader(len, header);

	assert(header.size() == len);

	tars::Int32 iHeaderLen = 0;

	::memcpy(&iHeaderLen, header.c_str(), sizeof(tars::Int32));

	iHeaderLen = ntohl(iHeaderLen);

	if (iHeaderLen > 100000 || iHeaderLen < (int)sizeof(unsigned int))
	{
		throw TarsDecodeException("packet length too long or too short,len:" + TC_Common::tostr(iHeaderLen));
	}

	if (in.getBufferLength() < (uint32_t)iHeaderLen)
	{
		return TC_NetWorkBuffer::PACKET_LESS;
	}

	in.moveHeader(sizeof(iHeaderLen));

    //parse requestId
	tars::Int32 iRequestId = 0;
	string sRequestId;
	in.getHeader(sizeof(iRequestId), sRequestId);
	in.moveHeader(sizeof(iRequestId));

	rsp.iRequestId = ntohl(*((unsigned int *)(sRequestId.c_str())));
	len =  iHeaderLen - sizeof(iHeaderLen) - sizeof(iRequestId);
    
    //parse buffer
	in.getHeader(len, rsp.sBuffer);
	in.moveHeader(len);

    return TC_NetWorkBuffer::PACKET_FULL;
}
```

Examples of asynchronous calls:
```
class CustomCallBack : public ServantProxyCallback
{
public:
    virtual int onDispatch(ReqMessagePtr msg)
	{
		if(msg->response->iRet != tars::TARSSERVERSUCCESS)
		{
			cout << "ret error:" << msg->response->iRet << endl;
		}
		else
		{
//			cout << "succ:" << string(msg->response->sBuffer.data(), msg->response->sBuffer.size()) << endl;
		}

		++callback_count;
		return msg->response->iRet;
	}
};
CustomCallBackPtr cb = new CustomCallBack();
prx->rpc_call_async(prx->tars_gen_requestid(), "doCustomFunc", buf.c_str(), buf.length(), cb);
```

Last note:
- The design of this protocol resolver can make the service side and the call can easily support any protocol. Of course, the protocol design, for example, meets the next requirement
- Due to the asynchronous feature, this protocol must match the request package and response package through iRequestId (int type)

