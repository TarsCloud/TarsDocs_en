# tars-registry documentation

## Introduction
The tarsregistry service of the tars platform provides a service discovery function.
This module provides the main control addressing capability (service discovery) for php.

## Instructions

```php
        //find server address from tarsregistry
        $wrapper = new \Tars\registry\QueryFWrapper("tars.tarsregistry.QueryObj@tcp -h 172.16.0.161 -p 17890",1,60000);
        $result = $wrapper->findObjectById("PHPTest.PHPServer.obj");
        var_dump($result);

        //find server address from memory firstly. find from registry then.
        \Tars\registry\RouteTable::getInstance();
        $result = \Tars\registry\RouteTable::getRouteInfo("PHPTest.PHPServer.obj");
        echo "result:\n";
        var_dump($result);
```

## Other

Because other modules have integrated this module, __so in general, the service script does not need to use this module explicitly. __
