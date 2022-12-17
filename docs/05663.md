# AWS 和 Go

> 原文：<https://dev.to/klaidliadon/aws-and-go>

# AWS 和 Go

<small>原载于[blog . vidsey . io](https://blog.vidsey.io/post/awsandgo/)T3】</small>

AWS 拥有仓库 [aws/aws-sdk-go](http://github.com/aws/aws-sdk-go) ，它是自己 API 的 go 版本。
它提供的许多服务都有几个第三方库，但使用前者有一些优势:

*   它是由 AWS 自己维护的
*   它提供最完整的一套服务(全部)
*   它是通过 AWS API 以编程方式创建的，因此不太容易出现人为错误
*   它总是与最新的 API 变化保持同步

另外:

*   有一个专门的 [Gitter chat](https://gitter.im/aws/aws-sdk-go)
*   和一个用于[堆栈溢出](http://stackoverflow.com/questions/tagged/aws-sdk-go)的标签

## 凭证

使用 AWS 和 Go 的第一步是使用 [IAM](https://aws.amazon.com/documentation/iam/) 为您的用例创建一个用户，并为该用户创建一个访问键。
这将产生一个访问密钥和一个密码，你需要它来访问服务。

这个包要求两个值都存储在两个环境变量`AWS_ACCESS_KEY_ID`和`AWS_SECRET_ACCESS_KEY`中。
您可以选择将这个变量直接存储在您的环境中，或者您可以从配置文件中读取它们并直接在 Go 运行时中设置它们
，或者您可以在您的配置中使用 [NewStaticCredentials](http://docs.aws.amazon.com/sdk-for-go/api/aws/credentials/#NewStaticCredentials) 函数:

```
package main

import (
    "github.com/aws/aws-sdk-go/aws"
    "github.com/aws/aws-sdk-go/aws/credentials"
    "github.com/aws/aws-sdk-go/aws/endpoints"
    "github.com/aws/aws-sdk-go/aws/session"
)

var config struct { AccessKeyID, SecretAccessKey string }

var s *session.Session

func init() { 
    // Load Configuration
}

func main() {
    // Environment
    os.Setenv("AWS_ACCESS_KEY_ID", config.AccessKeyID)
    os.Setenv("AWS_SECRET_ACCESS_KEY", config.SecretAccessKey)
    s = session.New(&aws.Config{
        Region: aws.String(endpoints.EuWest1RegionID),
    })
    // OR
    s = session.New(&aws.Config{
        Region: aws.String(endpoints.EuWest1RegionID),
        Credentials: credentials.NewStaticCredentials(
            config.AccessKeyID, 
            config.SecretAccessKey,
            "", // a token will be created when the session it's used.
        ),
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您希望在实例和所有 AWS 客户机中拥有相同的凭证，那么使用环境变量是一个好主意，相反，如果您计划为每个客户机/实例使用特定的键，那么第二种选择更合理。

## 样本用途:视频存储

亚马逊简单存储服务(S3)是一种针对规模、性能和耐用性而设计的对象存储。在下面的例子中，我们将使用它来创建一个简单的 web 应用程序，它提供了一个 API 来存储和检索视频文件。

### 权限

一旦你有了你的证书，你就可以在任何一项服务中使用它。给你的用户使用 [IAM](https://dev.to1) 的权限，你就可以开始了。创建一个具有权限的组，然后将用户分配到该组，这是一个很好的做法。对于我们的用例，我们创建一个拥有`AmazonS3FullAccess`权限的`S3`组，并将我们的用户添加到该组中。

### 端点

我们将使用两个端点:

*   将通过邮件接收文件并保存到 S3
*   `download`将恢复并返回文件

我们将使用 [gin](https://github.com/gin-gonic/gin) 而不是标准的`net/http`，因为它增加了许多实用程序，而开销却可以忽略不计。

#### 上传

我们为请求分配一个密钥，如果它不存在，并将文件上传到 S3。

```
func uploadHandler(c *gin.Context) {
    var key = c.Param("key")
    if key == "" {
        key = ulid.MustNew(ulid.Timestamp(time.Now()), entropy).String()
    }
    b, err := ioutil.ReadAll(c.Request.Body)
    if err != nil {
        log.Printf("%s: upload error (%s)", key, err)
        c.JSON(http.StatusInternalServerError, gin.H{"error": "internal error"})
        return
    }
    reader := bytes.NewReader(b)
    _, err = client.PutObject(&s3.PutObjectInput{
        Bucket:        aws.String(Bucket),
        Key:           aws.String(key),
        Body:          reader,
        ContentLength: aws.Int64(reader.Size()),
    })
    if err != nil {
        log.Printf("%s: put error (%s)", key, err)
        c.JSON(http.StatusInternalServerError, gin.H{"error": "internal error"})
        return
    }
    c.JSON(http.StatusCreated, gin.H{"key": key})
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 下载

我们从 S3 检索文件，并将其返回给用户。为了生成一个唯一的 ID，我们使用了 [ULID](https://github.com/oklog/ulid) 包。

```
func downloadHandler(c *gin.Context) {
    var key = c.Param("key")

    obj, err := client.GetObject(&s3.GetObjectInput{
        Bucket: aws.String(Bucket),
        Key:    aws.String(key),
    })
    if err != nil {
        log.Printf("%s: get error (%s)", key, err)
        c.JSON(http.StatusInternalServerError, gin.H{"error": "internal error"})
        return
    }
    defer obj.Body.Close()

    _, err = io.Copy(c.Writer, obj.Body)
    if err != nil {
        log.Printf("%s: download error (%s)", key, err)
        c.JSON(http.StatusInternalServerError, gin.H{"error": "internal error"})
        return
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

查看 [`aws_v1.go`](https://github.com/DauMau/blog.vidsey.io/blob/master/_examples/awsandgo/aws_v1.go) 查看完整示例。

### 额外:增加一些缓存

即使 S3 很快，用户也不应该每次都等着下载文件。我们可以很容易地建立一个机制，将文件下载到一个临时目录中，过一会儿再删除它。这个想法是将文件下载到一个目录中，然后用`time.Timer`删除它，每次在删除文件之前再次请求文件时，计时器都会重置。

```
var cache struct {
    sync.Mutex
    timers map[string]*time.Timer
    root   string
    client *s3.S3
}

cache.timers[key] = e.AfterFunc(keep, func() {
    cache.Lock()
    delete(cache.timers, key)
    os.Remove(cachePath(key))
    cache.Unlock()
}) 
```

Enter fullscreen mode Exit fullscreen mode

完整代码可在 [`aws_v2.go`](https://github.com/DauMau/blog.vidsey.io/blob/master/_examples/awsandgo/aws_v2.go) 获得。

除了减少响应时间，这种方法还为我们的用例带来了巨大的优势。我们使用的是`http.ServeFile`方法，在幕后调用`http.ServeContent`。正如我们所看到的，它接受了一个`io.ReadSeeker`(在我们的例子中是一个`*os.File`)，因为它解析了`Range`头，并只返回选定的范围，而不是整个文件。这对于一个大的视频文件来说是完美的，因为它可以在整个内容下载之前开始播放视频，或者允许你从一部分跳到另一部分。响应的 HTTP 代码不是`200 - OK`，而是`206 - Partial Content`(更多信息请参见 [206](https://httpstatuses.com/206) )。