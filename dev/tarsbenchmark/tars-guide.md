## Tars Case Instructions

This is a example of how to use the tool to benchmark tars service.

### 1.1.  Tars interface file

The sample file as follows，Demo.tars：

```cpp
module tars
{
    struct DemoReq
    {
        0 require  string aa;
        1 optional int    bb;
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

### <a id="tb-chapter-1"></a>1.2. Case file generation

Use the tars2case tool to automatically generate the cases needed by the tb tool. The tars2case tool generates their each case of rpc functions. The values of the parameters can be modified according to the service needs.

```text
/usr/local/tars/cpp/tools/tars2case Demo.tars --dir=benchmark

cd benchmark && ls
echo.case  test.case
```

### <a id="tb-chapter-2"></a> 1.3. Modified Cases

The case file is divided into upper and lower parts, which are divided by the first line of "#". The upper part is the RPC parameter, and the lower part is the value of the RPC parameter, which corresponds to the parameter one by one.

- **Parameter description**：
 1. The input parameter of rpc methed is divided by the "|" symbol,
 2. The method of struct like: struct<tag require | optional field 1, field 2, field 3. >. If tag starts from 0, direct field 1
 3. The method of vector like: vector<type>
 4. The method of map like: map<key type, value type>
 5. 2、3、4 can be used in nesting

- **Parameter value description**：
 1. <strong>basic type</strong>support random value setting：
    <strong>range random value</strong>expressed with [1-100]，indicates random occurrence within 1-100, must be a number
    <strong>limited random value</strong>expressed with [1,123,100]，indicates random occurrence in 1,123,100, can be a string
 2. Enter each line of the input parameter,
 3. The method of struct like: <field value 1, field value 2, field value 3 ...>
 4. The method of vector like: <value 1, value 2, value 3 ...>
 5. The method of map like:[key1=val1, key2=val2, key3=val3...]
 6. 3、4、5 can be used in nesting

- **E.g**：
```text
vector<string>|struct<string, int>|map<string, string>
#######
<abc, def, tt, fbb>
<abc, 1>
[abc=def, dfd=bbb]
```

### 1.4. Startup example
```text
./tb -c 600 -s 6000 -D 192.168.31.1 -P 10505 -p tars -S tars.DemoServer.DemoObj -M test -C test.txt
```

tars protocol parameter description
```text
  -S                   Tars servant name
  -M                   Tars interface method
  -C                   Case file，See[Case file generation](#main-chapter-1)和[Modified Cases](#main-chapter-2)
```

### 1.2. Closing example
Initiative close: ctrl+C or killall tb，Wait a few seconds and then output the final statistical results.<br/>
Passive close: The default duration is 1 hour. After 1 hour, the benchmark tool will bt stopped and the statistical results are output. This time can be adjusted by the-I parameter.


### 1.3. Display the results
![results](../../assets/tb_tars_result.png)