# TarsJava FAQ

### How to start and develop/debug tars

> * 1. mvn tars:build -Dapp=TestApp -Dserver=HelloJavaServer -DjvmParams="-Xms1024m -Xmx1024m -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false -Xdebug -Xrunjdwp:transport=dt\_socket,address=9000,server=y,suspend=n"
> * 2. start service in target/tars/bin/tars\_start 

