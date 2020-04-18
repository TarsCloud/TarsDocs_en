# TarsBenchmark FAQ

### What's the meaning of -s parameter

1.The rate unit is TPS(Transports Per Second) of a single target machine specified by -s. If there are n target machines, the total TPS = rate*n<br/>
2.If you do not specify -s parameter, tb will try its best to initiate the request to the target machine.

### How to achieve high performance of tb

1.The tool will create the same number of child processes by the source machine's cores, and the number of connections and flow will be divided equally among the child processes.<br/>
2.Through event-driven to avoid network IO blocking process, maximize CPU utilization,

### How to synchronize data synchronization between main process and child process ？

Data sharing is achieved through a lock-free shared memory queue, and the statistical results are output in the main process
