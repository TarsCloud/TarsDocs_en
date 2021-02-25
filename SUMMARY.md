# Table of contents

- [README](README.md)
- [CLA](cla.md)
- [LICENSE](license.md)

# Directory

> - [Intro](#intro)
> - [Deploy](#deploy)
> - [Dev Start](#enter)
> - [Benchmark](#benchmark)
> - [TarsCpp](#TarsCPP)
> - [TarsJava](#TarsJava)
> - [TarsGo](#TarsGo)
> - [Tarsjs](#Tars.js)
> - [TarsPhp](#TarsPHP)
> - [Other Important Features](#important)
> - [Open Source Cooperation](#cooperation)
> - [FAQ](#question)

## Intro <a id="intro"></a>

- [Introduction](base/tars-intro.md)
- [Base Concept](base/tars-concept.md)
- [Tars Protocol](base/tars-protocol.md)
- [Tup Protocol](base/tars-tup.md)

## Deploy <a id="deploy"></a>

- [Tars Deploy](installation/README.md)
- [Docker Installation](installation/docker-install.md)
- [Mysql Installation](installation/mysql.md)
- [Linux/Mac Compiler & Deploy](installation/source.md)
- [Windows Compiler & Deploy](installation/source-windows.md)
- [Deploy By Docker](installation/docker.md)
- [Deploy In K8s](installation/k8s-docker-1.md)
- [Alternative To Deploy In K8s](installation/k8s-docker-2.md)
- [TarsNode Deploy](installation/node.md)
- [Tars Upldate & Expand](installation/expand.md)
- [TarsWeb](installation/web.md)

## Start <a id="enter"></a>

- [Development Environment Setup](env/README.md)
  - [TarsCPP](env/tarscpp.md)
  - [TarsGo](env/tarsgo.md)
  - [TarsJava](env/tarsjava.md)
  - [TarsPHP](env/tarsphp.md)
  - [Tars.js](env/tars.js.md)
- [Hello World](hello-world/README.md)
  - [TarsGo Helloworld](hello-world/tarsgo.md)
  - [TarsJava Helloworld](hello-world/tarsjava.md)
  - [TarsCPP Helloworld](hello-world/tarscpp.md)
  - [TarsPHP Helloworld](hello-world/tarsphp.md)
  - [Tars.js Helloworld](hello-world/tars.js.md)

## benchmark tool <a id="benchmark"></a>

- [Benchmark Tool](benchmark/README.md)
- [Tool Build](benchmark/build.md)
- [Http protocol benchmark](benchmark/http-guide.md)
- [Tars protocol benchmark](benchmark/tars-guide.md)
- [Develop](benchmark/develop.md)

## TarsCPP <a id="TarsCPP"></a>

- [Guide for Use](dev/tarscpp/tars-guide.md)
- [Development Specification](dev/tarscpp/tars-spec.md)
- [Service Thread Description](dev/tarscpp/tars-server-thread.md)
- [Protobuf Support](dev/tarscpp/tars-protobuf.md)
- [Thirdpart Protocol support](dev/tarscpp/tars-thirdparty-protocol.md)
- [HTTP1 Support](dev/tarscpp/tars-http1.md)
- [HTTP2 Support](dev/tarscpp/tars-http2.md)
- [TLS Support](dev/tarscpp/tars-tls.md)
- [Cookie Support](dev/tarscpp/tars-cookie.md)
- [Performance](dev/tarscpp/tars-performance.md)
- [2.0 Update](dev/tarscpp/tars-2.0-update.md)
- [Example](demo/tarscpp/README.md)
  - [Tars C++ QuickStart](demo/tarscpp/tars_cpp_quickstart.md)
  - [Tars C++ Http Demo](demo/tarscpp/tars_cpp_http_demo.md)
  - [Tars C++ Push Demo](demo/tarscpp/tars_push.md)
  - [Tars C++ Corotine Demo](demo/tarscpp/tars_co.md)

## TarsJava <a id="TarsJava"></a>

- [Quick Start](dev/tarsjava/tars-quick-start.md)
- [Tutorials]
  - [Tars Service Development](dev/tarsjava/tars-tutorials.md)
  - [HTTP Service Development](dev/tarsjava/tars-http-server.md)
  - [Tars File Reference](dev/tarsjava/tars-reference.md)
- [Performance Test]
  - [Tars Java Stress ](dev/tarsjava/stress-testing.md)

## TarsGo <a id="TarsGo"></a>

- [Guide for Use](dev/tarsgo/README.md)
- [pb2tarsgo](dev/tarsgo/pb2tarsgo.md)
- [Performance](dev/tarsgo/xing-neng-ce-shi.md)
- [Quick Start](dev/tarsgo/tars_go_quickstart_en.md)
- [Example](demo/tarsgo.md)

## TarsPHP <a id="TarsPHP"></a>

- [Environment building]()
  - [Build php environment](dev/tarsphp/Environment/php.md)
  - [Use docker](dev/tarsphp/Environment/docker.md)
- [Quick Start](dev/tarsphp/QuickStart/introduce.md)
  - [Build HttpServer](dev/tarsphp/QuickStart/tars-http-server.md)
  - [Build TimerServer](dev/tarsphp/QuickStart/tars-timer-server.md)
  - [Build TcpServer](dev/tarsphp/QuickStart/tars-tcp-server.md)
  - [Build WebSocketServer](dev/tarsphp/QuickStart/tars-websocket-server.md)
  - [Barrage Activity Combat](dev/tarsphp/QuickStart/tars-act-demo.md)
- [Framework Introduction](dev/tarsphp/Framework/introduce.md)
  - [tars-server](dev/tarsphp/Framework/tars-server.md)
  - [tars-client](dev/tarsphp/Framework/tars-client.md)
  - [tars-config](dev/tarsphp/Framework/tars-config.md)
  - [tars-deploy](dev/tarsphp/Framework/tars-deploy.md)
  - [tars-extension](dev/tarsphp/Framework/tars-extension.md)
  - [tars-log](dev/tarsphp/Framework/tars-log.md)
  - [tars-monitor](dev/tarsphp/Framework/tars-monitor.md)
  - [tars-registry](dev/tarsphp/Framework/tars-registry.md)
  - [tars-report](dev/tarsphp/Framework/tars-report.md)
  - [tars-utils](dev/tarsphp/Framework/tars-utils.md)
  - [tars2php](dev/tarsphp/Framework/tars2php.md)
- [Advanced applications]()
  - [How PHP's Swoole Framework Accesses Tars](dev/tarsphp/Advanced/swoole-suport-tars.md)
  - [Use with thinkphp](dev/tarsphp/Advanced/thinkphp.md)
  - [Use with Swoft](dev/tarsphp/Advanced/swoft.md)
  - [Combined with Laravel](dev/tarsphp/Advanced/laravel.md)
  - [Combined with Yii2](dev/tarsphp/Advanced/yii2.md)
  - [Continuous Integration Solution](dev/tarsphp/Advanced/ci.md)
- [Other]()
  - [Frequently Asked Questions](dev/tarsphp/Question/index.md)
  - [How to Debug](dev/tarsphp/Question/debug.md)
  - [changelog](dev/tarsphp/Question/changelog.md)
  - [Other external documents](dev/tarsphp/Question/outsource.md)

## Tars.js <a id="Tars.js"></a>

- [Guide for Use](dev/tars.js/README.md)
* [@tars/nodetools-cli](dev/tars.js/nodetools-cli.md)
- [@tars/stream](dev/tars.js/tars-stream.md)
- [@tars/rpc](dev/tars.js/tars-rpc.md)
- [@tars/logs](dev/tars.js/tars-logs.md)
- [@tars/config](dev/tars.js/tars-config.md)
- [@tars/monitor](dev/tars.js/tars-monitor.md)
- [@tars/notify](dev/tars.js/tars-notify.md)
- [@tars/utils](dev/tars.js/tars-utils.md)
- [@tars/dyeing](dev/tars.js/tars-dyeing.md)
- [@tars/node-agent](dev/tars.js/tars-node-agent.md)
- [@tars/winston-tars](dev/tars.js/tars-winston-tars.md)
- [tars2node](dev/tars.js/tars2node.md)

## Other Important Features <a id="important"></a>

- [Business configuration](dev/tars-config.md)
- [Service monitoring](dev/tars-monitor.md)
- [Template configuration](dev/tars-template.md)
- [Guidelines for Web's User System](dev/tars-web-user.md)
- [Web Management platform API](dev/tars-web-api.md)
- [Call chain](dev/tars-call-chain.md)
- [IDC Set](dev/tars-idc-set.md)
- [Auth](dev/tars-auth.md)

## Open Source Cooperation <a id="cooperation">

- [TarsFramework GitHub Cooperation Rules](cooperation/tars_framework_git_flows.md)

## FAQ <a id="question"></a>

- [Install](question/Install_faq-en.md)
- [TarsCPP](question/tarscpp-question.md)
- [TarsJava](question/tarsjava-question.md)
- [TarsPHP](question/tarsphp-question.md)
