# 如何刮 Instagram 个人资料

> 原文：<https://dev.to/asciimoo/how-to-scrape-instagram-profiles-4gm>

抓取可能是一项单调乏味的工作，尤其是当目标站点不仅仅是一个标准的静态 HTML 页面时。许多现代网站只有 JavaScript，提取内容并不总是那么简单。Instagram 就是这些网站中的一个，所以我将向您展示如何相对快速地编写一个 scraper 来从 Instagram 获取图像。我用的是 [Colly](http://go-colly.org/) ，Golang 的一个抓取框架。完整的工作示例可以在[这里](http://go-colly.org/docs/examples/instagram/)找到。

## 情报收集

首先，如果我们查看一个个人资料页面的源代码(例如[https://instagram.com/instagram](https://instagram.com/instagram))，我们可以在`body`标签中看到一堆 JavaScript 代码，而不是静态的 HTML 标签。让我们仔细看看。我们可以看到第一个`script`只是一个变量声明，其中一个巨大的 JSON 被赋给一个变量(`window._sharedData`)。通过找到第一个`{`字符并获得它后面的全部内容，可以很容易地从`script`标签中提取这个 JSON:

```
jsonData  :=  scriptContent[strings.Index(scriptContent,  "{")  :  len(scriptContent)-1] 
```

注意，因为它是一个 JavaScript 变量声明，所以它后面有一个分号，我们必须去掉它才能得到一个有效的 JSON。这就是为什么上面的例子以`len(scriptContent)-1`结尾。

提取的 JSON 的格式化视图揭示了我们正在寻找的所有信息。JSON 包含关于用户图像的信息和简档的一些元数据(例如，简档 ID 是`25025320`)。元数据中有一个有趣的部分叫做`page_info`:

```
 "page_info":  {  "has_next_page":  true,  "end_cursor":  "AQBiQhGRC6c6f-YOxdU0ApaAvotN4zI601ymkAtQ8SutdWz2n-bKFCkv51PMAoi9im3tNDTFLyhV969z8a6JnAkQMzHbYVwNI4Ke7jbk99nvFA"  } 
```

大概，`has_next_page`为`true`时，`end_cursor`的值就是获取下一页的 URL 的属性。

用方便的 [jq](https://github.com/stedolan/jq) 命令行工具格式化 JSONs

### 分页

用户配置文件的下一页由 AJAX 调用获取，因此我们必须使用浏览器的网络检查器来找出获取它所需的内容。Network Inspector 显示了一个长而神秘的 URL，它有两个 GET 参数`query_id`和`variables`:

```
https://www.instagram.com/graphql/query/?query_id=17888483320059182&variables=%7B%22id%22%3A%2225025320%22%2C%22first%22%3A12%2C%22after%22%3A%22AQBiQhGRC6c6f-YOxdU0ApaAvotN4zI601ymkAtQ8SutdWz2n-bKFCkv51PMAoi9im3tNDTFLyhV969z8a6JnAkQMzHbYVwNI4Ke7jbk99nvFA%22%7D 
```

似乎 Instagram 使用了一个 [GraphQL](https://en.wikipedia.org/wiki/GraphQL) API，而`variables` GET 参数的值是一个 URL 编码值。我们可以用一行 Python 代码来解码它:

```
$ python -c 'import urlparse;print(urlparse.parse_qs("variables=%7B%22id%22%3A%2225025320%22%2C%22first%22%3A12%2C%22after%22%3A%22AQBiQhGRC6c6f-YOxdU0ApaAvotN4zI601ymkAtQ8SutdWz2n-bKFCkv51PMAoi9im3tNDTFLyhV969z8a6JnAkQMzHbYVwNI4Ke7jbk99nvFA%22%7D")["variables"][0])'
{"id":"25025320","first":12,"after":"AQBiQhGRC6c6f-YOxdU0ApaAvotN4zI601ymkAtQ8SutdWz2n-bKFCkv51PMAoi9im3tNDTFLyhV969z8a6JnAkQMzHbYVwNI4Ke7jbk99nvFA"} 
```

如您所见，它是一个 JSON 对象，`after`属性的值与`end_cursor`的值相同，`id`是概要文件的 ID。

下一个页面 URL 中唯一未知的信息是`query_id` GET 参数。HTML 源代码不包含它，cookies 或响应头也不包含它。经过一点挖掘，可以在主页中的一个静态 JS 文件中找到它，看起来它是一个常量值。

响应的格式也是 JSON，但是结构与我们在主页上看到的不同。这个 JSON 包含与前一个相同的信息，但是由于结构差异，我们不能使用相同的方法提取数据。

## 建造铲运机

信息收集阶段清楚地表明，我们需要四个构建模块来获取 Instagram 个人资料上的所有图像。让我们用 Colly 来做。

### 从主页中提取并解析 JSON

为了从 HTML 中提取内容，我们需要一个新的`Collector`，它有一个 HTML 回调来从`script`元素中提取 JSON 数据。可以在`Collector`的`OnHTML`函数中指定该回调以及何时必须调用该回调。使用标准库中的`json.Unmarshal`，可以很容易地将 JSON 转换成本地 Go 结构。

```
c  :=  colly.NewCollector()  c.OnHTML("body > script:first-of-type",  func(e  *colly.HTMLElement)  {  // find JSON string  jsonData  :=  e.Text[strings.Index(e.Text,  "{")  :  len(e.Text)-1]  // parse JSON  data  :=  struct  {  EntryData  struct  {  ProfilePage  []struct  {  User  struct  {  Id  string  `json:"id"`  Media  struct  {  Nodes  []struct  {  ImageURL  string  `json:"display_src"`  ThumbnailURL  string  `json:"thumbnail_src"`  IsVideo  bool  `json:"is_video"`  Date  int  `json:"date"`  Dimensions  struct  {  Width  int  `json:"width"`  Height  int  `json:"height"`  }  }  PageInfo  pageInfo  `json:"page_info"`  }  `json:"media"`  }  `json:"user"`  }  `json:"ProfilePage"`  }  `json:"entry_data"`  }{}  err  :=  json.Unmarshal([]byte(jsonData),  &data)  if  err  !=  nil  {  log.Fatal(err)  }  // enumerate images  page  :=  data.EntryData.ProfilePage[0]  actualUserId  =  page.User.Id  for  _,  obj  :=  range  page.User.Media.Nodes  {  // skip videos  if  obj.IsVideo  {  continue  }  c.Visit(obj.ImageURL)  }  // ...  } 
```

### 创建和访问下一页 URL

下一个页面 URL 的格式是固定的，所以可以声明一个格式字符串，它接受变化的`id`和`after`参数。

```
const  nextPageURLTemplate  string  =  `https://www.instagram.com/graphql/query/?query_id=17888483320059182&variables={"id":"%s","first":12,"after":"%s"}` 
```

### 解析下一页 JSONs

这与主页面的 JSON 的转换非常相似，除了这些响应有一些不同的属性名称(例如，图像 url 是`display_url`而不是`display_src`)。

### 下载并保存从 JSONs 中提取的图像

使用`Visit`功能从 Instagram 请求图片后，可以在`OnResponse`中处理响应。它需要一个回调作为参数，在响应到达后调用。为了选择包含图像的响应，我们应该基于`Content-Type` HTTP 头进行过滤。如果是图像，必须保存。

```
c.OnResponse(func(r  *colly.Response)  {  if  strings.Index(r.Headers.Get("Content-Type"),  "image")  >  -1  {  r.Save(outputDir  +  r.FileName())  return  }  // handle further response types...  } 
```

## 收场白

抓取只有 JS 的站点并不总是无足轻重的，但是可以在没有无头浏览器和客户端代码执行的情况下处理，以获得很好的性能。这个 scraper 示例通过常规的家庭互联网连接，在单线程上每分钟下载大约 1000 幅图像。

它可以进一步调整，以处理视频和提取元信息。