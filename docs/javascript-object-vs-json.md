# JavaScript 对象与 JSON

> 原文：<https://dev.to/taiwo_xyz/javascript-object-vs-json>

你好，欢迎再次光临。今天我将谈论 JavaScript 对象和 JSON。在阅读本文之前，我假设你已经熟悉了网络工作的一些基础知识——比如用 HTML 构建在线项目。您还应该熟悉 JavaScript 基础知识。
JavaScript 和 JSON 有时真的会让一些人感到困惑，这就是为什么我写这个来回答你所有可能的问题。
JSON 代表 JavaScript 对象符号。它基本上是一种文本格式，便于在客户机和服务器等设备之间共享数据。它的起源是基于 JavaScript object 的工作方式，因此在这个意义上，它与 JavaScript Object 相关/接近，但不完全相同。尽管它起源于 JavaScript，但它被广泛应用于许多语言，如 C、Ruby、Python、PHP 等等。由于它的大小和转换成数据结构的容易程度，它是 XML 等其他格式的一个很好的替代。
使用 JSON 的一个很好的优势是它很容易阅读。

[https://thepractical dev . S3 . Amazon AWS . com/I/4 mai HR 1k pvv 86 c 9y S6 cc . jpg](https://thepracticaldev.s3.amazonaws.com/i/4maihr1kpvv86c9ys6cc.jpg)

考虑上面的片段。我已经创建了一个带有几个键值对的 JSON 对象。关键字在左侧(姓名、语言和爱好)，而关键字的值在右侧。这很容易被 JavaScript 和任何阅读它的人理解为一个对象。JSON 的另一个优点是能够将值或数据传递给 JavaScript 对象。这可以简单地使用如下所示的 JavaScript 命令来完成。

[https://thepractical dev . S3 . Amazon AWS . com/I/4 au er2 ao 0s 2n 4 xctiikh . jpg](https://thepracticaldev.s3.amazonaws.com/i/4auer2ao0s2n4xctiikh.jpg)

使用这一行命令，您可以访问对象数据中的任何内容。因此，假设您要获取名称值，您所要做的只是简单地键入:
userinfo . name；//使用点符号。
userInfo["name"] //使用括号符号
另一个好处是它比 XML 更精简。当您尝试使用 XML 运行相同的命令或脚本时，您会发现传递 XML 可能会很耗时，而使用 JSON 会快得多。
我们来看看 JSON 字符串是怎么写的。

[https://thepractical dev . S3 . Amazon AWS . com/I/p 09 VP 0 pz 1 r 2 x 9 teo 9t 6d . jpg](https://thepracticaldev.s3.amazonaws.com/i/p09vp0pz1r2x9teo9t6d.jpg)

好好看看这些钥匙。你会注意到它们是用引号括起来的。密钥也可以是任何有效的字符串。JSON 值只能是六种数据类型之一(字符串、数字、对象、数组、布尔值、null)。另一方面，JavaScript 值可以是任何有效的 JavaScript 结构。

[https://thepractical dev . S3 . Amazon AWS . com/I/u 245 wunwrhecozexd0t . jpg](https://thepracticaldev.s3.amazonaws.com/i/u245wunnwrhecozexd0t.jpg)

在上面的代码片段中，您会注意到键没有用引号括起来。这是一个 JavaScript 对象的典型例子。JavaScript 对象值可以是任何数据类型，包括函数(这是 JSON 无法做到的。请看下面的代码片段，它显示了一个有效的 JavaScript 对象。

[https://thepractical dev . S3 . Amazon AWS . com/I/9cp 24 lyb 34 nmoqyl 4 yuu . jpg](https://thepracticaldev.s3.amazonaws.com/i/9cp24lyb34nmoqyl4yuu.jpg)

与 JavaScript 对象不同，JSON 对象必须作为字符串输入变量，然后解析成 JavaScript。像 jQuery 这样的框架在进行解析时非常有用。

[https://thepractical dev . S3 . Amazon AWS . com/I/80 qhkpbkrowz 5 POV 46 ij . jpg](https://thepracticaldev.s3.amazonaws.com/i/80qhkpbkrowz5pov46ij.jpg)

有很多工具可以用来验证你的 JSON 代码，下面列出了一些:
JSON lite
JSON Editor Online
Cocoa JSON Editor(MAC)
JSON pad
JSON view for Mozilla and Chrome 等等。
请继续关注关于使用 JavaScript 对象和 JSON 的更多内容。
谢谢。