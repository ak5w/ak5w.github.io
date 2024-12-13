[返回](/docs/dev/base/index.md)

##  golang 源码修改编译调试
### 1 修改源代码
修改 `src/fmt/print.go` 文件中的方法

```go
func Println(a ...any) (n int, err error) {
	println("debuging println.")
	return Fprintln(os.Stdout, a...)
}
```

### 2 编译
编译：在 src 目录下执行 ./make.bash 编译脚本

```shell
 src git:(read/golang) ✗ ./make.bash
Building Go cmd/dist using /usr/local/go/go1.20.5. (go1.20.5 linux/amd64)
Building Go toolchain1 using /usr/local/go/go1.20.5.
Building Go bootstrap cmd/go (go_bootstrap) using Go toolchain1.
Building Go toolchain2 using go_bootstrap and Go toolchain1.
Building Go toolchain3 using go_bootstrap and Go toolchain2.
Building packages and commands for linux/amd64.
---
Installed Go for linux/amd64 in /home/User/Workspaces/readings/go1.20.5
Installed commands in /home/User/Workspaces/readings/go1.20.5/bin
```

### 3 运行

```shell
## 先配置 GOROOT， 否则就使用已安装好的 Go
export GOROOT=/home/User/Workspaces/readings/go1.20.5 

# 或者使用下面命令也不生效
/home/User/Workspaces/readings/go1.20.5/bin/go run main.go

# 配置完后就可以直接使用 go
go run main.go
```

### 4 使用案例

```go
package main

import "fmt"

func main() {
	fmt.Println("hello debug golang")
}
```

运行结果如下：

```shell
debuging println.
hello debug golang
```
