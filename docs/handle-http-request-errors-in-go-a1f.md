# 在 Go 中处理 HTTP 请求错误

> 原文：<https://dev.to/plutov/handle-http-request-errors-in-go-a1f>

在这篇短文中，我想讨论在 Go 中处理 HTTP 请求错误。我看到人们编写代码，他们认为在进行 HTTP 请求时处理错误，但实际上他们遗漏了真正的错误。

下面是一个简单的 http 服务器和 GET 请求的例子。

```
package main

import (
    "log"
    "net/http"
)

func main() {
    http.HandleFunc("/500", func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(500)
        w.Write([]byte("NOT-OK"))
    })
    go http.ListenAndServe(":8080", nil)

    _, err := http.Get("http://localhost:8080/500")
    if err != nil {
        log.Fatal(err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的代码:我们正在启动一个服务器，然后我们向这个服务器发出一个请求。它返回 500 响应代码，我们检查返回的`err`值，但是...

```
if err != nil {
    log.Fatal(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们运行这段代码，我们将不会捕捉到错误。正如官方[文件](https://golang.org/pkg/net/http/#Client.Get)所说:

> 如果客户端的 CheckRedirect 函数失败或者存在 HTTP 协议错误，则会返回一个错误。非 2xx 响应**不会**导致错误。

所以我们应该经常做的是，检查带有错误的响应代码:

```
resp, err := http.Get("http://localhost:8080/500")
if err != nil {
    log.Fatal(err)
}
if resp.StatusCode != 200 {
    b, _ := ioutil.ReadAll(resp.Body)
    log.Fatal(string(b))
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们运行我们的代码，它将在非 200 响应代码的情况下记录响应正文。这是一个容易犯的错误。但是现在你知道了，这是成功的一半！

[我博客中的原帖](http://pliutau.com/handle-http-request-errors-in-go/)