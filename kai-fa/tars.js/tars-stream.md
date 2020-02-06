
# 00-Installation
> $ npm install @ tars/stream

# 01-Basic introduction and usage of stream module
The stream module is used as the Tars (tars / TUP) basic protocol codec library. This module can be used to encode and decode data streams based on the tars protocol description format, and can communicate with TARS servers and terminals that currently use the tars protocol.

The tars codec module workflow generally has the following three methods:

### The first is to use the tars file as the communication bridge between the caller and the server (the parties agree that the final agreement shall be based on the tars file).
The tars file is the protocol description file that ends with ".tars".

The tars file is generally developed by the back-end development. The front-end development needs to ask the back-end development for the confirmed tars file, and then convert it into a source code file suitable for NodeJS through tools.

```c ++
module TRom
{
    struct User_t
    {
        0 optional int id = 0;
        1 optional float score = 0;
        2 optional string name = "";
    };

    struct Result_t
    {
        0 optional int id = 0;
    };

    interface NodeJsComm
    {
        int test ();

        int getall (User_t stUser, out Result_t stResult);

        int getUsrName (string sUsrName, out string sValue1, out string sValue2);

        int secRequest (vector <byte> binRequest, out vector <byte> binResponse);
    };
};
```
For example, after we save the above as "Protocol.tars", we can use the following command to generate different files:

> $ tars2node Protocol.tars

The above command will ignore the interface description section, and only convert the data types such as "constant", "enumeration value", and "structure" defined in the file, for developers to use the Tars framework as the codec library file when calling tools. The generated file name is "Protocol.js".


> $ tars2node Protocol.tars --client

The above command not only converts data types such as "constant", "enumeration value", "structure" defined in the file, but also translates the description section of the interface into the RPC call framework. The resulting file is called "ProtocolProxy.js" and is used by the caller. After the developer introduces this file, he can directly call the server-side service. For specific usage, please refer to the documentation of the "npm install rpc" module.


> $ tars2node Protocol.tars --server

The above command not only converts data types such as "constant", "enumeration value", "structure" defined in the file, but also translates the description section of the interface into the server-side interface file. The generated file names are "Protocol.js" and "ProtocolImp.js". The developer should not modify "Protocol.js", but only need to continue to improve "ProtocolImp.js", and implement the specific functions in the file to serve as the Tars server. Provide services. For specific usage, please refer to the documentation of the "npm install rpc" module.


### Second, there is no protocol description file, when we need to write codec code by ourselves.
For example, the service background provides the function of purchasing a certain product, which requires four parameters, such as "user number", "user nickname", "item number" and "number of products".
The background numbers of these four parameters (that is, the tags in tars) are 0, 1, 2, and 3, respectively.  

```javascript
// First step, introduce tars / TUP codec library
var Tars = require ("@tars/stream");

// The second step, the client encodes the input parameters according to the server's requirements
var ost = new Tars.TarsOutputStream ();
ost.writeUInt32 (0, 155069599); // write "user number"; "0" on the server side represents "user number".
ost.writeString (1, "KevinTian"); // write "user nickname"; on the server side "1" stands for "user nickname".
ost.writeUInt32 (2, 1002121); // Write “Product Number”; “2” on the server side stands for “Product Number”.
ost.writeUInt32 (3, 10); // write "number of products"; "3" on the server side means "number of products".

// The third step, the client sends the packed binary buffer to the server
send (ost.getBinBuffer (). toNodeBuffer ()) to server

// Fourth step, the server receives the complete request binary buffer from the client
recv (var requestBuffer = new Buffer ()) from client

// Fifth step, decode and deserialize the request
var ist = new Tars.TarsInputStream (new Tars.BinBuffer (requestBuffer));

var uin = ist.readUInt32 (0, true); // Read "user number" according to the number "0".
var name = ist.readString (1, true); // Read "user nickname" according to the number "1".
var gid = ist.readUInt32 (2, true); // Read the "item number" according to the number "2".
var num = ist.readUInt32 (3, true); // Read the "number of products" according to the number "3".

// The sixth step is to perform corresponding logical operations according to the relevant incoming parameters
console.log ("name:", name);
console.log ("num:", num);
...
```
### Third, the server accepts data in the TUP protocol format.
```js
// First step, introduce tars / TUP codec library
var Tars = require ("@tars/stream");

// The second step, the client encodes the input parameters according to the server's requirements
var tup_encode = new Tars.Tup ();
tup_encode.writeUInt32 ("uin", 155069599); // The variable name of the server interface function "user number" is "uin".
tup_encode.writeString ("name", "KevinTian"); // The variable name of the server interface function "user nickname" is "name".
tup_encode.writeUInt32 ("gid", 1002121); // The variable name of the server interface function "item number" is "gid".
tup_encode.writeUInt32 ("num", 10); // The variable name of the server-side interface function "number of products" is "uum".

var BinBuffer = tup_encode.encode (true);

// The third step, the client sends the packed binary buffer to the server
send (BinBuffer.toNodeBuffer ()) to server

// Fourth step, the server receives the complete request binary buffer from the client
recv (var requestBuffer = new Buffer ()) from client

// Fifth step, decode and deserialize the request
var tup_decode = new Tars.Tup ();
tup_decode.decode (new Tars.BinBuffer (requestBuffer));

var uin = tup_decode.readUInt32 ("uin"); // The server reads the "user number" according to the variable name "uin".
var name = tup_decode.readString ("name"); // The server reads the "user nickname" according to the variable name "name".
var num = tup_decode.readUInt32 ("num"); // The server reads the "item number" according to the variable name "gid".
var gid = tup_decode.readUInt32 ("gid"); // The server reads the "number of products" according to the variable name "num".

// The sixth step is to perform corresponding logical operations according to the relevant incoming parameters
console.log("name:", name);
console.log("num :", num);
......
```

# 02 - Data types supported by stream and how to use them
**Basic data types**

| Data type | Corresponding data types in c++|
| ------------- | ------------- |
| bool | bool |
| signed integer | char(int8)、short(int16)、int(int32)、long long(int64) |
| unsigned integer | unsigned char(uint8)、unsigned short(uint16)、unsigned int(uint32) |
| number | float(32位)、double(64位) |
| string | std::string |

**Complex data type**

| Data type | Corresponding data types in c++ |
| ------------- | ------------- |
| struct | struct（In the Tars framework, you need to use tars2node to generate classes in Javascript according to tars protocol files.）|
| Binary buffer | vector&lt;char&gt;（Use the [stream].BinBuffer type in NodeJs to simulate）|
| Array | vector&lt;DataType&gt;（Use the [stream].list (vproto) type in NodeJs to simulate）|
| Dictionary | map&lt;KeyType, DataType&gt;（Use the [stream].Map (kproto, vproto) type in NodeJs to simulate）|

**Special data types in NodeJs**

**[1]：** Complex data types and basic data types, or a combination of complex data types and complex data types,can construct other advanced data types.

**[2]：** Although Float and Double data types are supported in NodeJS, they are not recommended because there is a loss of precision in the values after serialization and deserialization, which in some cases can cause harm to the business logic.

**[3]：** The 64-bit shaping we implement here is actually pseudo-64-bit, and its prototype in NodeJs is still Number.

We know that Number types in Js are represented by the IEEE754 double-precision floating-point standard. IEEE754 specifies that the first significant digit defaults to 1, followed by 52 digits to represent the value.

That is to say, the precision of the significant number provided by IEEE754 is 53 binary digits, which means that the Number value of NodeJs or the Int64 data type we implemented can only accurately represent integers whose absolute value is less than 2 to the power of 53.

**[4]：** In Javascript, the String type is Unicode encoding, and we convert it to UTF8 encoding format when we encode and decode tars.

The string received by the server is UTF8-encoded. If you need to process the string in a GBK-encoded way, the server needs to transcode (UTF8- > GBK) first.

If the server uses GBK, to send a string before it is sent, it needs to be converted to UTF8 encoding.

# 03 - Usage of basic types
```javascript
//stream module is required
var Tars = require("@tars/stream");

//use Tars.TarsOutputStream to serialize data
var os = new Tars.TarsOutputStream();

os.writeBoolean(0, false);
os.writeInt8(1, 10);
os.writeInt16(2, 32767);
os.writeInt32(3, 0x7FFFFFFE);
os.writeInt64(4, 8589934591);
os.writeUInt8(5, 200);
os.writeUInt16(6, 65535);
os.writeUInt32(7, 0xFFFFFFEE);
os.writeString(8, "我的测试程序");

//use Tars.TarsInputStream to deserialize data
var is = new Tars.TarsInputStream(os.getBinBuffer());

var tp0 = is.readBoolean(0, true, false);
console.log("BOOLEAN:", tp0);

var tp1 = is.readInt8(1, true, 0);
console.log("INT8:", tp1);

var tp2 = is.readInt16(2, true, 0);
console.log("INT16:", tp2);

var tp3 = is.readInt32(3, true, 0);
console.log("INT32:", tp3);

var tp4 = is.readInt64(4, true, 0);
console.log("INT64:", tp4);

var tp5 = is.readUInt8(5, true, 0);
console.log("UINT8:", tp5);

var tp6 = is.readUInt16(6, true, 0);
console.log("UINT16:", tp6);

var tp7 = is.readUInt32(7, true, 0);
console.log("UINT32:", tp7);

var tp8 = is.readString(8, true, "");
console.log("STRING:", tp8);
```

# 04 - Complex type -  prototype used to represent complex types
First of all, let's understand what is **type prototype**!

In c++, we can declare the container vector of a string as follows:
```cpp
#include <string>
#include <vector>

std::vector<std::string> vec;
vec.push_back("qzone");
vec.push_back("wechat");
```
Where std::vector\<std::string>, std::vector represents the container type, and std::string represents the **type prototype** contained by the container.

So how do we represent this type in NodeJs? And can it be seamlessly integrated with the coding and decoding library of tars?

To solve this problem, we use the following methods to simulate std::vector in c++:
```javascript
var Tars = require("@tars/stream");

var abc = new Tars.List(Tars.String);
abc.push("qzone");
abc.push("wechat");
```

Tars.List(Tars.String), Tars.List represents the array type, and Tars.String is used to represent the **type prototype** contained by the container.

**Now, we understand that type prototypes are used to combine with complex data types to represent more complex data types.**

In the current version, we support the following type prototype definitions:：

| Data type | Description |
| ------------- | ------------- |
| bool       | [stream].Boolean |
| integer         | [stream].Int8, [stream].Int16, [stream].32, [stream].64, [stream].UInt8, [stream].UInt16, [stream].UInt32 |
| number         | [stream].Float, [stream].Double |
| string       | [stream].String |
| enum       | [stream].Enum |
| array         | [stream].List |
| dictionary         | [stream].Map |
| binary buffer | [stream].BinBuffer |

n order for you to understand the concept more clearly, we describe in advance the representation of some complex types in NodeJs.

For more information on how to use the data type, please refer to the following detailed instructions.
```javascript
var Tars = require("@tars/stream");

//c++ code：std::vector<int>
var abc = new Tars.List(Tars.Int32)
abc.push(10000);
abc.push(10001);

//c++ code：std::vector<std::vector<std::string> >
var abc = new Tars.List(Tars.List(Tars.String));
var ta  = new Tars.List(Tars.String);
    ta.push("ta1");
    ta.push("ta2");
var tb  = new Tars.List(Tars.String);
    tb.push("tb1");
    tb.push("tb2");
abc.push(ta);
abc.push(tb);

//c++ code：std::map<std::string, std::string>
var abc = new Tars.Map(Tars.String, Tars.String);
abc.insert("key1", "value1");
abc.insert("key2", "value2");

//c++ code：std::map<std::string, std::vector<string> >
var abc = new Tars.Map(Tars.String, Tars.List(Tars.String));
var ta  = new Tars.List(Tars.String);
    ta.push("ta1");
    ta.push("ta2");
var tb  = new Tars.List(Tars.String);
    tb.push("tb1");
    tb.push("tb2");
abc.insert("key_a", ta);
abc.insert("key_b", tb);

//c++ code：std::vector<char>
var abc = new Tars.BinBuffer();
abc.writeInt32(10000);
abc.writeInt32(10001);
```


# 05-Complex Types-Explanation of how to use struct
```c++
module Ext
{
    struct ExtInfo  {
        0 optional string sUserName;
        1 optional map<string, vector<byte> > data;
        2 optional map<string, map<string, vector<byte> > > cons;
    };
};
```
Save the above as a file "Demo.tars", and then use the command "tars2node Demo.tars" to generate the codec file "Demo.js".

The content of "Demo.js" is as follows:
```javascript
var TarsStream = require("@tars/stream");

var Ext = Ext || {};
module.exports.Ext = Ext;

Ext.ExtInfo = function() {
    this.sUserName = "";
    this.data = new TarsStream.Map(TarsStream.String, TarsStream.BinBuffer);
    this.cons = new TarsStream.Map(TarsStream.String, TarsStream.Map(TarsStream.String, TarsStream.BinBuffer));
};
Ext.ExtInfo._write = function (os, tag, value) { os.writeStruct(tag, value); }
Ext.ExtInfo._read  = function (is, tag, def) { return is.readStruct(tag, true, def); }
Ext.ExtInfo._readFrom = function (is) {
    var tmp = new Ext.ExtInfo();
    tmp.sUserName = is.readString(0, false, "");
    tmp.data = is.readMap(1, false, TarsStream.Map(TarsStream.String, TarsStream.BinBuffer));
    tmp.cons = is.readMap(2, false, TarsStream.Map(TarsStream.String, TarsStream.Map(TarsStream.String, TarsStream.BinBuffer)));
    return tmp;
};
Ext.ExtInfo.prototype._writeTo = function (os) {
    os.writeString(0, this.sUserName);
    os.writeMap(1, this.data);
    os.writeMap(2, this.cons);
};
Ext.ExtInfo.prototype._equal = function (anItem) {
    return anItem.sUserName === this.sUserName
    && anItem.data === this.data
    && anItem.cons === this.cons;
}
Ext.ExtInfo.prototype._genKey = function () {
    if (!this._proto_struct_name_) {
        this._proto_struct_name_ = 'STRUCT' + Math.random();
    }
    return this._proto_struct_name_;
}
Ext.ExtInfo.prototype.toBinBuffer = function () {
    var os = new TarsStream.TarsOutputStream();
    this._writeTo(os);
    return os.getBinBuffer();
}
Ext.ExtInfo.create = function (is) {
    return Ext.ExtInfo._readFrom(is);
}
```

**Explanation on "module Ext"**

Ext is a namespace in C++. In Javascript, we translate it into an Object. All "constants", "enumeration values", "structures", and "functions" in this namespace are attached to the Object. under.

**Representation of structures described in tars files**

First, the structure is translated into an Object. The translator generates data members based on the data type and default values ​​defined in the tars file. In addition to the data members defined in tars, the translator adds several auxiliary functions to the structure according to the needs of the codec. These functions, such as _writeTo, are called by the codec library where the structure needs to be serialized into a data stream. This function writes data members one by one into the data stream.

**Auxiliary functions added by the translation program by default**

| Method | Restrictions | Description |
| ------------- | ------------- | ------------- |
\_write | Developer unavailable | Static function. Used when a structure is used as a type prototype. |
\_read | Unavailable to developers | Static functions. Used when a structure is used as a type prototype. |
\_readFrom | Unavailable to developers | Static function. Reads the data member values ​​of the structure from the data stream and generates a permissioned structure example to return. |
\_writeTo | Developer unavailable | Member function. Writes the data members of the current structure to the specified data stream. |
\_equal | Unavailable for developers | Member functions. Comparison function when the current structure is used as the dictionary type Key value. |
\_genKey | Unavailable for developers | Member functions. When the current structure is used as the dictionary type Key value, this function is used internally to obtain the alias of the current structure. |
| toBinBuffer | Developer Available | Member Functions. Serializes the current structure into a binary buffer, and the return value type is require ("@tars/stream"). BinBuffer. |
create | Developer Available | Member Functions. Returns a completely new structure from the data stream. |

**Example of using structure**

We demonstrate the use of structures in three typical scenarios:

**First scenario:** When a structure is used as an argument to an RPC function.

Since the rpc framework will serialize the parameters automatically, we don't need to care about encoding and decoding, just need to follow the common class, first assign new value, and then pass the parameters to call the RPC function directly.

If the server has an RPC as defined below:

```c ++
module TRom
{
    struct Param {
        0 optional string sUserName;
        1 optional int iId;
    };

interface process {
int getUserLevel (Param userInfo, out int iLevel);
};
};}
```

After installing the above method to generate a tars codec file (the generated file name is Protocol.js), call the peer service as follows:

```javascript
var Tars = require ("@ tars / rpc"). client;
var TRom = require ("./ Protocol.js"). TRom;

var prx = Tars.stringToProxy (TRom.NodeJsCommProxy, "TRom.NodeJsTestServer.NodeJsCommObj@tcp -h 10.12.22.13 -p 8080 -t 60000");

var usr = new TRom.Param ();
usr.sUserName = "KevinTian";
usr.iId = 10000;

prx.getUserLevel (usr) .then (function (result) {
console.log ("success:", result);
}, function (result) {
console.log ("error:", result);
}). done ();
```

**Second scenario:** The peer non-standard rpc framework accepts serialized data streams as parameters.

In this scenario, we need to serialize the structure ourselves. Take the above tars file as an example, the general method is as follows:

```js
// The client installs the following methods to package, and then sends the packaged binary data stream to the server
var Tars = require ("@tars/stream");
var TRom = require ("./ Protocol.js"). TRom;

var usr = new TRom.Param ();
usr.sUserName = "KevinTian";
usr.iId = 10000;

var os = new Tars.TarsOutputStream ();
os.writeStruct (1, usr);

// Package and get the binary data stream sent
var toSendBuffer = os.getBinBuffer (). toNodeBuffer ();
```

The client sends toSendBuffer to the server, and after the server accepts it, it decodes it as follows:

```javascript
var Tars = require ("@ tars/stream");
var TRom = require ("./Protocol.js"). TRom;

var is = new Tars.TarsInputStream (new Tars.BinBuffer (toSendBuffer));
var usr = is.readStruct (1, true, TRom.Param);

console.log ("TRom.Param.sUserName:", usr.sUserName);
console.log ("TRom.Param.iId:", usr.iId);
```

**Third scenario:** The peer service requires the data stream to use the Tup protocol, and the names of the variables have been agreed. We can encode and decode as follows:

```javascript
// The client puts the structure into Tup according to the agreed name
var Tars = require ("@tars/stream");
var TRom = require ("./ Protocol.js"). TRom;

var usr = new TRom.Param ();
usr.sUserName = "KevinTian";
usr.iId = 10000;

var tup_encode = new Tars.Tup ();
tup_encode.writeStruct ("userInfo", usr);

// Package and get the binary data stream sent
var toSendBuffer = tup_encode.encode (true) .toNodeBuffer ();
```

The client sends toSendBuffer to the server, and after the server accepts it, it decodes it as follows:
```javascript
var Tars  = require("@tars/stream");
var TRom = require("./Protocol.js").TRom;

var tup_decode = new Tars.Tup();
tup_decode.decode(new Tars.BinBuffer(toSendBuffer));

var usr  = tup_decode.readStruct("userInfo", TRom.Param);

console.log("TRom.Param.sUserName:", usr.sUserName);
console.log("TRom.Param.iId:", usr.iId);
```

# 06-Complex Types-How to Use Vectors (Arrays)
Since Javascript's native Array does not support some special operations in tars, we encapsulate it once. Developers can understand the following code:

```javascript
[stream].List = function (proto)
{
    this.proto = proto;
    this.value = new Array ();
    this.push = function (value) {this.value.push (value);}
    ...
}
```

#### [stream].List Object Properties
| Properties | Description |
| ------------- | ------------- |
value | Array data type in Js. Tars.List is actually an upper-level encapsulation based on the Array. |
| length | Returns the number of elements in the array. |

#### [stream].List Object Method
| Method | Description |
| ------------- | ------------- |
| at | Returns the element at the specified position in the array. |
| push | Adds an element to the end of the array. |
| forEach | The traversal method of the current array, please refer to the following examples for specific usage. |
| toObject | Converts a List instance into a basic data object. For details, please refer to the following examples. |
readFromObject | Processes the incoming array and pushes it to the List instance. For details, please refer to the following examples. |

proto is the type prototype of Vector (type prototype determines the method used when encoding and decoding Vector, so the correct type prototype must be passed in when declaring Vector).

#### [stream].List declaration example

```javascript
var Tars = require ("@tars/stream");

// Example 1: Declare vector <int32>
var va = new Tars.List (Tars.Int32);

// Example 2: Declare vector <string>
var vb = new Tars.List (Tars.String);

// Example 3: Declare vector <map <uint32, string>>
var vc = new Tars.List (Tars.Map (Tars.UInt32, Tars.String));

// Example 4: Declare vector <struct>, assuming the structure name is TRom.Param
var vd = new Tars.Vector (TRom.Param);
```

#### [stream].List operation example

```javascript
var Tars = require ("@tars/stream");

var ve = new Tars.List (Tars.String);

// Add elements to the array
ve.push ("TENCENT-MIG");
ve.push ("TENCENT-SNG");
ve.push ("TENCENT-IEG");
ve.push ("TENCENT-TEG");

// Get the length of the array
console.log ("Length:", ve.length);

// Get the element at the specified position
console.log ("Array [1]:", ve.at (1));

// Iterate method 1:
ve.forEach (function (value, index, oArray) {
console.log ("Array [" + index + "]:", value);
});

// Traverse method 2:
for (var index = 0, len = ve.length; index <len; index ++) {
console.log ("Array [" + index + "]:", ve.at (index));
}

// Detailed examples of the toObject method and readFromObject method can refer to the test-list-c3.js file under the sample / list path
var user1 = new TRom.User_t ();
user1.id = 1;
user1.name = 'x1';
user1.score = 1;

var user2 = new TRom.User_t ();
user2.id = 2;
user2.name = 'x2';
user2.score = 2;

var user3 = new TRom.User_t ();
user3.id = 3;
user3.name = 'x3';
user3.score = 3;

var userList1 = new Tars.List (TRom.User_t);

console.log ('user1:', user1);
console.log ('user2:', user2);

userList1.push (user1);
userList1.push (user2);

// toObject method
console.log ('userList1:', userList1.toObject ());

var userList2 = new Tars.List (TRom.User_t);
// readFromObject method
userList2.readFromObject ([user1, user2, user3]);
console.log ('userList2:', userList2.toObject ());
```

# 07-Complex Type-Map (Dictionary) Instructions
Because Javascript's native Object does not support some special operations in tars, we encapsulate it once. Developers can understand the following code:

```javascript
[stream].Map = function (kproto, vproto) {
    var Map = function () {
        this._kproto = kproto;
        this._vproto = vproto;
        this.value = new Object ();
        this.put = function (key, value) {this.insert (key, value);}
        ...
}

return new Map ();
}
```

#### [stream].Map object properties
| Properties | Description |
| ------------- | ------------- |
| value | Object data type in Js. [stream].Map is actually an upper-level encapsulation based on this Object. |

#### [stream].Map method properties
| Method | Description |
| ------------- | ------------- |
insert | Adds an element to the dictionary. |
| set | Same as insert. |
| put | Same as insert. |
remove | Removes the corresponding value from the dictionary according to the specified key. |
| clear | Clear the current dictionary. |
| has | Determine whether the dictionary contains the corresponding value according to the specified key. |
| size | Returns the number of elements in the current dictionary. |
| forEach | The traversal method of the current array, please refer to the following examples for specific usage |
| toObject | Converts a Map instance into a basic data object. Please refer to the following examples for specific usage. |
readFromObject | Inserts the passed object into the Map instance after processing, please refer to the following examples for specific usage |

#### [stream].Map declaration example

```javascript
var Tars = require("@tars/stream");

// Example 1: Declare map <int32, int32>
var ma = new Tars.Map (Tars.Int32, Tars.Int32);

// Example 2: Declare map <uint32, string>
var mb = new Tars.Map (Tars.Int32, Tars.String);

// Example 3: Method for declaring map <string, string>
var mc = new Tars.Map (Tars.String, Tars.String);

// Example 4: Declare map <string, vector <int32>>
var md = new Tars.Map (Tars.String, Tars.List (Tars.Int32));

// Example 5: Declare map <string, map <int32, vector <string>>>
var me = new Tars.Map (Tars.String, Tars.Map (Tars.Int32, Tars.List (Tars.String)));

// Example 6: Declare the method of map <string, struct>, assuming the structure name is TRom.Param
var mf = new Tars.map (Tars.String, TRom.Param);
```

#### [stream].Map operation example

```javascript
var Tars = require ("@tars/stream");

var mc = new Tars.Map (Tars.String, Tars.String);

// Add elements to the dictionary
mc.insert ("KEY-00", "TENCENT-MIG");
mc.insert ("KEY-01", "TENCENT-IEG");
mc.insert ("KEY-02", "TENCENT-TEG");
mc.insert ("KEY-03", "TENCENT-SNG");

// Get dictionary element size
console.log ("SIZE:", mc.size ());

// Check if there is a specified value in the dictionary
console.log ("Has:", mc.has ("KEY-04"));

// Dictionary traversal
mc.forEach (function (key, value) {
console.log ("KEY:", key);
console.log ("VALUE:", value);
});

// Detailed examples of the toObject method and readFromObject method can refer to the test-map-c5.js file under the sample / map path
var user1 = new TRom.User_t ();
user1.id = 1;
user1.name = 'x1';
user1.score = 1;

var user2 = new TRom.User_t ();
user2.id = 2;
user2.name = 'x2';
user2.score = 2;

var user3 = new TRom.User_t ();
user3.id = 3;
user3.name = 'x3';
user3.score = 3;

var userMap1 = new Tars.Map (Tars.String, TRom.User_t);

userMap1.insert ('user1', user1);
userMap1.insert ('user2', user2);

// toObject method
console.log ('userMap1:', userMap1.toObject ());

var userMap2 = new Tars.Map (Tars.String, TRom.User_t);
// readFromObject method
userMap2.readFromObject ({
    'user1': user1,
    'user2': user2,
    'user3': user3
});
console.log ('userMap2:', userMap2.toObject ());

```
#### Support MultiMap type
Support MultiMap type, this type allows a structure as the key of the Map. JavaScript native objects have no way to represent this data type, so this type does not implement the toObject and readFromObject methods supported by ordinary Maps.

The operation example is as follows:

```javascript
// Construct Map type
var msg = new Tars.Map (Test.StatMicMsgHead, Test.StatMicMsgBody);
msg.put (StatMicMsgHead1, StatMicMsgBody1);
msg.put (StatMicMsgHead2, StatMicMsgBody2);

// tars encoding
var os = new Tars.TarsOutputStream ();
os.writeMap (1, msg);

// tars decoding
var data = os.getBinBuffer (). toNodeBuffer ();

var is = new Tars.TarsInputStream (new Tars.BinBuffer (data));
var ta = is.readMap (1, true, Tars.Map (Test.StatMicMsgHead, Test.StatMicMsgBody));

// Traverse the Map result set
ta.forEach (function (key, value) {
    console.log ("KEY:", key.masterName, "VALUE.totalRspTime", value.totalRspTime);
});

// Get it according to the value
var tb = ta.get (StatMicMsgHead2);
if (tb == undefined) {
    console.log ("not found by name: StatMicMsgHead2");
} else {
    console.log (tb.totalRspTime);
}
```
# 08-Complex Type-Instructions for Using Binary Buffer

In the browser we can use "DataView" and "ArrayBuffer" to store and manipulate binary data. In order to improve performance, NodeJS provides a Buffer class. In order to facilitate the encoding and decoding of Tars, we have encapsulated the Buffer class. Developers can understand the following code:

```javascript
[stream].BinBuffer = function (buffer) {
    this._buffer = (buffer! = undefined && buffer instanceof Buffer)? buffer: null;
    this._length = (buffer! = undefined && buffer instanceof Buffer)? buffer.length: 0;
    this._capacity = this._length;
    this._position = 0;
}
```

#### [stream].BinBuffer Object Properties
| Properties | Description |
| ------------- | ------------- |
length | Get the data length of this binary buffer |
capacity | Get the maximum length of this binary buffer without reallocating memory |
position | Gets or sets the access pointer of the current binary buffer |

> The difference between length and capacity:
> Suppose we write an Int32 data to BinBuffer. After writing successfully, the difference between length and capacity:

Since the BinBuffer class uses the default 512 length to request memory for the first allocation, the value of capacity at this time is 512

> length indicates the size of the real data in the current buffer, and the value of length at this time is 4

#### [stream].BinBuffer method properties

**toNodeBuffer**

> Function definition; [stream].BinBuffer.toNodeBuffer ()
> Function: Returns the data of the current binary buffer. The value is a deep copy of the data of type NodeJS.Buffer.

Input parameters: none

Return data: NodeJS.Buffer type

**print**

> Function definition: [stream].BinBuffer.print ()

> Function: Print the current buffer in 16 bytes per line and in hexadecimal

**writeNodeBuffer**

> Function definition: [stream].BinBuffer.writeNodeBuffer (srcBuffer, offset, byteLength)

> Function: Write NodeJS.Buffer class data to binary buffer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | srcBuffer | NodeJS.Buffer | Raw Buffer Data |
> | offset | UInt32 | indicates the starting position of copying srcBuffer |
> | byteLength | UInt32 | indicates the amount of data copied from srcBuffer starting from offset |

> Function description:

> [1] `length = length of the current BinBuffer + originalLength`

> [2] `position = position of the current BinBuffer (position pointer of the original Buffer) + byteLength`

**writeBinBuffer**

> Function definition: [stream].BinBuffer.writeBinBuffer (value)

> Function: Write [stream].BinBuffer data to binary buffer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | value | [stream].BinBuffer | represents a binary buffer |

> Function description:

> [1] `length = length (original buffer data length) + value.length of the current BinBuffer

> [2] `position = position of the current BinBuffer (position pointer of the original Buffer) + value.length`

**writeInt8**

> Function definition: [stream].BinBuffer.writeInt8 (value)

> Function: Write Int8 data to binary buffer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | value | Int8 | 8-bit integer data |

> Function description:

> [1] `length = length (original buffer data length) + 1` of the current BinBuffer

> [2] `position = position of the current BinBuffer (position pointer of the original Buffer) + 1`

**writeInt16**

> Function definition: [stream].BinBuffer.writeInt16 (value)

> Function: Write Int16 data to binary buffer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | value | Int16 | 16-bit integer data |

> Function description:

> [1] `length = length (original buffer data length) + 2` of the current BinBuffer

> [2] `position = position of the current BinBuffer (position pointer of the original Buffer) + 2`

> [3] Data storage uses network byte order

**writeInt32**

> Function definition: [stream].BinBuffer.writeInt32 (value)

> Function: Write Int32 data to binary buffer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | value | Int32 | 32-bit integer data |

> Function description:

> [1] `length = length (original buffer data length) + 4` of the current BinBuffer

> [2] `position = position of the current BinBuffer (position pointer of the original Buffer) + 4`

> [3] Data storage uses network byte order

**writeInt64**

> Function definition: [stream].BinBuffer.writeInt64 (value)

> Function: Write Int64 data to binary buffer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | value | Int64 | 64-bit integer data |

> Function description:

> [1] `length = length (original buffer data length) of the current BinBuffer + 8`

> [2] `position = position of the current BinBuffer (position pointer of the original Buffer) + 8`

> [3] Data storage uses network byte order

**writeUInt8**

> Function definition: [stream].BinBuffer.writeUInt8 (value)

> Function: Write UInt8 data to binary buffer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | value | UInt8 | 8-bit integer data |

> Function description:

> [1] `length = length (original buffer data length) + 1` of the current BinBuffer

> [2] `position = position of the current BinBuffer (position pointer of the original Buffer) + 1`

**writeUInt16**

> Function definition: [stream].BinBuffer.writeUInt16 (value)

> Function: Write UInt16 data to binary buffer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | value | UInt16 | 16-bit integer data |

> Function description:

> [1] `length = length (original buffer data length) + 2` of the current BinBuffer

> [2] `position = position of the current BinBuffer (position pointer of the original Buffer) + 2`

> [3] Data storage uses network byte order

**writeUInt32**

> Function definition: [stream].BinBuffer.writeUInt32 (value)

> Function: Write UInt32 data to binary buffer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | value | UInt32 | 32-bit integer data |

> Function description:

> [1] `length = length (original buffer data length) + 4` of the current BinBuffer

> [2] `position = position of the current BinBuffer (position pointer of the original Buffer) + 4`

> [3] Data storage uses network byte order

**writeFloat**

> Function definition: [stream].BinBuffer.writeFloat (value)

> Function: Write Float (32-bit, single-precision floating point) data to binary buffer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | value | Float | 32-bit single-precision floating-point number |

> Function description:

> [1] `length = length (original buffer data length) + 4` of the current BinBuffer

> [2] `position = position of the current BinBuffer (position pointer of the original Buffer) + 4`

> [3] Data storage uses network byte order

**writeDouble**

> Function definition: [stream].BinBuffer.writeDouble (value)

> Function: Write Double (64-bit, double-precision floating point) data to binary buffer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | value | Double | 64-bit double-precision floating-point number |

> Function description:

> [1] `length = length (original buffer data length) of the current BinBuffer + 8`

> [2] `position = position of the current BinBuffer (position pointer of the original Buffer) + 8`

> [3] Data storage uses network byte order

**writeString**

> Function definition: [stream].BinBuffer.writeString (value)

> Function: Write String (UTF8 encoding) data to binary buffer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | value | String | UTF8 encoded string |

> Function description:

> [1] `length = length (original buffer data length) of the current BinBuffer + byte length of the string`

> [2] `position = position of the current BinBuffer (position pointer of the original Buffer) + byte length of the string`

**readInt8**

> Function definition: [stream].BinBuffer.readInt8 ()

> Function: Read a variable of type Int8 from the binary buffer according to the current data pointer

Input parameters: none

> Function description:

> [1] `position = position of the current BinBuffer (position pointer of the original Buffer) + 1`

**readInt16**

> Function definition: [stream].BinBuffer.readInt16 ()

> Function: Read a variable of type Int16 from the binary buffer according to the current data pointer

Input parameters: none

> Function description:

> [1] `position = position of the current BinBuffer (position pointer of the original Buffer) + 2`

**readInt32**

> Function definition: [stream].BinBuffer.readInt32 ()

> Function: Read a variable of type Int32 from the binary buffer according to the current data pointer

Input parameters: none

> Function description:

> [1] `position = position of the current BinBuffer (position pointer of the original Buffer) + 4`

**readInt64**

> Function definition: [stream].BinBuffer.readInt64 ()

> Function: Read a variable of type Int64 from the binary buffer according to the current data pointer

Input parameters: none

> Function description:

> [1] `position = position of the current BinBuffer (position pointer of the original Buffer) + 8`

**readUInt8**

> Function definition: [stream].BinBuffer.readUInt8 ()

> Function: Read a UInt8 variable from the binary buffer according to the current data pointer

Input parameters: none

> Function description:

> [1] `position = position of the current BinBuffer (position pointer of the original Buffer) + 1`

**readUInt16**

> Function definition: [stream].BinBuffer.readUInt16 ()

> Function: Read a UInt16 variable from the binary buffer according to the current data pointer

Input parameters: none

> Function description:

> [1] `position = position of the current BinBuffer (position pointer of the original Buffer) + 2`

**readUInt32**

> Function definition: [stream].BinBuffer.readUInt32 ()

> Function: Read a UInt32 variable from the binary buffer according to the current data pointer

Input parameters: none

> Function description:

> [1] `position = position of the current BinBuffer (position pointer of the original Buffer) + 4`

**readFloat**

> Function definition: [stream].BinBuffer.readFloat ()

> Function: Read a variable of type Float (32-bit single-precision floating-point number) from the binary buffer according to the current data pointer

Input parameters: none

> Function description:

> [1] `position = position of the current BinBuffer (position pointer of the original Buffer) + 4`

**readDouble**

> Function definition: [stream].BinBuffer.readDouble ()

> Function: Read a variable of type Double (64-bit double-precision floating-point number) from the binary buffer according to the current data pointer

Input parameters: none

> Function description:

> [1] `position = position of the current BinBuffer (position pointer of the original Buffer) + 8`

**readString**

> Function definition: [stream].BinBuffer.readString (byteLength)

> Function: Read a String (UTF8 encoding) variable from the binary buffer according to the current data pointer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | byteLength | UInt32 | Byte Length of String |

> Function description:

> [1] `position = position (original buffer's position pointer) of the current BinBuffer + byte length of the string`

> [2] Background encoding of strings requires UTF8 character set

**readBinBuffer**

> Function definition: [stream].BinBuffer.readBinBuffer (byteLength)

> Function: Read a variable of type [stream].BinBuffer from the binary buffer according to the current data pointer

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | byteLength | UInt32 | Byte Length of Binary Buffer |

> Function description:

> [1] `position = position of the current BinBuffer (position pointer of the original buffer) + byte length of the binary buffer`

# 09-Coding Tools-How to Use OutputStream

**Constructor**
> Function definition: [stream].OutputStram ()

> Function: declare an output stream object

Input parameters: none

Example usage: var os = new [stream].TarsOutputStream ()

**getBinBuffer**

> Function definition: var buffer = [stream].TarsOutputStream.getBinBuffer ()

> Function role: Call this function to get the binary data stream after packing

Input parameters: none

> Return data: return the packed binary data stream, the return value type is [stream].BinBuffer

**writeBoolean**

> Function definition: [stream].TarsOutputStream.writeBoolean (tag, value)

> Function: Write a Boolean variable to the data stream

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | Boolean | represents the value of the variable, ranging from {false, true} |

>Return data: void

**writeInt8**

> Function definition: [stream].TarsOutputStream.writeInt8 (tag, value)

> Function role: Write a variable of type int8 to the data stream

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | int8 (Number) | represents the value of the variable, value range [-128, 127] |

>Return data: void

**writeInt16**

> Function definition: [stream].TarsOutputStream.writeInt16 (tag, value)

> Function: Write a variable of type Int16 to the data stream

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | int16 (Number) | represents the value of the variable, value range [-32768, 32767] |

>Return data: void

**writeInt32**

> Function definition: [stream].TarsOutputStream.writeInt32 (tag, value)

> Function: Write an Int32 variable to the data stream

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | int32 (Number) | represents the value of the variable, value range [-2147483648, 2147483647] |

>Return data: void

**writeInt64**

> Function definition: [stream].TarsOutputStream.writeInt64 (tag, value)

> Function: Write an Int64 variable to the data stream

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | int64 (Number) | represents the value of this variable, the value range [-9223372036854775808, 9223372036854775807] |

>Return data: void

**writeUInt8**

> Function definition: [stream].TarsOutputStream.writeUInt8 (tag, value)

> Function: Write a UInt8 variable to the data stream

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | UInt8 (Number) | represents the value of the variable, in the range [0, 255] |

>Return data: void

**writeUInt16**

> Function definition: [stream].TarsOutputStream.writeUInt16 (tag, value)

> Function: Write a UInt16 variable to the data stream

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | UInt16 (Number) | represents the value of the variable, value range [0, 65535] |

>Return data: void

**writeUInt32**

> Function definition: [stream].TarsOutputStream.writeUInt32 (tag, value)

> Function: Write a UInt32 variable to the data stream

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | UInt32 (Number) | represents the value of the variable, value range [0, 4294967295] |

>Return data: void

**writeFloat**

> Function definition: [stream].TarsOutputStream.writeFloat (tag, value)

> Function: Write a float (32-bit) variable to the data stream

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | Float (Number) | Single-precision floating-point numbers, because of the loss of precision, this type is not recommended |

>Return data: void

**writeDouble**

> Function definition: [stream].TarsOutputStream.writeDouble (tag, value)

> Function: Write a double (64-bit) variable to the data stream

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | Double (Number) | Double-precision floating-point numbers, because of the loss of precision, this type is not recommended |

>Return data: void

**writeString**

> Function definition: [stream].TarsOutputStream.writeString (tag, value)

> Function: Write a String variable to the data stream

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | String | represents the value of the variable, the string encoding character set is UTF8 |

>Return data: void

**writeStruct**

> Function definition: writeStruct (tag, value)

> Function role: Write a custom structure variable to the data stream

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | Custom structure | The structure must be converted using tars2node, otherwise the codec may fail due to the lack of auxiliary functions |

>Return data: void

**writeBytes**

> Function definition: [stream].TarsOutputStream.writeBytes (tag, value)

> Function: Write a variable of type `char *` or `vector <char>` to the data stream

> Input parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | [stream].BinBuffer | BinBuffer is a wrapper for the Buffer class in NodeJs, and also integrates auxiliary functions needed for encoding and decoding |

>Return data: void

**writeList**

> Function definition: [stream].TarsOutputStream.writeList (tag, value)

> Function: Write a variable of type `vector <T>` (T cannot be byte) to the data stream

> Function parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | [stream].List (T) | type prototype of this variable |

>Return data: void

**writeMap**

> Function definition: [stream].TarsOutputStream.writeMap (tag, value)

> Function: Write a field of type `map <T, V>` to the data stream.

> Function parameters:

> | Parameter | Data Type | Description |
> | ------------- | ------------- | ------------- |
> | tag | UInt8 | Represents the numeric identifier of the variable, value range [0, 255] |
> | value | [stream].Map (T, V) | type prototype of this variable |

>Return data: void

# 10 - decoding tool - instructions for using InputStream
**Constructor**
>Function definition: [stream]. Tarsinputstream (binbuffer)

>Function to declare an input stream object

>Input parameters:  
>binbuffer the binary data stream to be decoded. The value type must be [stream]. Binbuffer, not the buffer class implemented in nodejs.

>Use example:   
>var is = new [stream]. Tarsinputstream (new [stream]. Binbuffer (node. Buffer))  

**readBoolean**
>Function definition: VaR value = [stream]. Tarsinputstream.readboolean (tag, require, default)
>Function to read a boolean type value from a data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Default | Boolean | indicates the return value when reading variables unsuccessfully. The value range is {false, true}|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return the default value of the variable;
>Return data: Boolean, value range {false, true}

**readInt8**

>Function definition: [stream].Tarsinputstream.readint8 (tag, require, default)
>Function: read a value of type int8 from the data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Default | int8 | indicates the return value in case of unsuccessful reading of variable, value range [- 128, 127]|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return the default value of the variable;
>Return data: int8, value range [- 128, 127]

**readInt16**
>Function definition: [stream]. Tarsinputstream.readint16 (tag, require, default)
>Function: read a value of type int16 from the data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Default | int16 | indicates the return value in case of unsuccessful reading of variable. The value range is [- 32768, 32767]|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return the default value of the variable;
>Return data: int16, value range [- 32768, 32767]  

**readInt32**
>Function definition: [stream]. Tarsinputstream.readint32 (tag, require, default)
>Function to read a value of type int32 from the data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Default | int32 | indicates the return value in case of unsuccessful reading of variable. The value range is [- 2147483648, 2147483647]|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return the default value of the variable;
>Return data: int32, value range [- 2147483648, 2147483647]  

**readInt64**
>Function definition: [stream]. Tarsinputstream.readint64 (tag, require, default)
>Function: read a value of type Int64 from the data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Default | Int64 | indicates the return value in case of unsuccessful reading of variable. The value range is [- 9223372036854775808, 9223372036854775807]|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return the default value of the variable;
>Return data: Int64 (number), value range [- 9223372036854775808, 9223372036854775807]  

**readUInt8**
>Function definition: [stream]. Tarsinputstream.readuint8 (tag, require, default)
>Function: read a uint8 type value from the data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Default | uint8 | indicates the return value in case of unsuccessful reading of variable, value range [0, 255]|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return the default value of the variable;
>Return data: uint8 (number), value range [0, 255]  

**readUInt16**
>Function definition: [stream].TarsInputStream.readUInt16 (tag, require, default)
>Function: read a uint16 type value from the data stream
>Input parameters:

>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Default | uint8 | indicates the return value in case of unsuccessful reading of variable, value range [0, 65535]|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return the default value of the variable;
>Return data: uint16 (number), value range [0, 65535]  

**readUInt32**
>Function definition: [stream]. Tarsinputstream.readuint32 (tag, require, default)
>Function: read a uint32 type value from the data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Default | uint8 | indicates the return value in case of unsuccessful reading of variable, value range [0, 4294967295]|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return the default value of the variable;
>Return data: uint32 (number), value range [0, 4294967295]  

**readFloat**
>Function definition: [stream]. Tarsinputstream.readfloat (tag, require, default)
>Function: read a float (32-bit, single precision floating-point) type value from the data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Default | float | indicates the return value when reading variables is unsuccessful|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return the default value of the variable;
>Return data: float (number)  

**readDouble**
>Function definition: [stream]. Tarsinputstream.readfloat (tag, require, default)
>Function to read a double (64 bit, double precision floating-point) type value from the data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Default | double | indicates the return value when reading variables is unsuccessful|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return the default value of the variable;
>Return data: double (number)

**readString**

>Function definition: [stream]. Tarsinputstream.readstring (tag, require, default)
>Function: read a string (utf8 encoding) type value from the data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Default | string | indicates the return value when reading variables is unsuccessful|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return the default value of the variable;
>Return data: string (utf8 encoding)  

**readStruct**
>Function definition: [stream]. Tarsinputstream.readstruct (tag, require, type
>Function to read a value of a custom structure type from a data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Type | type prototype of custom structure | type prototype of the variable|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data flow, the system will return an empty instance of the structure;
>Return data: instances of custom structures  

**readBytes**
>Function definition: [stream]. Tarsinputstream.readbytes (tag, require, type_
>Function: read a value of type '[stream]. Binbuffer' from the data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Type| [stream]. Binbuffer|indicates the type prototype of the variable|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return an empty instance of [stream]. Binbuffer;
>Return data: [stream].Binbuffer  

**readList**
>Function definition: [stream]. Tarsinputstream. Readlist (tag, require, type_
>Function function: read a value of type '[stream]. List < T >' from the data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Type | [stream]. List < T > |, indicating the type prototype of the variable|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return an empty instance of [stream]. List (T);
>Return data: [stream].List  

**readMap**
>Function definition: [stream]. Tarsinputstream.readmap (tag, require, type_
>Function function: read a value of type '[stream]. Map < T, V >' from the data stream
>Input parameters:
>|Parameter | data type | description|
>| ------------- | ------------- | ------------- |
>|Tag | uint8 | indicates the digital ID of the variable to be read, value range [0, 255]|
>|Require | Boolean | indicates whether the current variable is a required value. The value range is {false, true}|
>|Type | [stream]. Map (T, V) |, indicating the type prototype of the variable|
>>Description of require:
>>When 'require = = = true' &amp; nbsp; &amp; nbsp;, if the current variable is not in the data stream, the system will throw an exception that does not exist to read the data;
>>When 'require = = = false', if the current variable is not in the data stream, the system will return an empty instance of [stream]. Map (T, V);
>Return data: [stream]. Map (T, V)