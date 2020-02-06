# @tars/utils
TARS Framework Aids Collection

## Installation
```sh
$ npm install @tars/utils
```

## 01. Configuration file parser
```js
var Config = require ('@tars/utils'). Config;
```
### API
#### parseFile(sFilePath, [encoding, callback])
Parse the specified file
* `sFilePath`: filename
* `encoding`: File encoding type. (Default: utf8)
* `callback`: callback function, callback function format function callback (ret, config) {}, where ret is the object {code: return code, success is 0, failure is -1, message: description, exception: if success is undefined, if the failure is an event object}, config is the parser itself

### parseText(sText)
Parse the string, and store the result of the parsing in the internal _data property, you can get the corresponding value through the get method
* `sText`: string
* `return`: true: parsing succeeded, false: parsing failed

#### get(key, defaultValue)
After the file is parsed, the result is stored in an object, and the specified value can be obtained through the get method. Note: If there is the same key in the configuration file / string, when get the value corresponding to the key, not all the values ​​will be obtained, but the value corresponding to the last of the key can also be understood as corresponding to the same key. The value overwrites the previous value.
* `key`: The key value that needs to be valued, the format is x1.x2.x3, where x1, x2, and x3 are deep keys in order. Note: If the key value itself is in the format of x1.x2, take the corresponding key. The value needs to be written as <x1.x2>, see the example for specific usage.
* `defaultValue`: Cannot get the default value of the result

#### getDomain(key, defaultValue)
Get the attribute array of type Object in the value corresponding to key
* `key`: The key value.
* `defaultValue`: Cannot get the default value of the result

#### getDomainValue(key, defaultValue)
Gets the array of property values ​​of type Object in the value corresponding to key
* `key`: The key value.
* `defaultValue`: Cannot get the default value of the result

#### getDomainLine(key, defaultValue)
Get all non-blank lines in the path corresponding to key
* `key`: The key value.
* `defaultValue`: Cannot get the default value of the result
* `return`: array

#### data
Through this property, you can get the results of file parsing

## example

```js
var Config = require('@tars/utils').Config;

var config = new Config();
config.parseFile('./config.conf', 'utf8');

var data = config.data;
console.log('data: ', data);
console.log('get: tars.application.server.local: ', config.get('tars.application.server.local'));
console.log('getDomain: tars.application.server: ', config.getDomain('tars.application.server'));
console.log('getDomainValue: tars.application.server: ', config.getDomainValue('tars.application.server'));
```

For specific examples, see the test-config.js file in the examples directory

## 02. Endpoint tools
```js
var Endpoint = require ('@tars/utils'). Endpoint;
```
### API
#### Class method: parse (desc)
Parse Endpoint information from a string
* `desc`: string, for example: 'tcp -h 127.0.0.1 -p 10000 -t 60000'
* `return`: Returns an Endpoint instance.

#### toString()
Endpoint information into strings

#### copy()
Copy the Endpoint instance

## example

```js
var Endpoint = require ('@tars/utils').Endpoint;

var endpoint = Endpoint.parse ('tcp -h 127.0.0.1 -p 10000 -t 60000');
console.log ('endpoint:' + endpoint.toString());
console.log ('endpoint.copy:' + endpoint.copy(). toString());
```

For specific examples, see the test-endpoint.js file in the examples directory

## 03. timeProvider
```js
var timeProvider = require ('@tars/utils').timeProvider;
```
### API
#### nowTimestamp()
Use Date.now() to get the time. This method is the most efficient. The Date.now() method is about twice as efficient as new Date(). GetTime() and 4 times as process.hrtime().
* `return`: return object

```js
{
    hrtime: // array type, [seconds, nanoseconds],
    timestamp: // unit ms
}
```

#### diff(oTime)
Time interval of the current time relative to oTime
* `oTime`: relative time, object type returned by nowTimestamp function
* `return`: floating point type, time interval, in milliseconds
* Note: nowTimestamp and diff are used in pairs

#### dateTimestamp()
Get the current timestamp, that is, the time from machine startup to the current time (process.hrtime)
* `return`: return object

```js
{
    hrtime: // array type, [seconds, nanoseconds],
    timestamp: // unit ms
}
```

#### dateTimestampDiff(oTime)
Time interval of the current time relative to oTime
* `oTime`: relative time, object type returned by dateTimestamp function
* `return`: floating point type, time interval, in milliseconds
* Note: dateTimestamp and dateTimestampDiff are used in pairs

## example

```js
var timeProvider = require('@tars/utils').timeProvider;

var i = 0, count = 10000000;
var tt1, tt2, interval = 0;
var t1 = new Date().getTime();
var t2 = t1;

tt1 = timeProvider.nowTimestamp();
for(i = 0; i < count; i++) {
    tt2 = timeProvider.diff(tt1);
}
t2 = new Date().getTime();
console.log('【hrTime】interval: ' + (t2 - t1));

t1 = new Date().getTime();
tt1 = timeProvider.dateTimestamp();
for(i = 0; i < count; i++) {
    tt2 = timeProvider.dateTimestampDiff(tt1);
}
t2 = new Date().getTime();
console.log('【hrTime】interval: ' + (t2 - t1));
```

For specific examples, see the test-timer.js file in the examples directory

## 03. Promise Library
```js
var Promise = require ('@tars/utils').Promise;
```

Provide a convenient and unified Promise library for TARS applications. When developing TARS applications, we recommend that you use this library instead of choosing the Promise library yourself. When a better promise solution appears, we can directly replace the implementation in this module and take effect directly for all applications.

```javascript
var Promise = require ("@tars/utils").Promise;
var promise = new Promise (function (resolve, reject) {
     setTimeout (function() {
         resolve (666)
     }, 3000);
});
promise.then (function (data) {
     console.log (data);
});
```

Promises in TARS are currently implemented based on the bluebird library. Bluebird has the best performance among q, bluebird, and native promises.
