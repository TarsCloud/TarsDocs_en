# TarsJava
> * [Define interface file](#chapter-1)
> * [Interface file compilation](#chapter-2)
> * [Service interface implementation](#chapter-3)
> * [Service exposure configuration](#chapter-4)
> * [Service compilation and packaging](#chapter-5) 
> * [RPC Call](#chapter-6)

## 1 <a id="chapter-1"></a> Define interface file

The definition of interface file is defined by the tars interface description language. Create the hello.tars file in the src/main/resources directory. The content is as follows:

```text
module TestApp 
{
	interface Hello
	{
	    string hello(int no, string name);
	};
};
```

## 2 <a id="chapter-2"></a> Interface file compilation

Provide plugins to compile and generate java code, add and generate java file configuration in tars-maven-plugin:

```text
<plugin>
	<groupId>com.tencent.tars</groupId>
	<artifactId>tars-maven-plugin</artifactId>
	<version>1.6.1</version>
	<configuration>
		<tars2JavaConfig>
			<!-- tars file locate-->
			<tarsFiles>
				<tarsFile>${basedir}/src/main/resources/hello.tars</tarsFile>
			</tarsFiles>
			<!-- source encoding-->
			<tarsFileCharset>UTF-8</tarsFileCharset>
			<!-- true:create server source code, false: create client source code-->
			<servant>true</servant>
			<!-- generated source file encoding-->
			<charset>UTF-8</charset>
			<!-- generated source file locate -->
			<srcPath>${basedir}/src/main/java</srcPath>
			<!-- package prefix -->
			<packagePrefixName>com.qq.tars.quickstart.server.</packagePrefixName>
		</tars2JavaConfig>
	</configuration>
</plugin>
```

Execute ```mvn tars:tars2java``` in the project root directory. The HelloServant.java will be created and filled with the following lines of code automatically.

```text
@Servant
public interface HelloServant {

	public String hello(int no, String name);
}	
```

## 3 <a id="chapter-3"></a> Service interface implementation

create HelloServantImpl.java, Implement HelloServant.java interface

```text
public class HelloServantImpl implements HelloServant {

    @Override
    public String hello(int no, String name) {
        return String.format("hello no=%s, name=%s, time=%s", no, name, System.currentTimeMillis());
    }
}
```

## 4 <a id="chapter-4"></a> Service exposure configuration

Create a services.xml configuration file under resources dir. After the service is written, it needs to load the configuration exposure service when the process starts. The configuration is as follows

```text
<?xml version="1.0" encoding="UTF-8"?>
<servants>
	<servant name="HelloObj">
		<home-api>com.qq.tars.quickstart.server.testapp.HelloServant</home-api>
		<home-class>com.qq.tars.quickstart.server.testapp.impl.HelloServantImpl</home-class>
	</servant>
</servants>
```

**in addition to this method, you can also use spring mode to configure services**

## 5 <a id="chapter-5"></a> Service compilation and packaging

Execute mvn package in the project root directory to generate war package, which can be published by the management system later.

## 6 <a id="chapter-6"></a> RPC Call 

* Create client project
* Add dependency

```text
<dependency>
	<groupId>com.tencent.tars</groupId>
   	<artifactId>tars-client</artifactId>
   	<version>1.6.1</version>
   	<type>jar</type>
</dependency>    
```

* Add plugins

  ```text
  <plugin>
   	<groupId>com.tencent.tars</groupId>
   	<artifactId>tars-maven-plugin</artifactId>
   	<version>1.6.1</version>
   	<configuration>
   		<tars2JavaConfig>
   			<tarsFiles>
   				<tarsFile>${basedir}/src/main/resources/hello.tars</tarsFile>
   			</tarsFiles>
   			<tarsFileCharset>UTF-8</tarsFileCharset>
   			<!-- create source code，PS: Client call, must be false here -->
   			<servant>false</servant>
   			<charset>UTF-8</charset>
   			<srcPath>${basedir}/src/main/java</srcPath>
   			<packagePrefixName>com.qq.tars.quickstart.client.</packagePrefixName>
   		</tars2JavaConfig>
   	</configuration>
  </plugin>
  ```

```text
- write code according to service tar interface file
​```java
@Servant
public interface HelloPrx {
      
	public String hello(int no, String name);
      
      	public String hello(int no, String name, @TarsContext java.util.Map<String, String> ctx);
      
      	public void async_hello(@TarsCallback HelloPrxCallback callback, int no, String name);
      
      	public void async_hello(@TarsCallback HelloPrxCallback callback, int no, String name, @TarsContext java.util.Map<String, String> ctx);
}
```

* Synchronous invocation

```text
public static void main(String[] args) {
	CommunicatorConfig cfg = new CommunicatorConfig();
        //create Communicator
        Communicator communicator = CommunicatorFactory.getInstance().getCommunicator(cfg);
        //create proxy by Communicator
        HelloPrx proxy = communicator.stringToProxy(HelloPrx.class, "TestApp.HelloServer.HelloObj");
        String ret = proxy.hello(1000, "HelloWorld");
        System.out.println(ret);
}
```

* Asynchronous call

```text
public static void main(String[] args) {
	CommunicatorConfig cfg = new CommunicatorConfig();
        //create Communicator
        Communicator communicator = CommunicatorFactory.getInstance().getCommunicator(cfg);
        //create proxy by Communicator
        HelloPrx proxy = communicator.stringToProxy(HelloPrx.class, "TestApp.HelloServer.HelloObj");
        proxy.async_hello(new HelloPrxCallback() {
        		
        	@Override
        	public void callback_expired() {
        	}
        		
        	@Override
        	public void callback_exception(Throwable ex) {
        	}
        		
        	@Override
        	public void callback_hello(String ret) {
        		System.out.println(ret);
        	}
        }, 1000, "HelloWorld");
}
```

**Communicator manages the client's resources, preferably globally object**


