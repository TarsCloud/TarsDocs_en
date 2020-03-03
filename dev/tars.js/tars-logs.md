# @tars/logs

Based on [winston](https://github.com/flatiron/winston) and [winston-tars](https://github.com/tars-node/winston-tars), a log component conforming to the TARS framework specification, It contains staining logs, rolling (size, time) logs.


## Installation
`npm install @tars/logs`


## Examples

Output rolling log
```js
var logger = new tarsLogs ('TarsRotate');
```

Output daily log named access
```js
var logger = new tarsLogs ('TarsDate', 'access');
```

Output daily logs named access, only local and not remote logs
```js
var logger = new tarsLogs ('TarsDate', 'access', {
logTo: tarsLogs.LogTo.Local
});
```

Output an hourly log named access
```js
var logger = new tarsLogs ('TarsDate', 'access', {
format: tarsLogs.DateFormat.LogByHour
});
```

Output a 20-minute scroll log named access in the format of 2015-01-01_10: 00
```js
var logger = new tarsLogs ('TarsDate', 'access', {
format: new (tarsLogs.DateFormat.LogByMinute) (20, '% Y-% m-% d_% H:% M')
});
```

Specify output `INFO` level log information  
```js
logger.info ('data');
logger.info ('data1', 'data2', 'data3');
```

Specifies that the current `INFO` level log output needs to be dyed
```js
logger.info ('data', logger.getDyeingObj (true));
```

## Initialization

__No need to configure this if the service is running on the `TARS platform`__

__If the service is debugged in the local environment, all log types will be output to the Console__

Can be initialized by calling the tarsLogs.setConfig (data) static method

`data (String | Object)` can be a tars configuration file path or a configured `(@tars/utils).Config` instance.

## use

### Instantiation

```js
var tarsLogs = require ('@ tars / logs');

var logger = new tarsLogs (type, [name, options]);
```

`type (String)` log type:

* __TarsRotate__: Scroll logs by size
* __TarsDate__: Roll logs by time
* __TarsRemote__: remote log

`name (String)` User-defined file name (optional)

`options (Object)` According to different log types, there are different parameters, but the following parameters are shared by each type:
  
* __hasSufix__: whether the log file name has a .log suffix, * default is true *
* __hasAppNamePrefix__: Whether to allow the framework to add a business-related identifier to the log file name. * The default value is true *
* __concatStr__: The connector between characters in the log file name, * The default value is _ *
* __separ__: separator between log content items, * default is | *

For other parameters in `options`, please refer to the description of different log types.

__Under normal circumstances, the same log file should share the same `logger`, instead of instantiating multiple times__

### Log output

There are 4 log levels `INFO`, `DEBUG`,` WARN`, and `ERROR`, which can be output by corresponding methods

```js
logger.info ([data], [...]);
logger.debug ([data], [...]);
logger.warn ([data], [...]);
logger.error ([data], [...]);
```

The method supports multiple parameters, see [util.format ()] for details.

For dyeing, please see the section `Staining`

### File name and line number

By default, the output log contains `filename: line number` where the code that calls the output method is located.

The following examples can be customized (for packaging modules) or turned off (to improve performance).

Turn off file name and line number output:

``` js
logger.info('data', {
	lineno : false
});
```

Custom file name and line number output:

```js
logger.info ('data', {
lineno: 'app.js: 123'
});
```

For more details, please refer to [@tars/winston-tars.Metadata](https://github.com/tars-node/winston-tars#metadata)

### Log level

The priority of the log level is: `INFO` <` DEBUG` <`WARN` <` ERROR` <`NONE`

Among them, except that the default level of TarsRotate is `DEBUG`, all others are` INFO`.

If you need to change the log level, you can call the `logger.setLevel (level)` method and pass in the required log level:

```js
logger.setLevel ('info');
logger.setLevel ('none'); // none is a special log level, all logs are not output
```

If the service is running on the `TARS platform`:

* The module will receive the management command of `Log Level Change` and automatically change the current log level.
* The module reads the `tars.application.server.logLevel` section in the TARS configuration file to configure the log level.
* The above two log level configurations are only effective for `Rolling logs by size (TarsRotate)`.

### Format of the log content (Formatter)

The module provides two simplified log processing methods: Formatter.Simple () and complex Formatter.Detail ():

* __Complicated__: `date time | PID | log level | file name and line number | content`
* __Less__: `datetime | contents`

Different log types use different processing methods by default.

For more information on `Formatter`, please visit [@tars/winston-tars.Formatter](https://github.com/tars-node/winston-tars)

## Scroll logs by size (TarsRotate)

When initializing a `logger` of type` TarsRotate`, `options` also accepts the following parameters:

* __maxFiles__: maximum total number of files (ie n in the example), *default is 10*
* __maxSize__: maximum file size (unit is bytes), *default is 10M*
* __formatter__: Define the log content formatting method, *The default value is Formatter.Detail ()*

For more information on `TarsRotate`, please visit [@tars/winston-tars.TarsRotate](https://github.com/tars-node/winston-tars#tarsrotate)

## Time correlation (DateFormat)

Defines the processing method of time-related logs (`TarsDate`,` TarsRemote`) scrolling:

* Log by day: LogByDay ([interval, pattern])
* Log by 1 hour: LogByHour ([interval, pattern])
* Log by 10 minutes: LogByMinute ([interval, pattern])
* Custom format log: LogByCustom (pattern)

Among them, "interval" is the log rolling interval, and "pattern" is the format of the time in the log file name

In general, you can use literals directly:

```js
var logger = new tarsLogs ('TarsDate', 'access', {
	format: tarsLogs.DateFormat.LogByDay
});
```

But if you need a custom interval or log file name, you need to instantiate:

```js
var logger = new tarsLogs ('TarsDate', 'access', {
	format: new tarsLogs.DateFormat.LogByDay (3, '% Y-% m-% d');
});
```

For more information on `DateFormat`, please visit [@ tars / winston-tars.DateFormat] (https://github.com/tars-node/winston-tars#dateformat)

## Scroll logs by time (TarsDate)

When initializing a `logger` of type` TarsDate`, `options` also accepts the following parameters:

* __format__: interval for creating a new file, as a DateFormat object, * default value is FORMAT.LogByDay *
* __formatter__: defines the log content formatting method. * The default value is Formatter.Simple () *
* __logTo__: The destination of the log, which is LogTo enumeration, * The default value is LogTo.Both *

There are 3 items in the `LogTo` enumeration:

* __LogTo.Remote__: only remote log (tars.tarslog.LogObj)
* __LogTo.Local__: only log local
* __LogTo.Both__: remote + local

_Please note: `Options.format` can only be` DateFormat.LogByCustom` when `LogTo.Local`_

For more information on `TarsDate`, please visit [@tars/winston-tars.TarsDate](https://github.com/tars-node/winston-tars#tarsdate)

## Remote Log (TarsRemote)

Due to the complexity of remote log implementation (and parameters), please visit [winston-tars.TarsRemote](https://github.com/tars-node/winston-tars#tarsremote) for details.

## dyeing

At the time of writing each log, you can specify whether the log needs to be dyed (that is, the switch for dyeing is not at the time of initialization but when the log is written).

Only the logs output by TarsRotate and TarsDate can be dyed.

The dyed log is not only output according to the previous logic, but also an additional copy will be output under the same logic and placed in the `$LOG_PATH$/tars_dyeing/` directory.

The stain log is always output in full (ignore the current log level for output).

### Dyeing objects

The staining object identifies the current staining status (whether staining is required and additional information).

Dyeing objects need to be generated by the method provided by [@ tars/dyeing](https://github.com/tars-node/dyeing).

For ease of use, this module encapsulates the generation method of dyed objects. You can get the dyeing object through `logger.getDyeingObj()`.

Open stain  
```js
logger.getDyeingObj(true);
```

Open the dye and set the dyed val to guid and key to guidsn
```js
logger.getDyeingObj (true, 'guid', 'guid | sn');
```

__In practical applications, this method should not be called to generate dyeing objects, but should be used directly from other modules .__

Other modules that conform to the dyeing standard will provide the `Object.getDyeingObj()` method, which can be used to get the dyeing object instead of using the module's methods.

For more information about dyeing, please visit [@tars/dyeing](https://github.com/tars-node/dyeing).

### use

When you need to dye a log, you need to pass the `staining object` as the last parameter of the`logger` specific method (log level).

Output the log content as data1 data2 and force the dyed log
```js
logger.info ('data1', 'data2', logger.getDyeingObj (true));
```

Output the log content as `data` and dye the log based on the dyeing information on the`rpc.server` call chain
```js
tars.TestImp.prototype.echo = function (current, i) {
   logger.info ('data', current.getDyeingObj ());
}
```

_`rpc` For details on how to obtain dyed objects, please refer to [@tars/rpc](https://github.com/tars-node/rpc)_
