# 记得 Mailto 吗？

> 原文：<https://dev.to/jkirchartz/remember-mailto-4mb>

"我怎么把这个寄给我妈妈……"她问自己上下滚动页面；“…我可以发推特或者把它添加到 pinterest，”她无可奈何地叹了口气，打开一个新标签，输入“gmail.com ”,然后一路导航到撰写窗格。

当你想让用户分享你的页面时，有一点关于应该包括哪些服务的争论，但通常最初的终极应用被遗忘了，电子邮件。发送一封电子邮件并不难，但它可以更容易。它过去需要一个插件或一个外部电子邮件应用程序；如果你没有，它会打开你电脑上的任何一个电子邮件应用程序，但可能没有关联到任何电子邮件帐户，并要求你设置它。据报道，Firefox、Opera 和 Chrome 都会把你带到基于网络的电子邮件提供商那里。现在看起来 IE 又成了丑小鸭，但我总是对未来抱有很高的期望(这可能已经发生了)。

无论如何……以前我们会使用`mailto:` URI 方案让用户给我们发电子邮件(上坡！两种方式！一边打乳齿象！)我们会打字(用手！到洞壁上！)类似
的链接

```
<a href="mailto:webmaster@example.com?subject=Hello&body=I%20like%20your%20site"> 
```

Enter fullscreen mode Exit fullscreen mode

点击它会打开一封新邮件，发给`webmaster@example.com`，主题为`Hello`，正文为`I like your site`，但是 mailto 协议比这更通用，有`cc`和`bcc`以及多个收件人，用逗号分隔地址。

```
mailto:user@example.com,user2@example.com?cc=user3@example.com&bcc=user4@example.com 
```

Enter fullscreen mode Exit fullscreen mode

但前提是你知道你想给谁发电子邮件`To`这是不可能的，即使对[克里斯金](http://en.wikipedia.org/wiki/Kreskin)来说，还好没必要。协议本身就足够了，但是你应该包括一个链接，标题，也许是署名或者简介。不过要小心，这是一个 URI，所以它通常必须是 URI 编码的，比如:`%20`代表空格，`%3F`代表问号，等等。

```
mailto:?subject=I%20thought%20you%20would%20like%20this&body=http://example.com/some/page 
```

Enter fullscreen mode Exit fullscreen mode

URIs 已经 URI 编码，所以你不必做任何特殊的。您可能希望在服务器端或通过 JavaScript 生成此代码，因为在某些版本的 iPhone 上，编码可能会出现在邮件应用程序中，并且很难看。

## 链接:

*   [RFC-6068](http://www.ietf.org/rfc/rfc6068.txt)
*   [邮件生成器](http://www.mailto.co.uk/)

附:如果你喜欢这篇文章，你可以给我发邮件；)

*本帖最初发表于[jkirchartz.com](http://jkirchartz.com/2016/06/remember_mailto.html)T3】*