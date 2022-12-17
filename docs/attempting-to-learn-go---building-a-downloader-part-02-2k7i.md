# 尝试学习围棋-构建下载器第 2 部分

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-02-2k7i>

[上次](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-01-44gl)我们概述了创建一个简单的 Go 服务器来下载远程文件的计划。这次我想做的是开始写一些实际的代码。我将尝试记录下我是如何把程序和我使用的任何参考资料放在一起的。关于变量的命名，我也试着明确一些。例如，使用`response http.ResponseWriter, request *http.Request`代替`w http.ResponseWriter, r *http.Request`。希望任何一个有技能的人都能遵循这个代码。

## 过关一

我们的第一个任务只是在屏幕上显示一些代码。我们将定义我们的包，`main`和一个单独的导入`fmt`(格式)，这样我们就可以打印到标准输出。

```
package  main  import  "fmt" 
```

我们将使用之前定义的 JSON 对象和 [JSON-to-Go](https://mholt.github.io/json-to-go/) 网站来快速创建我们的结构。我推荐给这个站点添加书签，如果你打算在 Go 中使用 JSON，它会非常方便。

```
type  download  struct  {  Title  string  `json:"title"`  Location  string  `json:"location"`  } 
```

在我们的主函数中，现在，我们首先要在启动时简单地打印“Downloader”。之后，我们将创建一个测试结构，然后打印出来。

```
func  main()  {  fmt.Printf("Downloader")  download  :=  download{  Title:  "title-test",  Location:  "location-test",  }  fmt.Printf("%v",  download)  } 
```

它在运行时会产生以下输出。

```
$ go run downloader.go
Downloader  
{title-test location-test} 
```

* * *

## 传二

首先要注意的是，我们使用了两个新的导入。io/ioutil 和 T2 net/http 都是 Go 标准库的一部分，它们将帮助实现一些功能。标准库为开发者提供了创建许多不同程序所需的原始工具。他们需要做的就是学会如何把它们组合在一起。正如我们将在以后的文章中看到的，如果一个函数的标准库包不存在，那么社区中很有可能有人已经想到了这一点。

```
package  main  import  (  "fmt"  "io/ioutil"  "net/http"  )  type  download  struct  {  Title  string  `json:"title"`  Location  string  `json:"location"`  } 
```

我们现在来看看我们的第一个新功能，`status`。目前，在服务器上请求“/”将导致一个 200 响应，其主体为“Hello！”。最后，我们将对其进行更新，以返回一些服务器信息，用于服务健康检查。

```
func  status(response  http.ResponseWriter,  request  *http.Request)  {  fmt.Fprintf(response,  "Hello!")  } 
```

下一个新函数`handleDownloadRequest`将是我们程序的服务器部分的主要焦点。在当前的形式中，它将接受“/download”上的请求，并将请求的正文打印到控制台和单词“download！”到浏览器。

```
func  handleDownloadRequeset(response  http.ResponseWriter,  request  *http.Request)  {  r,  err  :=  ioutil.ReadAll(request.Body)  if  err  !=  nil  {  fmt.Println(err)  }  defer  request.Body.Close()  fmt.Println(string(r))  fmt.Fprintf(response,  "Download!")  } 
```

为了利用我们的新功能，我们更新了`main`来设置 HTTP 处理程序。最后，我们启动服务器监听端口 3000。

```
func  main()  {  fmt.Println("Downloader")  http.HandleFunc("/",  status)  http.HandleFunc("/download",  handleDownloadRequest)  http.ListenAndServe(":3000",  nil)  } 
```

* * *

## 三关

这次的大部分变化发生在`handleDownloadRequest`函数中。然而，如果你仔细观察，你会发现我还添加了[编码/json](https://golang.org/pkg/encoding/json) 和[日志](https://golang.org/pkg/log)作为导入。作为一个服务器应用程序，我想使用`log`的内置特性来打印到控制台。我不确定我是要把它重定向到文件，还是把它留在控制台上。

```
package  main  import  (  "encoding/json"  "fmt"  "io/ioutil"  "log"  "net/http"  )  type  download  struct  {  Title  string  `json:"title"`  Location  string  `json:"location"`  }  func  status(response  http.ResponseWriter,  request  *http.Request)  {  fmt.Fprintf(response,  "Hello!")  } 
```

目前，我们项目的核心就在这里。我们看起来有一些重大的变化。首先，我们创建一个名为`downloadReqest`的结构来保存传入的标题和 URL。我还添加了一些更好的错误处理，在适当的时候对浏览器做出实际的响应。

```
func  handleDownloadRequest(response  http.ResponseWriter,  request  *http.Request)  {  var  downloadRequest  download  r,  err  :=  ioutil.ReadAll(request.Body)  if  err  !=  nil  {  http.Error(response,  "bad request",  400)  log.Println(err)  return  }  defer  request.Body.Close() 
```

假设初始请求是正确的，并且我们能够读取主体，那么我们需要将请求 JSON 放入我们的结构中。我会帮我们处理重物的。如果不是，我们可以向浏览器返回一个错误，这里我们在输出中包括来自`json.Unmarshal`的错误消息。

```
err  =  json.Unmarshal(r,  &downloadRequest)  if  err  !=  nil  {  http.Error(response,  "bad request: "+err.Error(),  400)  log.Println(err)  return  }  log.Printf("%#v",  downloadRequest)  fmt.Fprintf(response,  "Download!")  }  func  main()  {  log.Println("Downloader")  http.HandleFunc("/",  status)  http.HandleFunc("/download",  handleDownloadRequest)  http.ListenAndServe(":3000",  nil)  } 
```

对于一点点工作来说还不错。我们很快就从一个基本框架变成了一个服务器。从明天开始，在我们的下一次修订中，我们将通过添加一个`getFile`函数来更新`handleDownloadRequest`函数，我们可以调用这个函数来获取我们的实际文件。

直到下次...

* * *

你可以在 GitHub 上的 repo 中找到这个和大多数其他试图学习围棋的帖子的代码。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [【新达昆】](https://github.com/shindakun) / [ atlg](https://github.com/shindakun/atlg)

### 我在 dev.to 上发布的“尝试学习围棋”帖子的来源报告

<article class="markdown-body entry-content p-5" itemprop="text">

# 尝试学习围棋

在这里你可以找到我为尝试学习围棋而写的代码，这些代码是我在 [Dev.to](https://dev.to/shindakun) 上写的。

## 帖子索引

| 邮政 | 密码 |
| --- | --- |
| [制作下载器第 01 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-01-44gl) | - |
| [制作下载器第 02 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-02-2k7i) | - |
| [制作下载器第 03 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-03-2214) | - |
| [制作下载器第 4 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-04-3ln9) | - |
| [建造下载器第 05 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-05-44o) | - |
| [使用 REST API](https://dev.to/shindakun/attempting-to-learn-go---consuming-a-rest-api-5c7g) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-01/main.go) |
| [继续休息冒险](https://dev.to/shindakun/attempting-to-learn-go---continuing-rest-adventures-2l4l) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-02/main.go) |
| [现在发送休息请求](https://dev.to/shindakun/attempting-to-learn-go---now-sending-rest-requests-akp) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-03/main.go) |
| [REST API 和模板上的位](https://dev.to/shindakun/attempting-to-learn-go---rest-api-and-a-bit-on-templates-4kca) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-04/main.go) |
| [再次通过 API 发送电子邮件](https://dev.to/shindakun/attempting-to-learn-go---sending-email-via-api-again-2e4e) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-05/main.go) |
| [让我们模块化吧！](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular-390i) | [src](https://github.com/shindakun/mailgunner) |
| [让我们再一次模块化！](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular---again-10cd) | [src](https://github.com/shindakun/mailgunner) |
| [构建开发日志第 1 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-01-1c3m) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-01/main.go) |
| [构建开发日志第 2 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-02-179c) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-02/main.go) |
| [构建开发日志第 3 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-03-7lk) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-03/main.go) |
| [构建开发日志第 4 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-04-2bok) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-04/main.go) |
| [构建开发日志第 5 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-05-4mo1) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-05/main.go) |
| [按扩展名 01 列出文件](https://dev.to/shindakun/attempting-to-learn-go---listing-files-by-extension-1n10) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-sort-01/main.go) |
| [按扩展名 02 列出文件](https://dev.to/shindakun/attempting-to-learn-go---sorting-and-moving-files-by-extension-227j) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-sort-01/main.go) |
| [开发至 API 01](https://dev.to/shindakun/interacting-with-the-devto-article-api-4g34) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devtoapi-01/main.go) |
| [开发至 API 02](https://dev.to/shindakun/interacting-with-the-devto-article-api---again-sort-of-2o8g) | 参见上面的代码 |

</article>

[View on GitHub](https://github.com/shindakun/atlg)

* * *

| 喜欢这篇文章吗？ |
| --- |
| 给我买杯咖啡怎么样？ |

* * *