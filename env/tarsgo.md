# TarsGo Development Environment Setup

## Content

> * [Environment Dependence](#Environment-Dependence)
> * [Installation](#Installation)

## Environment Dependence

Require Go `1.13.x` or above，see https://golang.org/doc/install

## Installation

Install go (for example go install path: `/usr/local/go`), and configure `GOROOT`, `GOPATH`. For example, in Linux:

```sh
export GOROOT=/usr/local/go
export GOPATH=/root/gocode
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

If you are in China, you can set up go proxy:

```sh
go env -w GOPROXY=https://goproxy.cn   
```

Set Go Modules to `auto`:

```sh
go env -w GO111MODULE=auto
```

Enter `GOPATH` and get TarsGo

```sh
cd $GOPATH
go get -u github.com/TarsCloud/TarsGo/tars
```

And TarsGo is downloaded to path: `$GOPATH/src/github.com/TarsCloud/TarsGo/`.

If you cannot find source code of TarsGo in this path, please check the above steps.

After TarsGo is downloaded, install `tars2go`:

```sh
go install $GOPATH/src/github.com/TarsCloud/TarsGo/tars/tools/tars2go
```
