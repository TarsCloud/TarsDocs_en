# Directory
> * [TARS Base Protocol](#main-chapter-1)
> * [TUP Intro](#main-chapter-2)
> * [TUP Protocol](#main-chapter-3)
> * [TUP Interface](#main-chapter-4)

## 1 <a id="main-chapter-1"></a> TARS Base Protocol

Before the introduction of TUP, first introduce the underlying protocol format of communication between the tars services. For details, see source: RequestF.tars & BaseF.tars

```RequestF.tars
module tars
{
    struct RequestPacket
    {
        1  require short        iVersion;
        2  require byte         cPacketType  = 0;
        3  require int          iMessageType = 0;
        4  require int          iRequestId;
        5  require string       sServantName = "";
        6  require string       sFuncName    = "";
        7  require vector<byte> sBuffer;
        8  require int          iTimeout     = 0;
        9  require map<string, string> context;
        10 require map<string, string> status;
    };

    struct ResponsePacket
    {
        1 require short         iVersion;
        2 require byte          cPacketType  = 0;
        3 require int           iRequestId;
        4 require int           iMessageType = 0;
        5 require int           iRet         = 0;
        6 require vector<byte>  sBuffer;
        7 require map<string, string> status;
        8 optional string        sResultDesc;
        9 optional map<string, string> context;
    };
};
```

```BaseF.tars
module tars
{
    ////////////////////////////////////////////////////////////////
    // version 

    const short TARSVERSION  = 0x01;
    const short TUPVERSION  = 0x03;

    ////////////////////////////////////////////////////////////////
    // message type
    const byte TARSNORMAL  = 0x00;
    const byte TARSONEWAY  = 0x01;

    ////////////////////////////////////////////////////////////////
    // TARS return value
    const int TARSSERVERSUCCESS       = 0;       
    const int TARSSERVERDECODEERR     = -1;      
    const int TARSSERVERENCODEERR     = -2;      
    const int TARSSERVERNOFUNCERR     = -3;      
    const int TARSSERVERNOSERVANTERR  = -4;      
    const int TARSSERVERRESETGRID     = -5;      
    const int TARSSERVERQUEUETIMEOUT  = -6;      
    const int TARSASYNCCALLTIMEOUT    = -7;      
    const int TARSINVOKETIMEOUT       = -7;      
    const int TARSPROXYCONNECTERR     = -8;      
    const int TARSSERVEROVERLOAD      = -9;      
    const int TARSADAPTERNULL         = -10;     
    const int TARSINVOKEBYINVALIDESET = -11;     
    const int TARSCLIENTDECODEERR     = -12;     
    const int TARSSERVERUNKNOWNERR    = -99;     

    /////////////////////////////////////////////////////////////////
    // message type 

    const int TARSMESSAGETYPENULL     = 0x00;    
    const int TARSMESSAGETYPEHASH     = 0x01;    
    const int TARSMESSAGETYPEGRID     = 0x02;    
    const int TARSMESSAGETYPEDYED     = 0x04;    
    const int TARSMESSAGETYPESAMPLE   = 0x08;    
    const int TARSMESSAGETYPEASYNC    = 0x10;    
    //const int TARSMESSAGETYPELOADED = 0x20;    
    //const int TARSMESSAGETYPESETED = 0x40;     
    const int TARSMESSAGETYPESETNAME = 0x80;     
    const int TARSMESSAGETYPETRACK   = 0x100;    
    /////////////////////////////////////////////////////////////////
}
```

RequestPacket & ResponsePacket is the underlying protocol of the communication between two tars services. Simply speaking, if you do not use the communicator of the tars to communicate, you can pack your own packets to complete the communication with the tars services (of course, it will be quite difficult, and you need to be very familiar with the underlying protocol of the tars). Therefore, in order to facilitate the construction of TUP to solve this problem.

## 2 <a id="main-chapter-2"></a> TUP Intro

### What's TUP

TUP（Tars Uni-Protocol's short name）.

It is the encapsulation of command layer protocol based on tars coding.

It was first used to make it convenient for clients of different languages to call the tars service. It only provides encoding and decoding, and network communication needs to be realized by yourself. Of course, if the tars provides clients of this language, there is no need to use the tup protocol to call the tars service.

In the formal use, we have this scenario for your reference:
- The back-end service is implemented with the tars service
- Implement a full asynchronous proxy of HTTP + tup protocol, that is, the entry is HTTP + tup, and the exit is the proxy of tars protocol
- Android/IOS communicates with any tars service in the background through the tup protocol and the proxy service

**Of course, we will provide a mechanism in the future, which can use HTTP + JSON to complete the communication of the backend tars service**

### What can TUP do

1.support java、c++ etc language

2.Supports serialization and deserialization of objects

3.Support protocol dynamic expansion

4.Provide put/get generic interface to quickly realize client/server coding and decoding

5.You can use Tup to directly call the server of tars

### What Tup can't do

1.Protocol encapsulation only, excluding network layer

2.Data compression is not supported (can be processed at the business layer)

3.Encryption protocol is not supported (can be processed in the business layer)

### Dependencies and constraints

1.Depending on the tars protocol, the structure objects used in the tup must be generated after the tars definition

2.Code generation tools that depend on various languages, such as：tars2cpp/tars2java etc

3.Tars related interfaces (such as getTars... / setTars...) encapsulated in TarsUnipacket need to be used only when calling the tars service

4.In the process of using, use UniPacket to complete the request and corresponding data transmission, where ServantName (service object name) and FuncName (interface name) are the parameters that must be set, otherwise the coding fails

5.It is not recommended to get/put too much data. If there is more data, it is recommended to form a struct in the tars file, and then put/get it into UniPacket, so as to improve efficiency and reduce network packet size

6.The result of UniPacket encoding contains the packet length information of four byte network byte order in the packet header, and the length includes the packet header. After receiving the packet, the receiver needs to judge the packet length according to the content of the packet header, and ensure that the packet is complete, the packet is sent to the decoding interface for decoding (without removing the packet header)

7.It is recommended not to include binary data in the string type interface of tars C + + language. Binary data is transmitted by vector

## 3 <a id="main-chapter-3"></a> TUP Protocol

### Class Structure Diagram

![](../assets/tars_tupclass.png)

### Class usage

1.RequestPacket：The base classes of the request packet and response package are generated by the definition of the tars file (refer to RequestF.tars in the source code). They are consistent with the basic packages of the tars service, and are generally not used directly

2.UniAttribute: Attribute class, users can add attributes and get attributes by operating the objects of this class. The class provides a put/get generic interface and can realize encoding and decoding. The encoded and serialized byte stream can be used for compression, encryption, network transmission or persistent storage to deserialize the original object when necessary

3.UniPacket: The request and response package class inherits from UniAttribute. It can add the attribute value of the request, set the remote object and method name to be requested, and send them to the server after encoding. The server can obtain the attribute parameters for processing after decoding. After the server finishes processing the request, it also returns the result through the object of this class, and the client decodes the result.

4.TarsUniPacket: The tars request and response package class inherits from UniPacket. It is used when calling the remote tars service. After the user adds and sets related properties, they encode them to form a request package that is sent to the tars service through the network for processing. After receiving the request of the tup protocol, the tars server will return the packet to the client with the object group of this class. After receiving the package, the client uses this class to decode and get the result.

### Using the TUP protocol to call the tars service

The following is an example of C + + version, similar to other languages.

1.When the client calls, the TarsUniPacket object is used for parameter setting and input parameter assignment of the request package. The required request parameter information includes:

```text
setRequestId();           Set message ID, auto increasing(must not be 0)

setServantName("");       Set remote servant name

setFuncName("");          Set remote interface name

setTarsPacketType();      Package type version, the default of tup protocol is 3

```

For the call of a specific remote interface, only the input parameters need to be assigned through the put interface. The attribute name is the parameter name defined by the tar interface, for example, for the interface:

```text
int testFunc(string inputString, int inputInt, out string outputString);
```

The input parameters are assigned as follows:

```text
TarsUniPacket<> req;

req.put<string>("inputString", "testInput");

req.put<Int32>("inputInt", 12345);

req.encode(buff);
```

**Here `inputString` match the first parameter of testFunc**

The TarsUniPacket object must set the property value for each input parameter defined by tars interface. Otherwise, the server will return an exception error that a property value is missing when processing the request. The output parameter can also be used as input, but it is not required.

The template type of put interface uses the corresponding type defined by the tars parameter, but enumeration type is replaced by Int32 used as the template type to assign the attribute value.

2. The TUP return package also uses the TarsUniPacket object for decoding. After decoding, use the getTarsResultCode \ (\) interface to obtain the processing results of the tars service. 0 is success, and non-0 is failure. The reason for failure can be obtained through the getTarsResultDesc \ (\) interface.

The output parameters of the returned successful result package are obtained by using the output parameter name defined by tars protocol file as the attribute name, and the return value of the interface is obtained by using the attribute name of an empty string.

As shown in the above interface, the way to obtain the return result is:

```
int testFunc(string inputString, int inputInt, out string outputString);
```
```text
TarsUniPacket<> rsp;

rsp.decode(recvBuff, recvLen);

if(rsp.getTarsResultCode() == 0)
{
    int ret = rsp.get<int32_t>("");						//get return value, type is int
    string retString = rsp.get<string>("outputString"); //get output parameter
}
else
{
    cout <<　rsp.getTarsResultDesc() << endl;
}
```

## 4 <a id="main-chapter-4"></a> TUP Interface Intro

### Linux c++

#### Class

UniAttribute

| function | describe |
| :--- | :--- |
| template void put\(const string& name, const T& t\) | Add attribute value |
| template void get\(const string& name, T& t\) | Get attribute value |
| template T get\(const string& name\) | Add attribute value |
| template void getByDefault\(const string& name, T& t, const T& def\) | Add attribute value\(if it is empty, then def is default value\) |
| template T getByDefault\(const string& name, const T& def\) | Get attribute value\(if it is empty, then def is default value\) |
| void clear\(\) | clear all attribute value |
| void encode\(string& buff\) | encode to buffer |
| void encode\(vector& buff\) | encode to buffer |
| void encode\(char\* buff, size\_t & len\) | encode to buffer |
| void decode\(const char\* buff, size\_t len\) | decode from buffer |
| void decode\(const vector& buff\) | decode from buffer |
| const map&lt;string, vector &gt;& getData\(\) const | get all attributes |
| bool isEmpty\(\) | not attribute exists |
| size\_t size\(\) | get attribute count |
| bool containsKey\(const string & key\) | contain attribute |

UniPacket

| function | describe |
| :--- | :--- |
| void setVersion\(short iVer\) | set protocol verison |
| UniPacket createResponse\(\) | The response package is generated by the request package. The generation process will obtain the request ID, object name, method name, etc. from the request package and backfill them into the response package |
| void encode\(string& buff\) | encode to buffer  |
| void encode\(vector& buff\) | encode to buffer  |
| void encode\(char\* buff, size\_t & len\) | encode to buffer  |
| void decode\(const char\* buff, size\_t len\) | decode from buffer, len is input buffer length and output decoding result length |
| tars::Short getVersion\(\) const | get protocol version |
| tars::Int32 getRequestId\(\) const | get request ID |
| void setRequestId\(tars::Int32 value\) | set request ID |
| const std::string& getServantName\(\) const | get Servant Name |
| void setServantName\(const std::string& value\) | set Servant Name\(can not be empty\) |
| const std::string& getFuncName\(\) const | get function name|
| void setFuncName\(const std::string& value\) | set function name\(can not be empty\) |


UniPacket is inherit from UniAttribute. You can build your own services and use UniPacket to complete service communication. In that case, setServantName can be used as the main command of the protocol, setFuncName as the sub command of the protocol. The underlying package structure of UniPacket is RequestPackage


TarsUniPacket

| function | describe |
| :--- | :--- |
| void setTarsVersion\(tars::Short value\) | set protocol verison, default is 3  |
| void setTarsPacketType\(tars::Char value\) | set package type, see BaseF.tars |
| void setTarsMessageType\(tars::Int32 value\) | set message type, set BaseF.tars |
| void setTarsTimeout\(tars::Int32 value\) | set timeout time(millseconds) |
| void setTarsBuffer\(const vector<tars::Char>& value\) | set buffer |
| void setTarsContext\(const map&lt;std::string, std::string&gt;& value\) | set context |
| void setTarsStatus\(const map&lt;std::string, std::string&gt;& value\) | set status info |
| tars::Short getTarsVersion\(\) const | get protocol version |
| tars::Char getTarsPacketType\(\) const | get package type |
| tars::Int32 getTarsMessageType\(\) const | get message type |
| tars::Int32 getTarsTimeout\(\) const | get timeout |
| const vector<tars::Char>& getTarsBuffer\(\) const | get encode buffer |
| const map&lt;std::string, std::string&gt;& getTarsContext\(\) const | get context |
| const map&lt;std::string, std::string&gt;& getTarsStatus\(\) const | get status |
| tars::Int32 getTarsResultCode\(\) const | Get the tars service processing result code, 0 for success, non-0 for failure |
| string getTarsResultDesc\(\) const | Get the tars service processing result description |

TarsUniPacket inherits UniPacket. When you need to communicate with the TarsUniPacket service, you can use TarsUniPacket to complete service communication. The underlying package structure of TarsUniPacket is RequestPackage

**An error in the above interface call will throw a runtime exeception**

#### Example

see cpp/test/testServant/testTup

### Java

almost same with c++.

#### Notice
1. At present, TUP supports basic types, tarsstruct, and maps and lists for storing basic types or tarsstruct. Only byte\[\] is supported for arrays. Putting other types will throw IllegalArgumentException exception
2. An ObjectCreateException exception will be thrown if the put and get method calls are wrong;



