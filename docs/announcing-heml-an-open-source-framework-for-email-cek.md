# 宣布 HEML:电子邮件的开源框架

> 原文：<https://dev.to/sparkpost/announcing-heml-an-open-source-framework-for-email-cek>

今天，我很兴奋地宣布 [HEML、](https://heml.io/)的首次发布，这是一种用于制作干净、响应迅速的电子邮件的开源标记语言。

### 为什么

如果你不熟悉写 HTML 格式的电子邮件，这可能是一个痛苦的过程。几个月前，我开始尝试一些令人兴奋的前端交互开发。我发现瓶颈不在于发送或测试电子邮件，而是简单地构建电子邮件本身。在我们的下一次公司黑客马拉松上，我抓住机会解决了这个问题，于是 HEML 诞生了。

HEML 的每一个元素都被转换成可以发送电子邮件的 HTML 格式，这样你就可以放心地发送邮件了。HEML 还致力于消除 CSS 缺陷和不同电子邮件客户端的限制。一个很好的例子是 Lotus Notes 中的一个模糊问题，如果使用 RGB 十进制值，整个样式声明将被忽略。HEML 会帮你处理这个问题。

### 我们的目标

我们希望 HEML 帮助开发者更容易使用电子邮件。这个想法是让开发人员快速进入并创建他们的电子邮件，而不用与 Outlook(或任何其他电子邮件收件箱)纠缠不清。为此，我们专注于三件事。

**原生感受**

我们希望 HEML 的外观和感觉都像 HTML。因此，它尽可能地反映 HTML，并使用普通的 ol' CSS 样式。

**前瞻性思维**

HEML 不限制你利用 HTML 和 CSS 的所有功能。它鼓励渐进的增强。电子邮件不必在任何地方看起来都一样。

**可延伸的**

HEML 允许你创建你的自定义元素，共享它们，并引入社区制作的其他元素。

### 利用 HEML

使用 HEML 有几种不同的方法。

在[heml.io/editor](https://heml.io/editor)使用我们的编辑器快速开始。

[![](img/8d78c9641dc945ab33eae3612db3d7ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6o3emMvP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media.sparkpost.com/uploads/2017/10/ezgif-5-df93edf292.gif)

要在本地使用它，请安装:

```
npm install -g heml 
```

在`email.heml` :
中创建您的 HEML 电子邮件

```
 <heml>
  <head>
    <subject>Email Rox!</subject>
    <style>
      body { background: SkyBlue; }
      h1 { color: DarkViolet; }
    </style>
  </head>
  <body>
    <container>
      <marquee><h1>Hello world </h1></marquee>
    </container>
  </body>
</heml> 
```

并运行:

```
heml develop email.heml 
```

这将启动一个开发服务器，它会在您做出更改时自动重新加载您的浏览器。

一旦你准备好发送你的邮件，运行:

```
heml build email.heml 
```

这将生成一个准备发送的`email.html`文件。

旋转一下！

这是我们对一个难题的看法。它不能解决电子邮件带来的所有问题，但可以帮助您为独特的电子邮件挑战创建解决方案。MJML、T2 电子邮件基金会和许多其他人已经做了惊人的工作来简化这个挑战。我们希望这同样对您有所帮助！

所以试试吧！希望这能让你的生活更轻松。如果您有任何反馈、建议或错误，[请告诉我们](https://github.com/SparkPost/heml/issues)。

编码快乐！

宣布 HEML:电子邮件的开源框架的帖子[首先出现在](https://www.sparkpost.com/blog/heml-open-source/) [SparkPost](https://www.sparkpost.com) 上。