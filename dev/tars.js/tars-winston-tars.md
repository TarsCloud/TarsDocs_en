# @tars/winston-tars

`@tars/winston-tars` provides TARS extensions based on [winston](https://github.com/flatiron/winston "winston") to provide TARS-compliant log formats and output.

Provide 4 types of `transport` objects in` @tars/winston-tars`:

* __TarsBase:__ provides base classes that conform to `TARS logs`
* __TarsRotate:__ provides rolling logs output by size
* __TarsDate:__ provides logs output by date
* __TafRemote:__ output to remote log (tars.tarslog)

And provide a custom log level:

* __TarsConfig:__ Provides log levels and color values that conform to the TARS framework standard

And related helper methods:

* __Formatter:__ provides content formatting methods that conform to the TARS log format standard
* __DateFormat:__ defines the processing method for time-related log scrolling

__Please note: If your service runs on the `TARS platform`, you should use the [@tars/logs](https://github.com/tars-node/logs) module directly, which is more convenient__

## Installation
`npm install @tars/winston-tars`

## use
```js
var winston = require('winston');

// Requiring `@tars/winston-tars` will expose

// transports
// `winston.transport.TarsRotate`
// `winston.transport.TarsDate`

// config
// `winston.config.tars.levels`
// `winston.config.tars.colors`
require('@tars/winston-tars');
```

## Log Format

For `transport` objects that support [formatter](https://github.com/winstonjs/winston#custom-log-format),` @tars/winston-tars` provides 2 methods to format log content:

* __Detailed log:__ Formatter.Detail ([options])
* __Simplified log:__ Formatter.Simple ([options])

__options__:
* __separ__: the separator between the log content items, * default value is | *

### Detailed logs:

```js
var winston = require ('winston');
var winstonTars = require ('@tars/winston-tars');

var logger = new (winston.Logger) ({
  transports: [
    new (winston.transports.Console) ({
    formatter: winstonTars.Formatter.Detail()
    })]
});
```

The format of the output log is: `date time | PID | log level | file name and line number | content`

The file name and line number are optional (see [Metadata](https://github.com/tars-node/winston-tars#metadata) for details)

### Streamlined logs

```js
var winston = require ('winston');
var winstonTars = require ('@tars/winston-tars');

var logger = new (winston.Logger) ({
  transports: [
    new (winston.transports.Console) ({
    formatter: winstonTars.Formatter.Simple()
    })]
});
```

The format of the output log is: `date time | content`

## TarsConfig

`winston.config.tars` provides log levels (` levels`) and colors (`colors`) that conform to the Tars framework standard

The log level of the TARS framework from low to high (and its corresponding color) is:

* info: white
* debug: cyan
* warn: yellow
* error: red
* none: grey

Need to actively introduce when using:

```js
logger.setLevels (winston.config.tars.levels);
winston.addColors (winston.config.tars.colors);
```

## TarsBase

This module can be used alone or as a base class for other logging modules.

The module implements similar management methods to existing `TARS` logs:

* Re-open the log file periodically to get changes to `fd`. (When the user deletes / moves the current file, the module will automatically create a new file)
* During the file opening process, the generated log will be written to the temporary memory queue, waiting to be written once after the file is opened. (As long as the maximum queue length is not exceeded, all logs are written to the file)
* This module uses the filename `options.filename` for caching, so using the same` options.filename` multiple times to instantiate this module will only produce a singleton (share an instance)

### Standalone

```js
winston.add (winston.transports.TarsBase, options)
```

__options__:
* __filename__: output file name
* __interval__: Interval for reopening the log file, *The default value is 5000ms*
* __bufferSize__: the maximum length of the temporary queue (unit is bar), *default value is 10000*
* __prefix__: prefix of each log content, *default value is empty*
* __formatter__: Define the log content formatting method, *The default value is Formatter.Detail()*

### As a base class for other classes

Need to rewrite the following 2 objects:

* TarsBase.prototype.name: Transport module name
* TarsBase.prototype._checkfile (callback): This function will be called when the log file is reopened. After the function is processed, it should call `callback ([err])` explicitly

E.g:

```js
var TarsFile = function (options) {
var instance = TarsBase.call (this, options);

// Since the parent class has a cache, it is necessary to determine whether the parent class has a return value. If it exists (hits the cache), it returns directly without further initialization.

if (instance) {
return instance;
}

	//Business code
};
util.inherits (TarsFile, TarsBase);

TarsFile.prototype.name = 'tarsFile';

TarsFile.prototype._checkfile = function (cb) {
	//Business code
	cb();
};

winston.transports.TarsFile = TarsFile;
```

## TarsRotate

This module inherits from TarsBase

Provides rolling log output by file size

When the set maximum size is reached, it will automatically scroll down and create a new log file.

For example, if the app.log file is written to the maximum size, the following process will be performed:

> delete app \ _n.log  
> app \ _n-1.log ===> app \ _n.log  
> ... ...  
> app \ _1.log ===> app \ _2.log  
> app.log ===> app \ _1.log  
> create app.log  

```js
  winston.add (winston.transports.TarsRotate, options)
```

__options__:
* __filename__: output file name
* __maxFiles__: maximum total number of files (ie n in the example), *default is 10*
* __maxSize__: maximum file size (unit is bytes), *default is 10M*
* __concatStr__: The connector between characters in the log file name, *The default value is _*
* __formatter__: Define the log content formatting method, *The default value is Formatter.Detail()*

### TarsRotate.Master

If the business script passes [Cluster](http://www.nodejs.org/api/cluster.html "Cluster") (multi-process start): Worker only writes the file negatively, and moves the file by the master. To solve the problem of multi-process resource competition.

When the service process (Worker) opens the log file for writing, it sends a message to the main process, as follows:

```js
{
	cmd: 'log: rotate',
	msg: {
		filename: String, // file name
		interval: Number, // how often to open the file
		maxFiles: Number, // Maximum number of files
		maxSize: Number, // Maximum single file size
		concatStr: String // Concatenator between characters in log file name
	}
}
```
After the master process receives the message, it needs to pass it to the `TarsRotate.Master.start` method. The complete example is as follows:

```js
worker.on ('message', function (msg) {
if (msg && typeof msg === 'object' && msg.cmd === 'log: rotate') {
var data = msg.msg;
TarsRotate.Master.start (data.filename, data.interval, data.maxFiles, data.maxSize, data.concatStr);
}
});

process.on ('exit', function() {
TarsRotate.Master.close();
});
```

__If the service runs through [node-agent](https://github.com/tars-node/node-agent "node-agent") (or on the TARS platform, you don't need to configure the platform), no explicit Call this module. Just follow the usual writing `console. [Log | info | warn | error]` to output the rolling log correctly__

## DateFormat

Defines the processing method for time-dependent log (`TarsDate`) scrolling:

* Log by day: LogByDay ([interval, pattern])  
* __interval__: 1 _Roll a log every other day_
* __pattern__:% Y% m% d _year month day_ (eg: 20150101)
* Log by 1 hour: LogByHour ([interval, pattern])
* __interval__: 1 _Roll a log every hour_
* __pattern__:% Y% m% d% H _year month day hour_ (eg: 2015010110)
* Log by 10 minutes: LogByMinute ([interval, pattern])
* __interval__: 10 _Roll a log every ten minutes_
* __pattern__:% Y% m% d% H% M _ year, month, day, hour, minute_ (eg: 201501011020)
* Custom format log: LogByCustom (pattern)
* __pattern__: user-defined

Where `pattern` is the time format in the log file name, see` linux strftime`

### Examples

Rolling a log every other day
> DateFormat.LogByDay  
> Or  
> new DateFormat.LogByDay()

Roll a log every 20 minutes
> new DateFormat.LogByMinute (20)

Scroll a log every 20 minutes, the time format in the file name is% Y-% m-% d_% H:% M
> new DateFormat.LogByMinute (20, '% Y-% m-% d_% H:% M')


## TarsDate

This module inherits from TarsBase

Provides logs output by date(year, month, day, hour, minute)

When the set time interval is reached, a new log file is automatically created

The format of the output file name is: filename\_[% Y |% m |% d |% H |% M] .log, such as: app\_20141015.log

```js
  winston.add (winston.transports.TarsDate, options)
```

__options__:
* __filename__: output file name
* __concatStr__: The connector between characters in the log file name, *The default value is _*
* __format__: interval for creating a new file, as a DateFormat object, *default value is FORMAT.LogByDay*
* __formatter__: defines the log content formatting method. *The default value is Formatter.Simple()*

For convenience use TarsDate.FORMAT = DateFormat


## TarsRemote

Provides the function of remote logging, which will output logs to the `tars.tarslog.LogObj` service.

Please note: This is not to send the log as soon as it is received, but to integrate the log and send it regularly.

```js
  var logger = new (winston.Logger) ({
    transports: [
      new (winston.transports.TarsRemote) (options)
    ]
  });
```

__options__:
* __filename__: remote log file name (no need to include date, path and other additional information)
* __tarsConfig__: tars configuration file path or configured `@ tars / utils.Config` instance
* __tarsLogServant__: remote log service Servant Obj, *read configuration file `tars.application.server.log` section by default*
* __interval__: Interval for sending logs, *The default value is 500ms*
* __format__: interval for creating a new file, as a DateFormat object, *default value is FORMAT.LogByDay*
* __hasSufix__: whether the log file name has a .log suffix, *default is true*
* __hasAppNamePrefix__: Whether to allow the framework to add a business-related identifier to the log file name. *The default value is true*
* __concatStr__: The connector between user-defined characters and date characters in the log file name. *The default value is _*
* __separ__: separator between log content items, *default is |*
* __formatter__: Define the log content formatting method, *The default value is Formatter.Detail()*

_Please note: `TarsRemote`` options.format` cannot be `FORMAT.LogByCustom`_

For convenience, TarsRemote.FORMAT = DateFormat

__If the service runs through [node-agent](https://github.com/tars-node/node-agent "node-agent") (or on the TARS platform), you do not need to configure the options.tarsConfig item__


## Metadata

By specifying [Metadata](https://github.com/winstonjs/winston#logging-with-metadata "Metadata"), two kinds of additional data can be output.

### pid

With the pid attribute, you can specify the _process id_ part of the log output entry, which is `process.pid 'by default

Such as:

```js
logger.info ('data', {
	pid: 123456
});
```

Then output:

> 2015-01-01 00: 00: 01 |__123456__ | INFO | data


### lineno

With the `lineno` attribute, you can specify the _file name and line number_ part of the log output entry. The default value is empty (that is, this section is not output).

Such as:

```js
logger.info ('data', {
	lineno: 'app.js: 6'
});
```

Then output:

> 2015-01-01 00:00:01|123456|INFO|__app.js:6__ |data