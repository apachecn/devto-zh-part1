# Cattaz:带有 markdown 和应用程序的 Wiki 协作工具

> 原文：<https://dev.to/yoheiseki/cattaz-wiki-collaboration-tool-with-markdown-and-applications-b1a>

我们开发了**“Cattaz”**，与 markdown 和应用程序自由、公开地合作。

Cattaz 是 OSS(开源软件)。

[Cattaz](http://cattaz.io/)

[GitHub - Cattaz](https://github.com/FujitsuLaboratories/cattaz)

这是一个不断发展的 Wiki，可以使用为 Markdown 团队的活动定制的应用程序来自由地启动、组织、积累和共享知识。

# 特性

### 编辑降价

CommonMark，降价的标准化，可用于描述。

使用 Markdown，使文本保持一种易于格式化的状态。

复制粘贴文本，不需要在 Cattaz 和其他 Markdown 编辑器之间转换。

编辑文本时，您可以实时看到预览。

### 运行应用程序和双向编辑

通过在 fenced 代码块(以 3 个反勾号开始的代码块)中指定应用程序名称，在预览屏幕上启动应用程序。

Markdown 使从应用程序的 UI 输入复杂信息变得容易。

此外，根据应用程序的用户界面，以用户友好的形式显示信息。

将多个应用程序放在一个页面上，应用程序中输入的信息可以立即反映在编辑器中，并可以保存为一个页面。

在 Cattaz 中，将所有内容都放在一个页面上，团队可以顺利地共同创建活动。

[![Cattaz: Run Applications](img/5d3748e9db982588ea2a7b3f75ebb938.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z4bKBVvm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0tq5undz9s3nyrvgzgki.png)

### 轻松开发应用

工程师可以开发和实现在预览屏幕上运行的应用程序，就像创建 web 应用程序一样。

使用 JavaScript 和 React 库编写应用程序。

通过处理指定的 propTypes，可以实现一个添加了同步功能的应用程序。

查看[“Hello world”](http://cattaz.io/build/#/doc/app-hello)应用的开发者指南，开始开发自己的应用。

### 实时同步编辑

Markdown 文本和应用程序可以由多人同时实时协作编辑，因此您可以高效地共享和共同创建信息。

Cattaz 也是一个强大的工具，当与远程位置的人协作时，例如远程会议。

# 工作原理

从 Markdown 启动预览应用程序的过程示例如下。

1.  将 **Markdown** 转换为 **MDAST (Markdown 抽象语法树)**
2.  将 **MDAST** 转换为 **HAST(超文本抽象语法树)**
3.  将 **HAST** 转换为 **HAST 进行应用(自定义 HAST)** ，转换为 **React 元素**，在预览中绘制
    *   基本上，HTML 元素被转换成 React 元素，无需修改
    *   在防护代码块的情况下，切换到与语言规范相对应的应用程序的反应组件
    *   对于未指定语言规范的隔离代码块，保留 HTML 的“pre”和“code”

[![Cattaz: Conversion processing](img/0205f2354f486cd09344b662f23690e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nLrnemW1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rsn7oucxko2g3ucwav2c.png)

# 用例

*   会议
    *   分钟
    *   远程会议
*   敏捷开发
    *   Sprint 的计划
    *   回顾
        *   看板应用程序
        *   KPT 应用程序
*   事件
    *   黑客马拉松
    *   思维能力

# 结论

起初，我们认为工程师们应该有一个活跃、自由、开放的平台。

随着许多工具、各种工作方式的出现，掌握这些工具的方法以及如何分享信息变得越来越复杂。

虽然自由增加了，但却越来越混乱。

因此，我们开发了“Cattaz ”,在一个地方分享一切，最大限度地发挥工程师的能力，使他们能够开展有趣的活动。

此外，我们的目标是使 Cattaz 成为一个自由和充满活力的地方，面向各种职业、不同文化的人，而不仅仅局限于工程师。