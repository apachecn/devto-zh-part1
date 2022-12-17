# 在纯文本中运行 JavaScript 代码

> 原文：<https://dev.to/rafnixg/ejecutar-codigo-javascript-en-sublime-text-4k6l>

[![Ejecutar Código JavaScript en Sublime Text](img/b9aded23d2257c51dc14b4e8265ac7b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bzqjJnrN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1572621403925-33e05716ad8d%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D2000%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

当我们开始使用 JavaScript 进行开发时，其中一个不太好的工作流程(更不用说最麻烦的工作流程)是，您必须在我们最喜欢的代码编辑器中编写代码，然后前往浏览器控制台进行测试。

为了解决这个问题，有很多方法，我在此提出的是最适合我需要的方法，我希望你能帮助你，对于所有使用*崇高文字*的人来说，并保持这种工作流程，以崇高的方式创建代码，然后将其传递给浏览器，并在浏览器控制台中进行调试我给你这个

一开始我们必须安装*【high text】*，安装起来非常简单，网站上有很多相关文件，第二件要安装的是*【node . js】*，

如果您尚未安装 *Node.js* ，我将在此向您提供官方文档，您可以在其中找到如何在您使用的任何操作系统上安装它的指南，[安装手册](https://nodejs.org/es/download/package-manager/)

#### 配置崇高

安装并运行 high text 和 Node.js 后，我们将对 high text 做一个小设置，为此，我们输入>>>>，将打开一个包含以下内容的新文档:

> {
> 
> " cmd ":[" make "]
> 
> }

该内容应改为:

> {
> 
> "cmd": ["node "，" $file"]，
> 
> "选择器":" source.js"
> 
> }

完成此操作后，我们将该文件保存到 superscript text 建议的路径中，为了证明该文件可以正常工作，我们只需创建一个包含所需 JavaScript 代码的新 JS 文件，我仅建议进行以下测试:

> console . log(" JavaScript funcionando desde Sublime Text ")

以您希望的名称保存新文件(在我的情况下为“t0”→“t1”)，并且您必须转到“T2”>或运行以下键组合**+**，才能运行该文件

#### 建议

对于 JavaScript 世界中刚起步的用户，我建议您阅读一些 Node.js 以及它可能具有的强大功能，因为此帖子没有看到 node . js 的强大功能。

安装上标文本代码片段和代码检查器，以提高工作效率并了解可能出现错误的位置。