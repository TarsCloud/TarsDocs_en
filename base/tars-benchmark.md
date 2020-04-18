## Introduction

[tb(tars benchmark)](https://github.com/TarsCloud/TarsBenchmark) It is a non-code benchmark tool specially tailored for tars service, with the following characteristics:

 - High network performance: 8-core machine TPS supports more than 20W/s;
 - Communication compatibility: The network layer supports TCP and UDP protocol;
 - Protocol scalability: the application layer supports http/tars service benchmark,  and open to third-protocol agreements
 - Perfect real-time monitoring support. Provide the number of requests / TPS / time-consuming /Success rate distribution within the cycle;

## Framework

The tb tool is designed in a multi-process model. The main process is responsible for resource scheduling and display, and the benchmark process is responsible for network transmission and reception and statistics. The network layer can flexibly choose TCP or UDP; adopts a protocol proxy factory mode to manage various service protocols, and supports http / by default The benchmark of the tars protocol supports automatic protocol discovery; the main process and the benchmark process exchange signals through control information, and the data interacts through the lock-free shared memory queue to achieve the lowest resource consumption. The main process periodically collects each The network statistics of the benchmark process are output to the console after a simple summary.

![tb system](../assets/tb-platform.png)


## Usage

Sample
```text
./tb -c 600 -s 6000 -D 192.168.31.1 -P 10505 -p tars -S tars.DemoServer.DemoObj -M test -C test.txt
```
参数说明
```text
  -h                   帮助信息
  -c                   number of connections
  -D                   target server address(ipv4)
  -P                   target server port
  -p                   service protocol(tars|http)
  -t(optional)         overtime time，default 3 second
  -T(optional)         network protocol，default tcp
  -I(optional)         continue time(by second)
  -s(optional)         maximum tps
  -i(optional)         view interval
  -n(optional)         maximum process
```
See details in [tb README.md](../dev/tarsbenchmark/README.md)

