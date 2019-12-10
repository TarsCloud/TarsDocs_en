# Tars 平台针对 PHP 修改说明

[Permalink](https://github.com/TarsCloud/TarsDocs/blob/293528ce7047760f61730cca487baf6d6aff9214/kai-fa/tarsphp/tars-ping-tai-zhen-dui-php-xiu-gai-shuo-ming.md)

### Join GitHub today

GitHub is home to over 40 million developers working together to host and review code, manage projects, and build software together.

[Sign up](https://github.com/join?source=prompt-blob-show)

 Fetching contributors…

![](https://github.githubassets.com/images/spinners/octocat-spinner-32-EAF2F5.gif) Cannot retrieve contributors at this time

 32 lines \(26 sloc\) 1.08 KB

## Tars 平台针对 PHP 修改说明

与默认模板tars.default相比，php新增了tars.tarsphp.default, 主要添加了如下部分:

```text
    <tars>
        ...
        <application>
            <server>
                ...
                php=/data/env/runtime/php-7.1.7/bin/php
                buffer_output_size=12582912
                open_tcp_nodelay=1
                open_eof_check=0
                open_eof_split=0
                task_worker_num=1
                dispatch_mode=2
                daemonize=1
                ...
            </server>
        </application>
        ...
    </tars>
```

* 支持自定义php执行文件（/tars/application/server&lt; php &gt;），默认/usr/bin/php。
* 支持自定义tars\_php框架启动文件配置（/tars/application/server&lt; entrance &gt;），默认项目目录下的src/index.php。
* tars平台生成的启动脚本会以entrance为入口文件来执行启停脚本。

在tars-server模块中,三个不同的http、timer、tcp server都需要使用不同的模块,请务必进行新的模板的添加和修改工作!!

