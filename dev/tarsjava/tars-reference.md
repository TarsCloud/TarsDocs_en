# Tars Reference

Tars protocol is a protocol based on IDL. Similar to Protocol Buffer, it is language-independent and is a C ++-like identifier language which is used to generate specific service interface files. At the same time, as a binary protocol, compared with common text protocols such as JSON, it has higher encoding and decoding efficiency and smaller network packet space.

The Tars file uses .tars as the extension. For each service in the .tars file, a Java interface will be generated when generating the code. If the server interface code is generated, the Servant suffix will be added. If the code is generated for the client, the Prx suffix will be added. For the grammar rules of Tars language, please refer to [Tars protocol](../../base/tars-protocol.md).



## Tars-maven-plugin

Tars-maven-plugin needs to add related dependencies in the pom.xml file:

```xml
<!--tars2java plugin-->
<plugin>
	<groupId>com.tencent.tars</groupId>
	<artifactId>tars-maven-plugin</artifactId>
	<version>1.7.0</version>
	<configuration>
		<tars2JavaConfig>
			<!-- tars file location -->
			<tarsFiles>
				<tarsFile>${basedir}/src/main/resources/hello.tars</tarsFile>
			</tarsFiles>
			<!-- Source file encoding -->
			<tarsFileCharset>UTF-8</tarsFileCharset>
			<!-- Generate server code -->
			<servant>false</servant>
			<!-- Generated source code encoding -->
			<charset>UTF-8</charset>
			<!-- Generated source code directory -->
			<srcPath>${basedir}/src/main/java</srcPath>
			<!-- Generated source code package prefix -->
			<packagePrefixName>com.tencent.tars.client.</packagePrefixName>
		</tars2JavaConfig>
	</configuration>
</plugin>
```

Some of the configurations are as follows:

- tarsFiles: Indicate the location of the .tars file
- tarsFileCharset: Source file encoding format
- servant: true means to generate server code and Servant suffix interface, false means to generate client code and Prx suffix interface
- charset: The encoding format of the generated source code
- srcPath: Generated source code directory
- packagePrefixName: The package prefix of the generated source code



## Server Tars file

Take hello.tars on the server side in tars-quick-start as an example:

```text
module TestApp
{
	interface Hello
	{
	    string hello(int no, string name);
	};
};
```

TestApp is the namespace, and all structs and interfaces must be in the namespace.

When generating the server code, you need to set the servant tag in the tars-maven-plugin dependency in the pom.xml file to true to obtain the interface file with the suffix Servant, HelloServant.java:

```java
@Servant
public interface HelloServant {

	public String hello(int no, String name);
}
```

After that, the interface can be implemented according to the business logic.



## Client Tars file

When making a service call on the client, you first need to obtain the tars file on the server side, and then you need to set the servant tag in the tars-maven-plugin dependency in the pom.xml file to false when generating the client code to obtain the interface file with the suffix Prx, HelloPrx.java:

```java
@Servant
public interface HelloPrx {

	 String hello(int no, String name);

	CompletableFuture<String>  promise_hello(int no, String name);

	 String hello(int no, String name, @TarsContext java.util.Map<String, String> ctx);

	 void async_hello(@TarsCallback HelloPrxCallback callback, int no, String name);

	 void async_hello(@TarsCallback HelloPrxCallback callback, int no, String name, @TarsContext java.util.Map<String, String> ctx);
}
```

The interface provides three calling modes:

- Synchronous call
- Asynchronous call
- Promise call

In addition, HelloPrxCallback.java is also generated when the client code is generated, which is an abstract class for ordinary asynchronous callback processing:

```java
public abstract class HelloPrxCallback extends TarsAbstractCallback {

	public abstract void callback_hello(String ret);

}
```

This class extends the abstract class TarsAbstractCallback:

```java
@TarsCallback(comment = "Callback")
public abstract class TarsAbstractCallback implements com.qq.tars.net.client.Callback<TarsServantResponse> {

    @Override
    public final void onCompleted(TarsServantResponse response) {
        TarsServantRequest request = response.getRequest();
        try {
            Method callback = getCallbackMethod("callback_".concat(request.getFunctionName()));
            callback.setAccessible(true);
            callback.invoke(this, ((TarsCodec) response.getSession().getProtocolFactory().getDecoder()).decodeCallbackArgs(response));
        } catch (Throwable ex) {
            throw new ClientException(ex);
        }
    }

    private Method getCallbackMethod(String methodName) throws NoSuchMethodException {
        Method[] methods = getClass().getDeclaredMethods();
        for (Method method : methods) {
            if (methodName.equals(method.getName())) {
                return method;
            }
        }
        throw new NoSuchMethodException("no such method " + methodName);
    }

    @Override
    public final void onException(Throwable ex) {
        callback_exception(ex);
    }

    @Override
    public final void onExpired() {
        callback_expired();
    }

    public abstract void callback_exception(Throwable ex);

    public abstract void callback_expired();
}

```

By implementing the callback_exception, callback_expired and callback_hello functions, the corresponding callback logic can be executed.

- When receiving the return from the server, HelloPrxCallback's callback_hello will be responded.
- If the call returns an exception or timeout, then callback_exception will be called.



## Code generation

Run the `mvn tars: tars2java` command in the root directory of the project to generate the corresponding code.