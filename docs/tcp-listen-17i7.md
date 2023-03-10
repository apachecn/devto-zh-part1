# 侦听 TCP 上的空闲端口

> 原文：<https://dev.to/shiena/tcp-listen-17i7>

# 前言

启动服务器时，端口可以是任何端口，所以有时想 Listen 空闲端口。
我原以为只能按顺序调查除了井诺端口以外的其他端口，
没那么麻烦，只要 Listen 端口 0，就会分配空闲端口。

# 样品

以下是用 Go 写的验证代码。

```
package main

import (
    "fmt"
    "net"
)

func main() {
    l, err := net.Listen("tcp", "localhost:0")
    if err == nil {
        defer l.Close()

        addr := l.Addr().String()
        _, port, err := net.SplitHostPort(addr)

        if err == nil {
            fmt.Printf("    listen port: %s\n", port)
        } else {
            fmt.Printf("    %s\n", addr)
        }
    } else {
        fmt.Println("can't listen")
    }
} 
```

每次运行时都会分配不同的端口。

```
% go run main.go
    listen port: 64522
% go run main.go
    listen port: 64830
% go run main.go
    listen port: 64835
% go run main.go
    listen port: 64838 
```

# 参考链接

*   [想取用 Java 空着的端口号](http://moznion.hatenadiary.com/entry/2014/11/29/222221)
*   [知名港口](http://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers#Well-known_ports)