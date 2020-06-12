## Tars Case Instructions
> * [Interface Definition](#chapter-1)
> * [JSON protocol usage](#chapter-2)
> * [Custom protocol usage](#chapter-3)
> * [Closing benchmark](#chapter-4)
> * [Display benchmark](#chapter-5)


The tb tool supports two protocols(json and custom) to benchmark the target tars service. Generally, it is recommended to use the json protocol.

### 1.1. <a id="chapter-1"></a>Tars interface file

The sample file as follows，Demo.tars：

```cpp
module tars
{
    struct DemoReq
    {
        0 require  string aa;
        1 optional int    bb;
        2 optional vector<byte>  cc;
    };

    struct DemoRsp
    {
        0 require  DemoReq  rr;
        1 optional bool     ok;
    };

    interface DemoObj
    {
        int test(int a, int b);
        int echo(DemoReq req, out DemoRsp rsp);
    };
};
```

### 1.2. <a id="chapter-2"></a>JSON protocol usage

##### 1.2.1. Case file generation

Use the tars2case tool to automatically generate the input file needed by the tb. According to the rpc function of each interface, the tool generates case file and description file, the description file can not be changed.
```text
/usr/local/tars/cpp/tools/tars2case --json Demo.tars --dir=benchmark

cd benchmark && ls
echo.case  echo.desc  test.case  test.desc
```

##### 1.2.2. Case file modification

  After the use case file is automatically generated according to the tars2json tool, the Json content can be modified as needed, in which the structural data of vector and map also can be increased up and down, and the content in vector<byte> type can be input in both string and array. Using string, it needs to be converted into bin2hex. In addition, in order to avoid the hot key effect in the benchmark process and prevent the back-end server from falling on the same single machine, tb supports the generation of random content in Number and string in the tars structure in the following two random ways (Value must be string).
  1. **range random value** expressed as [1-100], which means that it occurs randomly within 1-100 and must be a number.
  2. **limit random value** expressed as [369pr aarem BBB], which means that it appears randomly in 369pr aaarech BBB.

 **Case Content**: eg, echo interface
```json
{
   "req":"string val",
   "bb": "[100-10000]",
   "cc": "123456A5B6"
}
```

##### 1.2.3. Startup example
```text
./tb -c 600 -s 6000 -D 192.168.31.1 -P 10505 -p json -S tars.DemoServer.DemoObj -M test -C test
```
json protocol description：
```text
  -S                   Tars servant name
  -M                   Tars interface method
  -C(optional)         Prefixes for cases and description files, using method names by default
```

### 1.3. <a id="chapter-3"></a>Custom protocol usage

##### 1.3.1. Case file generation

Use the tars2case tool to automatically generate the cases needed by the tb tool. The tars2case tool generates their each case of rpc functions. The values of the parameters can be modified according to the service needs.

```text
/usr/local/tars/cpp/tools/tars2case Demo.tars --dir=benchmark

cd benchmark && ls
echo.case  test.case
```

##### 1.3.2. Modified Cases

The case file is divided into upper and lower parts, which are divided by the first line of "#". The upper part is the RPC parameter, and the lower part is the value of the RPC parameter, which corresponds to the parameter one by one.

- **Parameter description**：
 1. The input parameter of rpc methed is divided by the "|" symbol,
 2. The method of struct like: struct<tag require | optional field 1, field 2, field 3. >. If tag starts from 0, direct field 1
 3. The method of vector like: vector<type>
 4. The method of map like: map<key type, value type>
 5. 2、3、4 can be used in nesting

- **Parameter value description**：
 1. <strong>basic type</strong>support random value setting：
    <strong>range random value</strong> expressed with [1-100]，indicates random occurrence within 1-100, must be a number
    <strong>limited random value</strong> expressed with [1,123,100]，indicates random occurrence in 1,123,100, can be a string
 2. Enter each line of the input parameter,
 3. The method of struct like: <field value 1, field value 2, field value 3 ...>
 4. The method of vector like: <value 1, value 2, value 3 ...>
 5. The method of map like:[key1=val1, key2=val2, key3=val3...]
 6. 3、4、5 can be used in nesting

**E.g**：
```text
vector<string>|struct<string, int>|map<string, string>
#######
<abc, def, tt, fbb>
<abc, 1>
[abc=def, dfd=bbb]
```

##### 1.3.3. Startup example
```text
./tb -c 600 -s 6000 -D 192.168.31.1 -P 10505 -p tars -S tars.DemoServer.DemoObj -M test -C test.txt
```

custom protocol parameter description
```text
  -S                   Tars servant name
  -M                   Tars interface method
  -C                   Case file，See[Case file generation]和[Modified Cases]
```

### 1.4. <a id="chapter-4"></a>Closing example
Initiative close: ctrl+C or killall tb，Wait a few seconds and then output the final statistical results.<br/>
Passive close: The default duration is 1 hour. After 1 hour, the benchmark tool will bt stopped and the statistical results are output. This time can be adjusted by the-I parameter.


### 1.5.<a id="chapter-5"></a> Display benchmark
![results](../assets/tb_tars_result.png)