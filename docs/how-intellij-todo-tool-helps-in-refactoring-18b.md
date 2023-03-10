# IntelliJ TODO 工具如何帮助重构

> 原文：<https://dev.to/harshitrathod/how-intellij-todo-tool-helps-in-refactoring-18b>

这篇文章最初发表在我的[博客](http://harshitrathod.com/2017/12/09/how-intellij-todo-tool-helps-in-refactoring/)

每个开发人员在其职业生涯中都需要执行重构。当你觉得你的代码不可维护或不可伸缩时，你需要重构。对于复杂的设计，有时很难开发新的功能，重构是必不可少的。重构是一项非常关键的任务，因为它可能会为错误打开大门。

当我开始重构时，我做的第一件事就是端到端地分析整个代码。在我很好地理解影响之前，我不会编写代码。当我浏览代码时，我需要一种方法来给一行或方法添加注释。这就是 IntelliJ TODO 工具的用处。

### **什么是 IntelliJ TODO 工具？**

这是一个工具窗口，在这里你可以引用你在工具模式设置中提到的所有注释。IntelliJ 正在连续扫描此 TODO 模式的项目，当模式匹配时，在工具窗口上显示注释。默认情况下，IntelliJ 有两个 TODO 模式 **//TODO** 和 **//FixMe** 。这些匹配任何以 **//TODO** 和 **//FixMe** 开头的注释

要查看您所有的待办事项注释，请从**视图- >工具窗口- >待办事项**或 **(Alt + 6)** 打开待办事项工具窗口

[![TODO Tool window](img/e717543c2154e6598af5216f159b28b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aaNPBNaT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/harshitrathod.com/wp-content/uploads/2017/12/todo1.png%3Fw%3D503)

这里显示了您所有的待办事项注释。你有三个选择来限制你范围。这意味着如果您选择“项目”范围，扫描发生在所有项目文件。更多信息可以参考:[待办事项工具窗口](https://www.jetbrains.com/help/idea/todo-tool-window.html)

### **如何创建自定义待办事项模式？**

您可以在 IntelliJ 中定义自己的 TODO 模式。添加此模式后，IntelliJ 将扫描此模式，并在 IntelliJ 工具窗口中显示匹配项。进入**设置- >编辑器- >待办事项**添加新花样

[![Add TODO pattern](img/26bcd03bb674061546700596575f4807.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r419eTFz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/harshitrathod.com/wp-content/uploads/2017/12/todo2.png%3Fw%3D655)

如您所见，我们已经配置了 2 个默认模式。单击加号图标添加新模式

[![Add new TODO Pattern](img/647681c648b3dc9082923793c41a3568.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ffhrO8-9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/harshitrathod.com/wp-content/uploads/2017/12/todo3.png%3Fw%3D324)

你需要在这里定义你的模式。我已经配置了** \bhello\b.* **它将映射从 hello 开始的所有评论。您可以使用[正则表达式语法引用](https://www.jetbrains.com/help/idea/regular-expression-syntax-reference.html)来定义您的模式。您还可以更改注释的配色方案。保存更改后，你可以在 TODO 工具窗口中看到所有以 **Hello** 开头的评论

[![View custom TODO comment](img/dd825cfe8f7445021e25b5ecc445f652.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GF_Uq3f---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/harshitrathod.com/wp-content/uploads/2017/12/todo4.png%3Fw%3D505)

### **待办事宜带重构:**

现在我们知道如何添加 TODO 模式，我们可以在重构中使用它。当我开始重构时，我有一个任务的 JIRA Id。我将用这个 Id 创建一个 TODO 模式。例如，我的吉拉 Id 是 1245，所以我创建了一个类似** \b1245\b. *的模式。此后，我将开始我的代码分析，当我需要添加一些点时，我会添加一个前缀为 1245 的注释。现在我可以在 TODO 工具窗口中看到所有注释。*

在一个大项目中，你可能有太多的 TODO 注释。在这种情况下，寻找特定的评论会变得很痛苦。您可以创建 TODO 过滤器，以便只过滤特定的注释。

### **创建待办事宜过滤器:**

进入**设置- >编辑器- >待办事项**，点击加号添加滤镜。

[![Add TODO filter](img/3f4ea40ba7ec77418a7add1ec1b09d86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--N4vMDfp4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/harshitrathod.com/wp-content/uploads/2017/12/todo5.png%3Fw%3D359)

选择将包含在此过滤器中的模式并保存更改。现在在 TODO 工具窗口打开过滤器菜单。 [![](img/af4bd571430afd99c299c1c14ab1d25b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qDaus151--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/harshitrathod.com/wp-content/uploads/2017/12/todo6.png%3Fresize%3D24%252C22) 选择您的过滤器，您的所有评论将被所选模式过滤。

现在我已经过滤了我所有评论，我可以用我的代码来解决它。现在很容易通过 TODO 工具找到受影响的代码。我还可以通过 TODO 工具窗口中的一些注释来跟踪有多少工作是未完成的。

当我完成重构并提交代码后，我可以在我的**“提交前”**部分选择**“检查待办事项”**选项。它将扫描所有文件并检查是否有待办事项。您还可以应用过滤器来扫描特定模式。

[![Scan TODO while Commit](img/bdec19d5e8f38ae0281b6d0749fc7037.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h8aNa78W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/harshitrathod.com/wp-content/uploads/2017/12/todo7.png%3Fw%3D313)

### **结论:**

我在从事许多重构任务的过程中逐渐发展了这项技术。这就像创建我的重构清单来跟踪我的进展。通过系统地这样做，我们可以减少错误的可能性。

我很高兴得到一些反馈来进一步改进这项技术。

如果你真的快乐，就点赞和订阅吧！！