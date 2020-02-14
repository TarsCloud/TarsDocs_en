# TarsPHP FAQ

### Q: Where can check service log?

A: /usr/local/app/tars/app\_log/$APPNAME/$SERVERNAME/$APPNAME.$SERVERNAME.log. Don't forget to check php\_error.

### Q: Class 'TARS\_Struct' not found in /usr/local/app/tars/tarsnode/data/PHPTest.PHPHttpServer/bin/src/vendor/phptars/tars-report/src/ServerInfo.php on line 5

A: Please check whether installed phptars. 
   Command: php -m \| grep phptars
   Installation: please check script and documentation of auto and auto7 in /php/php\_extension.

### Q: The tcp service does not return packets correctly when http and tcp services are built. Why

A: Check whether the status of the tcp service is normal, and check whether the service cannot be started due to the php\ _ error and the permission in the business log. Check whether the tcp service uses the correct template. For example, you cannot specify protocolName as http, because the tcp service must use the tars protocol for communication, and you must specify configuration parameters such as package\ _ length of swoole automatic parcel. Use tcpdump-iany-s0 port tcp service port-XNNnn to grab packets to see if the http service has properly sent packets to the tcp service.

### Q: Can I start php service without tars platform?

A: Obviously, it is possible to go directly to the service directory: /usr/local/app/tars/tarsnode/data/PHPTest.PHPHttpServer/bin there will be two scripts tars\ _ stop.sh and tars\ _ start.sh, that can directly start and stop the service as long as they have permission.

### Q: Why part of methods to request tcp work, part cannot?

A: Maybe you use format tool, which hurt server format. (eg. examples/Tars-tcp-server/src/servant/PHPtest/PHPServer/obj/TestTafServiceServant.php\). Please download the lastest version in GitHub, or re-build interface of server by tars2php.

### Q: How to deal with it cannot get parameters, and it shows 'illegalargumentException' after calling java service, .

A: Java support for tup has not been released before. But now you need to recompile the two jar packages tars-core and maven-tars-plugin, and add the word true to the plugin configuration in the pom.xml of the project, so that you can support php to call the default tup protocol after generation. The regenerated interface file will be annotated: @ TarsMethodParameter, so that it can be parsed normally.

### Attention

Make sure you install phptars extensions. If you use server, please install swoole extensions also.

