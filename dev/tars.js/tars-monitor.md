# Monitor

`Monitor` is a` TARS(TUP) `service and feature monitoring and reporting module.

It consists of 3 sub-modules:

* Service monitoring (stat)
* Property monitoring (property)
* PP monitoring (propertyplus)

## Installation
`npm install @tars/monitor`

## Initialization

__If the service runs through [node-agent](https://github.com/tars-node/node-agent "node-agent") (or on the TARS platform), you don't need to execute this method.__

Initialization is achieved by calling the `init(data)` method of a specific module.

__data__: You can configure the file path for tars or a configured `(@tars/utils).Config` instance.

## Service monitoring (stat)

```js
var stat = require ('@tars/monitor'). stat;
```

The service monitoring mainly counts (reports) the `success, failure, and timeout invocations ` of each request, and collects the `invocation time` when the invocation is successful.

Because other modules have integrated this module, __so in general, the service script does not need to use this module explicitly.__

The integrated modules are as follows:

* TUP Client & Server is reported by [@tars/rpc](https://github.com/tars-node/rpc "@tars/rpc").
* HTTP (S) Server is reported by [node-agent](https://github.com/tars-node/node-agent "node-agent"), but due to the characteristics of the `HTTP(S)` protocol:
* Does not count the amount of timeout calls. The timeout of all requests is reported as 0.
* [response.statusCode> = 400](http://www.nodejs.org/api/http.html#http_response_statuscode "http_response_statuscode") is a failed call, otherwise it is a successful call.

If you decide to report manually, you can use the following code:

```js
stat.report (headers, type [, timeout]);
```

__headers__:
* __masterName__: name of the main module
* __slaveName__: name of the module being called
* __interfaceName__: Interface name of the module to be adjusted
* __masterIp__: Master IP
* __slaveIp__: adjusted IP
* __slavePort__: port being called
* __bFromClient__: reported by the client as `true` and reported by the server as` false`
* __returnValue__: return value, * default value is 0 *

If set to `set` then` headers` also needs to include the following information:
* __slaveSetName__: called `set` name
* __slaveSetArea__: adjusted `set` area name
* __slaveSetID__: set name

If the key is set, then the headers also need to include the following information:
* __masterSetInfo__: Calling `set` information (consisting of setName.setArea.setID)


The value of the parameter `type` is one of` stat.TYPE`, as shown below:

__stat.TYPE__:
* __SUCCESS__: Success
* __ERROR__: failed
* __TIMEOUT__: timeout

If `type === stat.TYPE.SUCCESS` must report the response time-consuming` timeout` _(integer)_

After the data is reported, the user can view the reported data in the service monitoring tab.

## Property monitoring (property)

```js
var property = require ('@ tars / monitor'). property;
```

The feature monitoring report is the “custom feature” of the service script, which consists of the feature name, feature value, and statistical method _(key / value pairs)_.

### property.create (name, policies)

Calling the `create` method will return (or create) a report object, which can be reported by calling the` report (value) `method of the returned object.

Where `name` is the name of the reported attribute value, and` policies` is an instance array of the statistical method class (specifying the statistical method of the data).

```js
property.create ('name', [new property.POLICY.Count, new property.POLICY.Max]);
```

The instance objects in the _`policies` array must not have duplicate statistical methods._

__Please note: Do not call the `create` method to get the report object every time you report. This will cause performance loss.__

### obj.report (value)

`property.create` will return a report object, which can be reported by calling the` report` method of the object.

Data can only be reported once for each call to `report`, and` value` must be a numeric value in general.

After the data is reported, the user can view the reported data in the feature monitoring.

## PP Monitor (propertyplus)

```js
var pp = require('@tars/monitor').propertyplus;
```

PP monitoring allows users to report features through `custom dimensions `and ` custom indicators`, which are composed of dimension names, index values, and corresponding index statistical methods.

Compared with `feature monitoring`, PP monitoring has more dimensions and can be customized more widely. Can output multi-dimensional service monitoring like `service monitoring`.

### pp.create (name [, policies, options])

Calling the `create` method will return (or create) a report object, which can be reported by calling the` report(keys, values) `method of the returned object.

* __name__: Reported log name
* __policies__: an array of statistical method classes (statistical methods for specifying indicator values ​​in order), *default is to use `POLICY.Sum` for each indicator for statistics*
* __options__:
* __notTarsLog__: Whether to report by non-TARS service (the log name is assembled differently, there is no default dimension value), *The default value is: `false`*
* __cacheKeyPolicy__: Whether to enable local cache to improve performance. *Default value: `false`*

```js
pp.create ('name', [property.POLICY.Count, property.POLICY.Max]);
```

The statistical method of the corresponding position in the __`policies` array specifies the statistical policy of the corresponding position of the indicator value array at the time of reporting.__ Therefore, the number of statistical methods should be the same as the number of indicators reported each time, that is, `policies.length === values.length`

All statistical methods except `POLICY.Distr` can be used for this monitoring.

If the number of dimensions reported by the service script (the cardinality of the dimension values) is very large, it is recommended to enable `cacheKeyPolicy` to improve performance and avoid memory overflow.

Do not call the `create` method to get the report object every time you report. This will cause performance loss.

### obj.report (keys, values)

`pp.create` will return a report object, which can be reported by calling the object's` report` method.

Each item in the `keys` array must be __character__, which represents _dimension name_.

Each item in the `values` array must be __value__, which represents _indicator value_.

__The number of dimensions and indicators of the same report object must be the same each time (the order is the same), and the order of the indicator values ​​must be consistent with the order of the `policies` statistical method.__

### Examples

When calling the DB in the service, you need to monitor the DB call. Among them:
 
* Dimension: DB name and corresponding IP
* Metrics: number of calls and average time

```js
var obj = pp.create ('db_status', [pp.POLICY.Sum, pp.POLICY.Avg]);
```

Calling DB: abc@192.168.1.1, takes 12.2ms:

```js
obj.report (['abc', '192.168.1.1'], [1, 12.2]);
```

Call DB: test@127.0.0.1, takes 25.6ms:

```js
obj.report (['test', '127.0.0.1'], [1, 25.6]);
```

## statistical methods

The data reported by `characteristic monitoring` (that is, when` create` is called) needs to specify one or more statistical methods in order to count the values ​​over a period of time. These methods are defined in `POLICY`. They are :

* POLICY.Max: statistical maximum
* POLICY.Min: statistical minimum
* POLICY.Count: Count how many data there are
* POLICY.Sum: add all data
* POLICY.Avg: Calculate the average value of the data
* POLICY.Distr: statistics between partitions

__Except for `property.POLICY.Distr`, no construction parameters need to be passed__

### property.POLICY.Distr (ranges)

`Distr` is statistics between partitions. The interval is divided in advance, and the number of` value` falling into this interval will be automatically counted when reporting.

At the same time, when displaying data, the statistical display between partitions becomes _pie chart type_.

Among them, the parameter `ranges` is an array, each item of which is a value (Int), and they are arranged in ascending order.

E.g:

```js
var monitor = property.create ('name', [new property.POLICY.Distr ([0, 10, 100, 1000])]);
monitor.report (2);
monitor.repott (20);
monitor.report (200);
```

The statistics of the above example are:
  
> [0-10] = 1
> (10-100) = 1
> (100-1000) = 1

_Each interval includes the right side and does not include the left side (except the first interval)_

## Reporting interval

In monitoring and reporting, the data is not reported every time the `report` method is called. The module collects the data submitted within a certain period of time, and performs integrated statistics for one-time reporting (one-way calling).

The module will automatically read the `tars.application.client.report-interval` configuration section (unit: ms) in the TARS configuration file to configure the reporting interval.

__Please note: The configured reporting interval cannot be lower than 10s, nor higher than the `TARS master refresh time` (that is, the` tars.application.client.refresh-endpoint-interval` configuration section).__

In order to prevent cyclic calls, the call status of the reporting module itself is reported by the called party (that is, the reporting logic of the one-way call).