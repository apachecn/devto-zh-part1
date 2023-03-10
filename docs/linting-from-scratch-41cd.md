# 林挺从零开始

> 原文：<https://dev.to/sadick/linting-from-scratch-41cd>

#### 了解如何为您的工作空间设置林挺(javascript)

<figure>[![](img/2f2111362d6a3377d0a2f2926b602a9f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IAUtZK3M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A6HfeoBfBHEgO7b5J6InJRA.jpeg) 

<figcaption>eslint 图像</figcaption>

</figure>

作为软件工程师，我们问自己“我如何提高我的代码质量？”。你可以做一些事情来提高你的代码质量，其中之一就是**林挺。**

**林挺**是运行一个程序来分析代码中常见错误的过程(**潜在错误**)。在 javascript 上下文中，常见的错误可能是:

*   行尾缺少分号。
*   花括号中没有一个 *if* ，*为*， *while* 等。
*   因*返回*、*抛出*、*继续*、*或中断*而从未运行的代码。
*   开关中没有 *break* 语句的 Case 语句。
*   数字的前导和尾随小数点。
*   将数字转换成八进制(以 8 为基数)的前导零。
*   评论中的评论。
*   两个相邻的行是否是同一语句的一部分。
*   没有任何作用的声明。

### **在工作区设置林挺。**

现在是 2016 年，围绕 javascript 有很多工具。对于一个新手来说，这可能会让人不知所措，所以我会尽可能保持简单。

确保您的系统上安装了 Nodejs。一个简单的方法是通过您的终端来检查它是否已安装。键入 node - version，您应该会看到安装的节点的版本。

您将需要一个程序来帮助您下载要在项目中使用的包。您可以使用 node 附带的默认 npm，但我更喜欢 yarn。安装纱线很简单。键入 npm install yarn -g，这将在全局范围内安装 yarn，这样您就可以在系统的任何地方使用它。

创建一个文件夹，随便你怎么命名(我已经给我的 app 命名了)。这将是您项目的根目录。让我们通过键入 yarn init 来初始化我们的应用程序。它会提示你一堆问题，填好再继续。在您的根目录下，您应该有一个如下所示的 package.json 文件。