## TarsBenchmark Instructions

tb can download source code to [Compile](build.md), The tool supports to benchmark services of [Http Protocol](http-guide.md) and [Tars Protocol](tars-guide.md)  by default, It can also flexibly support the other third protocol development service.

### Third protocol development
If your service agreement is not Tars or Http, to adapt to develop a new service protocol according to the following specifications, you can also use the underlying mechanism of tb to achieve the target service high-performance and real-time feedback. The tool discovers new protocols through the dynamic registration mechanism. Developers do this by implementing {new} Protocol.h and {new} Protocol.cpp. Be sure to declare these two macros: DECLARE_DYNCREATE/IMPLEMENT_DYNCREATE (which must be declared in cpp), and complete the process in three steps:

 - 1.Refer to httpProtocol to inherit the Protocol protocol class.
 - 2.Implement the three function of input/encode/decode.
 - 3.Implement the initialization function to intercept the parameters required by the command line.

```cpp
class httpProtocol : public Protocol
{
    DECLARE_DYNCREATE(httpProtocol)
public:
    httpProtocol() {}
    virtual ~httpProtocol() {}

    /**
     * @brief  initialize
     *
     * @param argc number of argv
     * @param argv parameter
     *
     * @return 0:succ, other: fail
     */
    virtual int initialize(int argc, char** argv);

    /**
     * @brief  isSupportSeq
     *
     * @return boolean
     */
    virtual int isSupportSeq() { return false; }

    /**
     * @brief  input
     *
     * @param buf  buffer point
     * @param len  buffer length
     *
     * @return int =0incomplete; >0:real packet length; <0:wrong，
     */
    virtual int input(const char *buf, size_t len);

    /**
     * @brief  encode
     *
     * @param req  request
     * @param len  buffer length
     * @param uniqId  unique ID
     *
     * @return 0:succ, <0: fail, >0 real buffer length
     */
    virtual int encode(char *buf, int& len, int& uniqId);

    /**
     * @brief  decode
     *
     * @param buf  buffer point
     * @param len  buffer length
     * @param uniqId  unique ID
     *
     * @return 0:succ, other: fail
     */
    virtual int decode(const char *buf, int len, int& uniqId);
};
```


Before starting the development work, you need to know which mode the target service of the stress test belongs to. Specify it by implementing the isSupportSeq method. The default is the unordered mode.

**Ordered Mode**
In this mode, the benchmark client sends a request to the target server at a constant speed at the specified rate, and does not rely on the server's last request to return. When sending the request, the client will generate a **globally unique id** to the server, and when the server returns the packet, it will bring this id to the client. When the interface function is passed through uniqId, the tb tool will transfer the globally unique id to the new protocol module in encode. Decode sends the id returned by the server to the server by rewriting the uniqId.
![ordered](../assets/tb_ordered.png)


**Disordered Mode**
In this mode, the server does not return the sequence number to the client, and the benchmark client will send the request to the target server at a uniform speed below the specified rate, which strongly depends on the return of the server. The response or timeout of the previous request must be received before sending the next request. The following figure is an example.
![disordered](../assets/tb_disordered.png)
