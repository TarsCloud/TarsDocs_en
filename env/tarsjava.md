# TarsJava
> * [Environmental Dependency](#chapter-1)
> * [Build Project](#chapter-1)
> * [Config Dependency](#chapter-1)

# <a id="chapter-1"></a>Environmental Dependency

>= JDK 1.8 

>= Maven 2.2.1

# <a id="chapter-2"></a>Build Project

Create a maven web project through ide or Maven. Take eclipse as an example，File -&gt; New -&gt; Project -&gt; Maven Project -&gt; maven-archetype-webapp

Then enter the groupid and artifactid. After the generation, you can import them through eclipse. The directory structure is as follows:

```text
├── pom.xml
└── src
   ├── main
   │   ├── java
   │   │   └── tars
   │   │       └── test
   │   │          ├── HelloServant.java
   │   │          └── HelloServantImpl.java
   │   ├── resources
   │   │   └── servants.xml
   │   └── webapp
   └── test
       ├── java
       │   └── tars
       │       └── test
       │           └── TarsTest.java
       └── resources
```

# <a id="chapter-3"></a>Config Dependency

Add dependent jar package in pom.xml in the build project.

* Framework dependency configuration

```text
<dependency>
	<groupId>com.tencent.tars</groupId>
     	<artifactId>tars-server</artifactId>
     	<version>1.6.1</version>
     	<type>jar</type>
</dependency>
```

* Plugin dependency configuration

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
   			<servant>true</servant>
  			<srcPath>${basedir}/src/main/java</srcPath>
  			<charset>UTF-8</charset>
   			<packagePrefixName>com.qq.tars.quickstart.server.</packagePrefixName>
  		</tars2JavaConfig>
   	</configuration>
</plugin>
```

