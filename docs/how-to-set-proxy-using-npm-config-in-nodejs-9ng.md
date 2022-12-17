# 如何在 node.js 中使用 npm 配置设置代理

> 原文：<https://dev.to/moreonfew/how-to-set-proxy-using-npm-config-in-nodejs-9ng>

在使用 NodeJs 时，你会注意到很多时候你可能无法在代理网络后安装或更新，比如你办公室的公司网络代理。基本上，您可能会注意到像 **npm install 这样的命令不起作用**。然而，这很容易通过**使用配置命令**设置 NodeJs 的代理来解决。您必须为 http 和 https 代理设置代理。

在 NodeJs 中，npm 使用一个配置文件，可以使用 npm config edit 命令轻松编辑该文件。您可以使用它将代理值直接设置到文件中。或者，您也可以使用`npm config set <key> <value>`命令来设置 http-proxy 值。下面是使用 NodeJs 中的 npm 配置设置代理所需的代码:

```
npm  config  set  proxy  http://your-company-proxy.com:8080  npm  config  set  https-proxy  http://your-company-proxy.com:8080 
```

Enter fullscreen mode Exit fullscreen mode

如果您的网络需要用户名和密码，请像下面这样输入:

```
npm  config  set  https-proxy  http://username:password@your-company-proxy.com:8080 
```

Enter fullscreen mode Exit fullscreen mode

## NPM 中密码中的特殊字符如何编码？

此外，请记住，如果代理 url 中有特殊字符，您可能需要对其进行编码。特别是如果您的密码中有“@”这样的字符，您可能需要用来自 [ASCII 码](https://www.ascii-code.com/)列表的相关十六进制值来替换它。

例如，如果密码包含像“abc@xyz”这样的“@”符号，那么您必须以“abc **%40** xyz”的形式传递密码。您需要用等效的十六进制值替换特殊字符，您可以从下面的 [ASCII 码](https://www.ascii-code.com/)表中选择。您需要在表中给出的十六进制值前面加上百分号“%”。

由于特殊字符被转义，如果您的密码是“ABC**@**XYZ”:
，配置命令可能如下所示

```
npm  config  set  https-proxy  http://username:abc%40xyz@your-company-proxy.com:8080 
```

Enter fullscreen mode Exit fullscreen mode

希望本文对您有所帮助，并且您能够使用 npm 在 nodeJs 上设置代理。请在评论中告诉我们。

如何在 NodeJs 中使用 npm 配置设置代理？最早出现在 [MoreOnFew](https://www.moreonfew.com) 上。