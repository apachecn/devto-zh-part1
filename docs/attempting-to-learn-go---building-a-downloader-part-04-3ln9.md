# 尝试学习围棋-构建下载器第 4 部分

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-04-3ln9>

欢迎回来！如果您还记得，[上次](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-03-2214)我们添加了实际下载文件的功能。这一次，我们将在此基础上继续努力。你可以马上看到我们只添加了一个导入，[字符串](https://godoc.org/strings)。您将在下面的使用中看到这一点，因为大部分基本代码在这次迭代中保持不变。

* * *

## 五关

```
package  main  import  (  "encoding/json"  "fmt"  "io"  "io/ioutil"  "log"  "net/http"  "net/url"  "os"  "path/filepath"  "strings"  )  type  download  struct  {  Title  string  `json:"title"`  Location  string  `json:"location"`  }  func  status(response  http.ResponseWriter,  request  *http.Request)  {  fmt.Fprintf(response,  "Hello!")  }  func  handleDownloadRequest(response  http.ResponseWriter,  request  *http.Request)  {  var  downloadRequest  download  r,  err  :=  ioutil.ReadAll(request.Body)  if  err  !=  nil  {  http.Error(response,  "bad request",  400)  log.Println(err)  return  }  defer  request.Body.Close()  err  =  json.Unmarshal(r,  &downloadRequest)  if  err  !=  nil  {  http.Error(response,  "bad request: "+err.Error(),  400)  return  }  log.Printf("%#v",  downloadRequest)  err  =  getFile(downloadRequest)  if  err  !=  nil  {  http.Error(response,  "internal server error: "+err.Error(),  500)  return  }  fmt.Fprintf(response,  "Download!")  } 
```

这里我们来看看我们的第一个新函数`createSaveDirectory`。发生了很多事情，所以我试着把它分解一下。函数本身接受一个字符串，在本例中，是在我们的 struct 中定义的`title`字段(我们从传入的 JSON 构建的)，并返回一个字符串或一个错误。

我们保存文件的第一步是获取程序当前运行目录的绝对路径。在开发的这个阶段，在我的本地机器上，恰好是`/e/Projects/Go/src/downloader`。

```
func  createSaveDirectory(title  string)  (string,  error)  {  dir,  err  :=  filepath.Abs(filepath.Dir(os.Args[0]))  if  err  !=  nil  {  log.Fatal(err)  } 
```

如果其中任何一个失败了，我们不仅仅是记录一个错误并返回，相反，我们会抛出一个致命错误并完全退出。现在我们已经将目录存储在变量`dir`中，我们将使用`filepath.Join`(如下所示)添加我们传入的标题，作为我们想要保存文件的目录的名称。对于这个使用我们以前的 JSON 的例子，它将是`/e/Projects/Go/src/downloader/Attempting Go`。现在，这是另一个我们正在引入问题的例子——我们假设我们的输入是格式良好的，不会在文件系统上引起问题。*不要在生产中这样做。*我想我们很快会重新讨论这些问题。毕竟，我们不想冒险写入服务器上的错误位置。

无论如何，一旦我们有了文件路径，我们就调用`os.Stat`来查看该路径是否已经存在，如果不存在，我们就尝试创建它。如果它已经存在，我们就把完整的路径返回给调用者。

```
path  :=  filepath.Join(dir,  title)  _,  err  =  os.Stat(path)  // directory does not exist we should create it.  if  err  !=  nil  {  err  =  os.Mkdir(path,  0755)  if  err  !=  nil  {  log.Fatal(err)  }  }  return  path,  nil  }  func  getFile(downloadRequest  download)  error  {  u,  err  :=  url.Parse(downloadRequest.Location)  if  err  !=  nil  {  log.Println(err)  return  err  } 
```

我不得不稍微修改一下`http.Get`调用，因为它有编码不正确的 URL 的问题，这是意料之中的。更具体地说，URL 包含空格，而不是+或%20。我使用内置的 URL 包尝试了几种不同的 URL 编码方法，但是，它似乎从来没有按照需要的方式进行编码。最后，为了让事情向前发展，我决定只使用`strings.Replace`来简单地将空格换成正确的%20 编码。在这种情况下，我已经在 GET 调用中完成了，在未来的版本中，我们将在一个完全独立的函数中解决我们的输入验证问题。

```
response,  err  :=  http.Get(strings.Replace(downloadRequest.Location,  " ",  "%20",  -1))  if  err  !=  nil  {  log.Println(err)  return  err  }  defer  response.Body.Close() 
```

最后，我们有响应体，所以我们要确保有地方保存它。所以，我们调用上面描述的`createSaveDirectory`函数。我可能会将这几行移到`http.Get`之上，毕竟，如果我们在本地准备保存时遇到问题，实际执行下载是不值得的。

```
save,  err  :=  createSaveDirectory(downloadRequest.Title)  if  err  !=  nil  {  log.Println(err)  return  err  }  out,  err  :=  os.Create(filepath.Join(save,  filepath.Base(u.Path)))  defer  out.Close()  _,  err  =  io.Copy(out,  response.Body)  if  err  !=  nil  {  log.Println(err)  return  err  }  return  nil  }  func  main()  {  log.Println("Downloader")  http.HandleFunc("/",  status)  http.HandleFunc("/download",  handleDownloadRequest)  http.ListenAndServe(":3000",  nil)  } 
```

这就使我们结束了这次修订。我打算明天再来一遍，尝试清理一些编码问题，并整理一些输入验证。然后我们将决定如何在“云中”运行这些代码，也许还会涉及到我们实际上如何使用它。

直到[下一次](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-05-44o)...

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