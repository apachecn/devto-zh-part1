# 使用 Golang 模板发送 HTML 电子邮件

> 原文：<https://dev.to/dhanushgopinath/sending-html-emails-using-templates-in-golang>

在 [Geektrust](https://geektrust.in) 应用程序(构建在 NodeJS 上)中，我们发送了大量在运行时使用 HTML 模板自动生成的电子邮件。在我们在 [Go](https://golang.org/) 开发的新应用中，我们需要同样的特性。

这篇文章是一篇关于使用 Go 的`html/template`和`net/smtp`包读取 HTML 模板文件和发送电子邮件的短文。

代码如下所示。

```
package main

import (
    "bytes"
    "fmt"
    "html/template"
    "net/smtp"
)

var auth smtp.Auth

func main() {
    auth = smtp.PlainAuth("", "iamwho@whoami.com", "password", "smtp.gmail.com")
    templateData := struct {
        Name string
        URL  string
    }{
        Name: "Dhanush",
        URL:  "http://geektrust.in",
    }
    r := NewRequest([]string{"junk@junk.com"}, "Hello Junk!", "Hello, World!")
    err := r.ParseTemplate("template.html", templateData)
    if err != nil {
        ok, _ := r.SendEmail()
        fmt.Println(ok)
    }
}

//Request struct
type Request struct {
    from    string
    to      []string
    subject string
    body    string
}

func NewRequest(to []string, subject, body string) *Request {
    return &Request{
        to:      to,
        subject: subject,
        body:    body,
    }
}

func (r *Request) SendEmail() (bool, error) {
    mime := "MIME-version: 1.0;\nContent-Type: text/plain; charset=\"UTF-8\";\n\n"
    subject := "Subject: " + r.subject + "!\n"
    msg := []byte(subject + mime + "\n" + r.body)
    addr := "smtp.gmail.com:587"

    if err := smtp.SendMail(addr, auth, "dhanush@geektrust.in", r.to, msg); err != nil {
        return false, err
    }
    return true, nil
}

func (r *Request) ParseTemplate(templateFileName string, data interface{}) error {
    t, err := template.ParseFiles(templateFileName)
    if err != nil {
        return err
    }
    buf := new(bytes.Buffer)
    if err = t.Execute(buf, data); err != nil {
        return err
    }
    r.body = buf.String()
    return nil
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我将 smtp 请求封装在一个结构`Request`中。它包含了像 To、Subject、Body 这样的基本东西。这个模板文件有数据占位符，这些占位符将被 Go 的`html/template`包[执行](https://golang.org/pkg/html/template/#Template.Execute)方法替换为实际数据值。

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
        "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html>

</head>

<body>
<p>
    Hello {{.Name}}
    <a href="{{.URL}}">Confirm email address</a>
</p>

</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

模板数据是字段值为`Name` & `URL`的结构。它用值初始化，并传递给 NewRequest 方法以创建一个请求对象。

```
templateData := struct {
    Name string
    URL string
 }{
    Name: "Dhanush",
    URL: "http://geektrust.in",
 } 
```

Enter fullscreen mode Exit fullscreen mode

一旦创建了`Request`的实例，就调用它的`ParseTemplate`方法。该方法接收模板文件名和模板数据。它解析模板文件并使用提供的模板数据执行它。一个`bytes.Buffer`作为`io.Writer`被传递给`Execute`方法，这样我们就可以得到替换了数据的 HTML 字符串。然后，这个 HTML 字符串被设置为电子邮件正文。

`SendEmail`方法将 MIME 编码设置为`text/html`并调用 smtp 包的`SendMail`方法来发送邮件。当电子邮件发送成功时，SendEmail 方法返回一个 true 值。

注意事项:

1.  如果您在 ParseTemplate 方法中返回请求，而不是错误，那么我们在一行中进行调用。但是任何来自 ParseTemplate 的错误都必须在方法内部被跟踪。
2.  阅读`html/template`的包文档，了解 Go 如何用实际数据替换 HTML。
3.  感谢吉杰什·莫汉和 T2·纳瓦尼特·k·N 让围棋代码比以前更加地道:)
4.  这篇文章最初发表在我的个人博客