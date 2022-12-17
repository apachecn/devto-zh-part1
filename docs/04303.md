# 如何给你的用户发邮件而不给他们发垃圾邮件？

> 原文：<https://dev.to/gcdcoder/how-to-email-your-users-without-sending-them-spam-1la>

电子邮件功能是我们在 web 应用程序中(几乎)一直在使用的东西，所以在开发阶段测试这个功能并避免向我们的用户发送垃圾邮件非常重要。正因为如此，我写这篇文章告诉你关于 [Mailtrap](https://mailtrap.io/) 的事情，这是一个为开发团队设计的假 smtp 测试服务器。为了使用 Mailtrap，您必须按照以下步骤设置您的收件箱:

1.  点击此处的创建一个账户。
2.  为您的收件箱写一个名称(我将使用“testing”)，然后单击带有文本 Create Inbox 的绿色按钮。
3.  点击您的测试收件箱，查看您的证书。

现在我们来谈谈 Go 代码，在这个教程中，我将使用 [gomail](https://github.com/go-gomail/gomail) 包发送电子邮件，点击[这里](https://github.com/go-gomail/gomail#download)下载。在您的 go 工作区中创建以下项目结构:

```
mailing\
    image.jpg // May be any image you want to.
    mail.html
    main.go 
```

Enter fullscreen mode Exit fullscreen mode

在你最喜欢的代码编辑器中打开`mailing`文件夹，然后打开`main.go`文件并编写以下代码:

```
package main

import (
        "fmt"
        "io/ioutil"
        "gopkg.in/gomail.v2"
)

func main() {
        file, err := ioutil.ReadFile("mail.html") 
        if err != nil {
                fmt.Errorf("Could not open the file due to %v ", err.Error())
                return
        }

        // put your own credentials instead
        // order: Host, port, username, password
        m := gomail.NewMessage("smtp.mailtrap.io", 2525, "bc580f0dd3826c", "caec98e37af35d")
        m.SetHeader("From", "gcdcoder@gmail.com")
        m.SetHeader("To", "fake-user@mail.com")
        m.SetHeader("Subject", "welcome to my site")
        m.Embed("image.jpg")
        m.SetBody("text/html", string(file))

        dialer := gomail.NewDialer()

        if err := dialer.DialAndSend(m); err != nil {
                fmt.Errorf("Could not send the Email due to %v ", err.Error())
                return
        }
        fmt.Println("Email sent successfully!")
} 
```

Enter fullscreen mode Exit fullscreen mode

现在打开`mail.html`文件，写下如下内容:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    Email
    <style>
        body, html {
            background: #eee;
            width: 100%;
            height: 100vh;
            overflow: hidden;
            margin: 0;
            padding: .5em 0;
            text-align: center;
        }

        img {
            max-width: 100%;
            width: 250px;
            height: 150px;
            margin-top: 1em;
        }

        h1 {
            margin: 0;
            color: steelblue;
            text-align: center;
            font-family: sans-serif;
        }

        a {
            text-decoration: none;
            display: block;
            width: 200px;
            color: #fff;
            background: steelblue;
            text-align: center;
            padding: .5em 0;
            font-family: sans-serif;
            margin-left: auto;
            margin-right: auto;
            margin-top: 1em;
        }
    </style>
</head>
<body>
    <h1>Saludo de bienvenida</h1>
    <img src="cid:image.jpg" alt="Image">
    <a href="http://twitter.com/gcdcoder" target="_blank">Click me!</a>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

是时候测试我们的代码了，在你的`mailing`文件夹中打开一个终端，写下下面的命令:

```
go run main.go 
```

Enter fullscreen mode Exit fullscreen mode

过一会儿你应该会看到消息`Email sent successfully!`，然后在 Mailtrap 中访问你的收件箱，你应该会看到一个新的电子邮件打开它，哇，你有它了！。

**注意:**为了简单起见，我使用`ioutil`包来读取`mail.html`文件，我知道这不是最好的方式，你应该使用`html/template`包来代替。

感谢阅读和快乐编码！；)