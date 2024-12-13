> golang 的类型转换问题

实际上，在 `go` 语言本身中，已经提供了 [strconv包 (string convert)](https://golang.org/pkg/strconv/) 进行字符串相关的转换操作。
但存在代码有一点混乱的问题，尤其在方法名上不够易读和“傻白甜”，所以看看 spf13 大神的 cast 。

## 1. 类型转换概述
