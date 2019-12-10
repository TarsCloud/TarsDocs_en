# TarsJava 常见问题

[Permalink](https://github.com/TarsCloud/TarsDocs/blob/293528ce7047760f61730cca487baf6d6aff9214/chang-jian-wen-ti/tarsjava-chang-jian-wen-ti.md)

### Join GitHub today

GitHub is home to over 40 million developers working together to host and review code, manage projects, and build software together.

[Sign up](https://github.com/join?source=prompt-blob-show) **1** contributor

###  Users who have contributed to this file

 12 lines \(6 sloc\) 487 Bytes

## TarsJava 常见问题

### Tars java开发环境如何搭建？

> * 参考tars\_install.md

### 如何在本地启动和开发调试tars

> * 首先在工程目录下执行 mvn tars:build -Dapp=TestApp -Dserver=HelloJavaServer -DjvmParams="-Xms1024m -Xmx1024m -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false -Xdebug -Xrunjdwp:transport=dt\_socket,address=9000,server=y,suspend=n"
> * 在工程目录target/tars/bin/tars\_start 启动服务

