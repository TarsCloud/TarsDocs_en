# Tars-Config
Used to get config files in Tars framework
## Installation
`npm install @tars/config`

## Instantiation

Instantiate the object before using it:   
`var config = new TarsConfig (data) `

__data__: The path to the config file or the configured `@tars/config-parser` instance.

__If the server runs by [node-agent](https://www.npmjs.com/package/@tars/node-agent "node-agent") （or on Tars platform，then you don't need to pass `data` 。__

## Enum

### FORMAT

Defines the format of the config file:

* __C__: C++ Config file formart
* __JSON__: JSON format
* __TEXT__: Normal text（Custom format）

### LOCATION

Defines the position where the config files are stored:

* __APP__: Config files are stored in the application
* __SERVER__: Config files are stored in the servers

## Methods

### loadConfig([files ,]options)

Get config file content。

`files(String|Array)` It can be a single file name or an array, and if it is left empty, all the file contents will be obtained by default.

`options(Object)` Optional, accept the following parameters:

* __format__: File format， *default to e FORMAT.C*   
* __location__: Stored position， *default to be LOCATION.SERVER*

Return array of following objects after call success:

* __filename__: Filename
* __content__: Content after file parsing

__If only get one single file, it will return the parsed content of the file.__

#### Example

Get the content of `a.conf` ：
```javascript
config.loadConfig("a.conf").then(function(data) {
    console.log("content:", data);
}, function (err) {
    console.error("loadConfig err", err);
});
```

Get the content of `a.conf` and parse by json：
``` javascript
config.loadConfig("a.conf", {format : config.FORMAT.JSON}).then(function(data) {
    console.log("content:", data);
}, function (err) {
    console.error("loadConfig err", err);
});
```

Get content of `a.conf` which is stored in application：
``` javascript
config.loadConfig("a.conf", {location : config.LOCATION.APP}).then(function(data) {
    console.log("content:", data);
}, function (err) {
    console.error("loadConfig err", err);
});
```

Get content of `a.conf` and `b.conf` ：
``` javascript
config.loadConfig(["a.conf", "b.conf"]).then(function(data) {
    data.forEach(function(item) {
        console.log("filename:", item.filename);
        console.log("content:", item.content);
    });
}, function (err) {
    console.error("loadConfig err", err);
});
```

Get all config files of server：
``` javascript
config.loadConfig().then(function(data) {
    data.forEach(function(item) {
        console.log("filename:", item.filename);
        console.log("content:", item.content);
    });
}, function (err) {
    console.error("loadConfig err", err);
});
```

### loadList(options)

Get list of config files（name of all config files）。

`options(Object)` Optional, accept the following parameters:

* __location__: Stored position， *default to be LOCATION.SERVER*

Return array of file names after call success.

#### Example

Get all config file names of server:
```javascript
config.loadList().then(function(filelist) {
    console.log("files:", filelist);
}, function(err) {
    console.log("loadList error", err);
});
```

### loadServerConfig(options)

Get default config file（name is like `App.Server.conf`）。

`options(Object)` Optional, accept the following parameters:

* __format__: File format， *default to be FORMAT.C*   

Return parsed Content of config file after call success.
#### Example

Get default config file：
```javascript
config.loadServerConfig().then(function(data) {
    console.log("content:", data);
}, function(err) {
    console.log("loadServerConfig error", err);
});
```

## Events

### configPushed

Emited when push config file to Tars platform.

The callback function while provide pushed filename.

#### Example

Listen to push event, and get pushed file content :
```javascript
config.on("configPushed", function(filename) {
    console.log("config pushed", filename);
    config.loadConfig(filename).then(function(data) {
        console.log("content:", data);
    }, function(err) {
        console.error("loadConfig err", err);
    });
});
```