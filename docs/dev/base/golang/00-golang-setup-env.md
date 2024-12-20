[返回](/docs/dev/base/index.md)

## golang 开发环境设置

> 有关go语言的介绍可以在 [https://zh.wikipedia.org/zh-cn/Go](https://zh.wikipedia.org/zh-cn/Go) 或官网查看。

### 1. golang 安装

最新版本可在 [The Go Programming Language](https://golang.google.cn/dl/)  或  [Go 语言中文网](https://studygolang.com/dl) 上下载
本文仅提供在`Linux`的安装方法，其他环境请自行查阅

#### 1.1 Linux上安装

可通过如下命令
```shell
$ wget https://studygolang.com/dl/golang/go1.17.3.linux-amd64.tar.gz
$ tar -zxvf go1.17.3.linux-amd64.tar.gz
$ sudo go /usr/local
```
并在 ~/.profile 文件中添加如下环境变量
```shell
export GOROOT=/usr/local/go
export GOPATH=/home/xxx/go
export GOBIN=$GOROOT/bin
export GOPROXY=https://goproxy.cn, direct
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```
然后 通过 source ~/.profile 使配置生效。可通过 go version 检查安装
```shell
➜  ~ go version
go version go1.17.3 linux/amd64
```
可通过 go env 命令查看各项配置

#### 1.2 GoPATH 配置

自从Golang采用Module的方式管理项目后，GOPATH目录已经不是那么重要了，目前主要用来存放依赖的Module库，生成的可执行文件等。

-  bin文件夹存放`go install`命名生成的可执行文件，可以把GOPATH/bin路径加入到PATH环境变量里，如上面配置的GOROOT/bin一样，这样就直接在终端里使用我们go开发的程序了。 
-  pkg文件夹是存在go编译生成的文件。 
-  src存放的是非Go Module项目源代码。 
-  GOROOT 指定了Go 的安装路径 
-  GOPATH 指定了 Go 引用包的存储路径，类似于 Java 体系中的 maven 一样 
-  GOPROXY 


### 2. vscode 配置 go 开发环境


Go语言是采用UTF8编码的，可使用任何文本编辑器开发。在这里使用 VS Code。
[VS code 配置Go语言开发环境](https://www.liwenzhou.com/posts/Go/00_go_in_vscode/)
 
### 3. hello,golang

按惯例，我们的 hello 程序按如下步骤建立：

```shell
➜  ~ sudo mkdir go-demo
➜  ~ cd go-demo 
➜  ~ go mod init go-demo    # 会生成 go.mod 文件
➜  ~ touch main.go
```

在 main.go 中，写入：

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, golang world")
}
```

执行 go build main.go， 然后运行编译好的可执行文件:

```shell
➜  ~ ./main 
Hello, golang world
```

至此，我们的 hello 程序已运行成功，诸行代码详解如下：

- package main：声明了 main.go 所在的包，Go 语言中使用包来组织代码。一个文件夹即一个包，包内可以暴露类型或方法供其他包使用。
- import “fmt”：fmt 是 Go 语言的一个标准库/包，用来处理标准输入输出。
- func main：main 函数是整个程序的入口，main 函数所在的包名也**必须**为 `main`。
- fmt.Println(“Hello, golang world”)：调用 fmt 包的 Println 方法，打印出 “Hello World!”


### 4. 使用 dlv 调试 go 程序

在本节，介绍如何使用 `dlv`进行 `go`程序调试

#### 4.1 dlv 的安装

在golang 项目目录下执行如下命令，即可将 dlv 安装至 `$GO_PATH`所在目录
```shell
go get github.com/derekparker/delve/cmd/dlv
```
使用 `dlv -h`获取帮助信息：
```shell
Delve is a source level debugger for Go programs.

Delve enables you to interact with your program by controlling the execution of the process,
evaluating variables, and providing information of thread / goroutine state, CPU register state and more.

The goal of this tool is to provide a simple yet powerful interface for debugging Go programs.

Pass flags to the program you are debugging using `--`, for example:

`dlv exec ./hello -- server --config conf/config.toml`

Usage:
  dlv [command]

Available Commands:
  attach      Attach to running process and begin debugging.
  connect     Connect to a headless debug server with a terminal client.
  core        Examine a core dump.
  dap         Starts a headless TCP server communicating via Debug Adaptor Protocol (DAP).
  debug       Compile and begin debugging main package in current directory, or the package specified.
  exec        Execute a precompiled binary, and begin a debug session.
  help        Help about any command
  run         Deprecated command. Use 'debug' instead.
  test        Compile test binary and begin debugging program.
  trace       Compile and begin tracing program.
  version     Prints version.

Flags:
      --accept-multiclient               Allows a headless server to accept multiple client connections via JSON-RPC or DAP.
      --allow-non-terminal-interactive   Allows interactive sessions of Delve that don't have a terminal as stdin, stdout and stderr
      --api-version int                  Selects JSON-RPC API version when headless. New clients should use v2. Can be reset via RPCServer.SetApiVersion. See Documentation/api/json-rpc/README.md. (default 1)
      --backend string                   Backend selection (see 'dlv help backend'). (default "default")
      --build-flags string               Build flags, to be passed to the compiler. For example: --build-flags="-tags=integration -mod=vendor -cover -v"
      --check-go-version                 Exits if the version of Go in use is not compatible (too old or too new) with the version of Delve. (default true)
      --disable-aslr                     Disables address space randomization
      --headless                         Run debug server only, in headless mode. Server will accept both JSON-RPC or DAP client connections.
  -h, --help                             help for dlv
      --init string                      Init file, executed by the terminal client.
  -l, --listen string                    Debugging server listen address. (default "127.0.0.1:0")
      --log                              Enable debugging server logging.
      --log-dest string                  Writes logs to the specified file or file descriptor (see 'dlv help log').
      --log-output string                Comma separated list of components that should produce debug output (see 'dlv help log')
      --only-same-user                   Only connections from the same user that started this instance of Delve are allowed to connect. (default true)
  -r, --redirect stringArray             Specifies redirect rules for target process (see 'dlv help redirect')
      --wd string                        Working directory for running the program.

Additional help topics:
  dlv backend  Help about the --backend flag.
  dlv log      Help about logging flags.
  dlv redirect Help about file redirection.

Use "dlv [command] --help" for more information about a command.

```

[https://www.cnblogs.com/realjimmy/p/13418508.html](https://www.cnblogs.com/realjimmy/p/13418508.html)

### 5. golang 文档查询
