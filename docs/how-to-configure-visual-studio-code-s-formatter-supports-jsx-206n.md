# 如何配置 Visual Studio 代码的格式化程序支持 JSX？

> 原文：<https://dev.to/wteja/how-to-configure-visual-studio-code-s-formatter-supports-jsx-206n>

与 JSX 一起工作时，你是否对 Visual Studio 代码的格式化程序感到恼火？

我之前也遇到过。我有在编码后格式化代码的习惯，以使代码更具可读性和美观性。VSCode 格式化程序在许多语言上都工作得很好，直到我在 JavaScript 文件中使用 JSX…

它可以把代码变成这样…

[![Original JSX Code](img/996e3a504697aa95879cc3a70d18264b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kUopQfpX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/57zsxelqxqrills5v1iz.png)

变成了这个丑陋的家伙…

[![Transformed JSX…](img/fccdd233b413643ba5ca2f0f04c6366f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7TRGPAGa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/di7ujyuhailryfdvx9x6.png)

## 简单解决

最简单的解决方案就是点击 Visual Studio 代码右下角的语言模式，从 **JavaScript** 改为 **JavaScript React** 。

[![Click on JavaScript Language Mode](img/13ba3f5cee2319e2615ef48e25d033fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uAHYp9rW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xef2rfdlmoiixyhcipdu.png)

[![Then choose JavaScript React Instead](img/fc85b114bc47427113043d3888c0b947.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X-pYEHIw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/d95ykhxl6m1ebmxhq2fd.png)

现在您可以工作，不用担心格式化程序会破坏您的 JSX。

但是等等…我需要在每个*都做这些步骤吗？js 文件？

不要！实际上，您可以告诉 Visual Studio 代码在默认情况下对每个 JavaScript 文件使用 **JavaScript React** 语言模式。

配置 Visual Studio 代码默认支持 JavaScript React

转到用户设置。
在 Windows 和 Linux 中进入菜单**文件>首选项>设置**T3】在 MacOS 中进入**代码>首选项>设置**

[![Go to settings](img/5958aced52920842422931c8cf5d09ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pVYrhklK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ya1iayciwcigcu1ghzfs.png)

搜索 files.associations，文件关联设置将显示为第一个结果，单击 settings.json 中的 Edit

[![Edit File Associations](img/b0eef3b3aae455ad3bc59085a1f438ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OzQdJHW4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hhhe6hc43gzlecq3gep2.png)

在左侧面板中是默认用户设置，通常为空，在右侧面板中将包含覆盖系统设置的用户设置。你的设置应该像下面的例子。

[![Original](img/0df6dca0389e54f108424ef3498d293c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lNpeZUWm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/a3thcoyv9sbxhnraeyzr.png)

您的 exists 设置可能有所不同，但我们只是添加了一个新的 files.associations 设置，让所有 JavaScript 文件默认支持 **"javascriptreact"** 语言模式。所以你只是把这些属性和价值相加:

```
{
    "files.associations": { "**.js": "javascriptreact" },
    "another-config": "xx"
} 
```

Enter fullscreen mode Exit fullscreen mode

这让我们的生活更轻松！希望这个指南对你们有帮助。编码快乐！