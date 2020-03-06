# TLS Support

OpenSSL is a very robust and full-featured TLS and SSL protocol library, as well as a general cryptographic algorithm library. It provides security and integrity for our communications. It is a common and important function to support TLS with OpenSSL library. TLS protocol is combined with HTTP in application layer to support HTTPS protocol. In addition, TLS function and [tar authentication function](tars-auth.md) can be enabled at the same time. Currently, only C + + supports TLS function, and Java will support it soon.

see demo: cpp/examples/SSLDemo.

## tars tls use

The use of tar TLS is very simple. You don't need to modify a line of code, just change the configuration file. Just follow these four steps:

### Compile tarscpp, support TLS

The tars framework does not turn on TLS by default. Configure cmake to turn on TLS:

```text
cmake .. -DTARS_SSL=ON
make
make install
```

rebuild tarscpp.

### Modify the endpoint protocol of the server and change TCP or UDP to SSL

In the tars service platform, select the corresponding service, edit the service, modify the endpoint protocol, and change the original tcp or udp to ssl

![](../assets/tars_ssl_endpoint.png)

### Modify server's profile add certificate configuration

In general, TLS uses one-way authentication, that is, client authentication server. So the callee must provide a certificate. No more screenshots. If you don't know how to change the private template, check the screenshots in [tar authentication](tars-auth.md). Add the configuration in the private template as follows:

```text
<tars>
    <application>
    
        <server>
           # ca Public certificate, not filled in by the client without verification
           ca=/certs/ca.pem 
           # not filled in by the client without verification
           verifyclient=0 
           # server public cert
           # server private key
           cert=/certs/server-cert.pem 
           key=/certs/server-key.pem 
        </server>
    </application>
</tars>
```

Now, restart the service on the web platform.

### Modify the caller's profile to add a certificate

Since the main caller can get the address of the callee from the tar registry, the address configuration of the callee here does not need to be changed, as long as the certificate is added. Generally speaking, the main caller does not need to provide a certificate. Here, just configure the PKI public key certificate to verify the server. If the transferred party requires the main dispatching party to provide a certificate, it needs to provide a certificate. As described in Section 2, it will not be repeated here. Modify the private template of the main dispatcher, and configure the TLS certificate as follows:

```text
<tars>
    <application>
        <client>
            # ca Public certificate, authentication server
           ca=/certs/ca.pem 
           # Client public certificate (do not enable two-way authentication)
           cert=/certs/client-cert.pem 
           # Client private Certificate (not allowed if bidirectional authentication is not enabled)
           key=/certs/client-key.pem 
        </client>
    </application>
</tars>
```

Now restart the caller on the web platform.

Without modifying a single line of code (but recompiling the link), your service is already using TLS to encrypt communications.

## Other things you need to know

In the above methods, the server and client are officially unique, that is, different certificates are not bound for different ports. In fact, the framework allows you to enable different certificates for different ports

### Server side

You can add your own certificate in the configuration file under adapter:

```
    <server>
        <Hello2Adapter>
            #ip:port:timeout
            endpoint = ssl -h 127.0.0.1 -p 9007 -t 10000
            #allow ip
            allow	 =
            #max connection num
            maxconns = 4096
            #imp thread num
            threads	 = 5
            #servant
            servant = TestApp.SSLServer.SSL2Obj
            #queue capacity
            queuecap = 1000000
            #tars protocol
	        protocol = tars
            ca          = ../examples/SSLDemo/certs/client1.crt
            cert        = ../examples/SSLDemo/certs/server1.crt
            key         = ../examples/SSLDemo/certs/server1.key
            #default is 0
            verifyclient = 1
        </Hello2Adapter>
    </server>    

```

### Client Side

The client can also add the certificate of a remote service instead of using unified. In the configuration file:

```
    <client>
        <TestApp.SSLServer.SSL2Obj>
            #server crt
            ca                      = ../examples/SSLDemo/certs/server1.crt
            #can be empty
            cert                    = ../examples/SSLDemo/certs/client1.crt
            #can be empty
            key                     = ../examples/SSLDemo/certs/client1.key
        </TestApp.SSLServer.SSL2Obj>
    </client>
```

In addition, tarscpp1. X cannot be used in a simple client, which has been fixed in version 2.0



