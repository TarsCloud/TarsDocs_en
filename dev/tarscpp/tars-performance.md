# performance data

The configuration of our test machine is shown in the figure below. We have three machines with the same configuration, two of which are clients and one is server:：

![](../../assets/tars_performance_en.png)

The machine is configured as follows

| Software | Software requirements |
| :--- | :--- |
| system kernel | Linux 3.10.94 x86\_64 x86\_64 GNU/Linux |
| cpu info | 3.30Ghz cpu\*8 |
| memory| 16GB |
| Network | Gigabit Ethernet |
| Hard disk | 7200rpm Mechanical hard disk |


In the first test, we tested the general performance of the machine. We deployed two network threads and five business threads on the client machine, and sent the business data of the normal size. In the second test, we tested the ultimate performance of the tars. We opened 50 processes on two machines as the client respectively. Each process has 16 threads, and each thread sends 4 bytes Small business data package, the test results are as follows:

| Lang | Client Machines| Process num | Thread num | package size（B） | TPS（w/s） | Avg cost\(ms\) | cpu| In traffic\(Mb/s\) | Out traffic\(Mb/s\) | In packets\(/s\) | Out packets\(/s\) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| c++ | 1 | 1 | 1 | 4 | 0.7 | 0.13 | 3% | 8M　　　　　 | 　7M | 7214　　 | 　7220 |
| c++ | 1 | 1 | 1 | 1024 | 0.52 | 0.17 | 3% | 53M　　　　 | 　50M | 5677 　 | 　5666 |
| c++ | 2 | 1 | 10 | 4 | 11.39 | 0.17 | 28% | 139.729M | 92.389M | 127,267 | 127,923 |
| c++ | 2 | 1 | 10 | 1024 | 6.73 | 0.29 | 19% | 644.135M | 616.395M | 95,424 | 95,823 |
| c++ | 2 | 2 | 10 | 4 | 20.05 | 0.20 | 63% | 238.542M | 158.658M | 220,434 | 221,658 |
| c++ | 2 | 2 | 10 | 1024 | 10.03 | 0.38 | 38% | 972.232M | 930.256M | 141,841 | 142,388 |
| c++ | 2 | 5 | 10 | 4 | 27.22 | 0.37 | 84% | 327.972M | 215.173M | 306,896 | 300,099 |
| c++ | 2 | 5 | 10 | 1024 | 10.02 | 0.96 | 48% | 974.102M | 932.277M | 132,091 | 133,574 |
| c++ | 2 | 50 | 16 | 4 | 41 | 3.88 | 92% | 463.815M | 313.112M | 422,732 | 431,050 |
| java | 1 | 1 | 1 | 4 | 0.7 | 0.13 | 5% | 8.424M　　　　　 | 　6.041M | 7773　　 | 　7793 |
| java | 1 | 1 | 1 | 1024 | 0.52 | 0.17 | 8% | 61.866M | 59.951M | 6674 　 | 　6700 |
| java | 2 | 1 | 10 | 4 | 9.83 | 0.18 | 34% | 155.719M | 106.310M | 156,681 | 148,201 |
| java | 2 | 1 | 10 | 1024 | 7.82 | 0.27 | 26% | 694.184M | 669.369M | 103,564 | 104,158 |
| java | 2 | 2 | 10 | 4 | 20.42 | 0.20 | 57% | 254.149M | 183.307M | 252,928 | 259,064 |
| java | 2 | 2 | 10 | 1024 | 10.03 | 0.38 | 41% | 964.790M | 930.363M | 141,965 | 143,004 |
| java | 2 | 5 | 10 | 4 | 26.34 | 0.34 | 77% | 244.887M | 186.358M | 243,527 | 254,967 |
| java | 2 | 5 | 10 | 1024 | 10.11 | 0.97 | 48% | 967.217M | 939.408M | 132,421 | 135,919 |
| java | 2 | 50 | 16 | 4 | 38 | 4.27 | 82% | 438.999M | 329.996M | 413,046 | 426,961 |

**The test results are only for reference. The actual results will be affected by different test conditions and measurement methods**

