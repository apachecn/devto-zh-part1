# 尝试学习围棋-构建下载器第 5 部分

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-05-44o>

这篇文章将会和之前的[有所不同。我们只会对 Go 代码做一个小小的更新。这主要是为了解决我上次指出的一些消毒问题。一旦我们完成了这些，我们将为 Chrome 创建一个扩展所需的代码。因此，让我们开始吧！](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-04-3ln9)

## 通六部

我们将从`kennygrant`添加[杀毒](https://shindakun.glitch.me/attempting-to-learn-go-building-a-downloader-part-05/github.com/kennygrant/sanitize)包开始。要在 Go 中添加一个包，我们需要从命令行使用`go get`。

```
go get github.com/kennygrant/sanitize 
```

然后，我们可以将它添加到我们的导入列表中，如下面的代码所示。Sanitize 增加了帮助净化文本字符串的功能。正是我们要找的！这将有助于从我们试图创建的文件和目录中删除任何不需要的字符。

小更新，正如 [@rafaacioly](https://dev.to/rafaacioly) 指出的，我可以在我的 HTTP 错误上使用内置的`http.StatusBadRequest`和`http.StatusInternalServerError`。它有更多的字符，但我相信使用内置值是“正确的”。

```
package  main  import  (  "encoding/json"  "fmt"  "io"  "io/ioutil"  "log"  "net/http"  "net/url"  "os"  "path/filepath"  "strings"  "github.com/kennygrant/sanitize"  )  type  download  struct  {  Title  string  `json:"title"`  Location  string  `json:"location"`  }  func  status(response  http.ResponseWriter,  request  *http.Request)  {  fmt.Fprintf(response,  "Hello!")  }  func  handleDownloadRequest(response  http.ResponseWriter,  request  *http.Request)  {  var  downloadRequest  download  r,  err  :=  ioutil.ReadAll(request.Body)  if  err  !=  nil  {  http.Error(response,  "bad request",  http.StatusBadRequest)  log.Println(err)  return  }  defer  request.Body.Close()  err  =  json.Unmarshal(r,  &downloadRequest)  if  err  !=  nil  {  http.Error(response,  "bad request: "+err.Error(),  http.StatusBadRequest)  return  }  log.Printf("%#v",  downloadRequest)  err  =  getFile(downloadRequest)  if  err  !=  nil  {  http.Error(response,  "internal server error: "+err.Error(),  http.StatusInternalServerError)  return  }  fmt.Fprintf(response,  "Download!")  }  func  createSaveDirectory(title  string)  (string,  error)  {  dir,  err  :=  filepath.Abs(filepath.Dir(os.Args[0]))  if  err  !=  nil  {  log.Fatal(err)  }  path  :=  filepath.Join(dir,  title)  _,  err  =  os.Stat(path)  // directory does not exist we should create it.  if  err  !=  nil  {  err  =  os.Mkdir(path,  0755)  if  err  !=  nil  {  log.Fatal(err)  }  }  return  path,  nil  } 
```

正如你在下面看到的，我们在两个地方调用`sanitize`。第一个是`sanitize.BaseName`，是在将标题传递给`createSaveDirectory`函数时。第二次，`sanitize.Path`，当试图用`os.Create`实际保存文件时。该部分应该注意删除任何我们不想要的字符。例如，如果有人通过提交 JSON `"title": "Fake Title ../../../../../etc/"`来尝试一个带有标题的目录。保存目录仍将被创建为我们的应用程序所在的子文件夹，它的名字只是`Fake-Title-etc-`。文件名的清理略有不同，但最终结果基本相同。我没有做详尽的测试，但现在，我们应该没问题。

```
func  getFile(downloadRequest  download)  error  {  u,  err  :=  url.Parse(downloadRequest.Location)  if  err  !=  nil  {  log.Println(err)  return  err  }  save,  err  :=  createSaveDirectory(sanitize.BaseName(downloadRequest.Title))  if  err  !=  nil  {  log.Println(err)  return  err  }  // Encoding URL via path never seems to work as expected, fall back to  // simply replacing spaces with %20's, for now.  response,  err  :=  http.Get(strings.Replace(downloadRequest.Location,  " ",  "%20",  -1))  if  err  !=  nil  {  log.Println(err)  return  err  }  defer  response.Body.Close()  out,  err  :=  os.Create(filepath.Join(save,  sanitize.Path(filepath.Base(u.Path))))  defer  out.Close()  _,  err  =  io.Copy(out,  response.Body)  if  err  !=  nil  {  log.Println(err)  return  err  }  return  nil  }  func  main()  {  log.Println("Downloader")  http.HandleFunc("/",  status)  http.HandleFunc("/download",  handleDownloadRequest)  http.ListenAndServe(":3000",  nil)  } 
```

这就结束了这篇文章的开始部分。现在，我们要做一个编写 Chrome 扩展的速成班！我不打算讲得太深，现在只讨论组成扩展的三个核心文件。要更深入地了解如何创建扩展，请查看 Chrome 开发者门户网站上的[扩展部分](https://developer.chrome.com/extensions/getstarted)。

* * *

## 镀铬扩展

扩展名通常由三个或四个文件组成。最重要的是`manifest.json`，它为浏览器提供了加载扩展所需的所有信息。权限和背景条目可能是文件中需要注意的最重要的部分。另外，请注意，我们在测试中使用了`localhost`。

```
{  "manifest_version":  2,  "name":  "Send to Downloader",  "short_name":  "Send2DL",  "description":  "Sends JSON to download server.",  "version":  "0.0.1",  "minimum_chrome_version":  "38",  "permissions":  [  "webRequest",  "*://localhost/",  "contextMenus",  "activeTab"],  "icons":  {  "16":  "assets/download.png"  },  "background":  {"page":  "background.html"}  } 
```

我相信你看到我们下面的`background.html`一定很惊讶。没错，它什么都不做，除了 load `main.js`，这实际上是一件大事。

```
<!DOCTYPE html>  
<html>  
  <body>
    <script src="main.js"></script>
  </body>
</html> 
```

是我们扩展的核心，因为它实际上负责将我们的请求放在一起。它还负责将我们的下载选项添加到 Chrome 的右键上下文菜单中。仔细看，你可以看到“发送到下载器”选项只会出现，如果你右击图像。在目前的形式下，代码只是获取当前选项卡的标题，并将其用作保存图像的目录。如果我曾经把这作为一个学习练习，我想把它扩展到任何链接和文件类型。

```
sendToDownloader = function(obj, tab) {  
  let downloaderUrl = 'http://localhost:3000/download';

  let title = tab.title;
  let imgUrl = obj.srcUrl;

  let payload = {
    title: "title,"
    location: imgUrl
  };

  let xhr = new XMLHttpRequest();

  xhr.open("POST", downloaderUrl, false);
  xhr.setRequestHeader('Content-type','application/json; charset=utf-8');
  xhr.send(JSON.stringify(payload));

  let result = xhr.responseText;
};

chrome.contextMenus.create({  
  title: "\"Send to downloader\","
  contexts:["image"],
  onclick: sendToDownloader
}); 
```

这就使我们的练习接近尾声。最后一篇文章将介绍在 Google Compute engine 上实际部署应用程序的过程。我们终于可以看到整个过程了！在那之后，我可能会继续使用下载器，但是我可能不会记录太多关于 [GitHub](https://github.com/shindakun/downloader) 的内容。

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
| [制作下载器第 05 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-05-44o) | - |
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