# 帮助用户使用内置的微软工具自助

> 原文：<https://dev.to/briannipper/help-users-help-themselves-with-built-in-microsoft-tools-5ama>

感谢 Scott Hanselman 在他题为[的博客文章中提醒我这个非常有用的工具，用问题步骤记录器](http://www.hanselman.com/blog/HelpYourUsersRecordAndReportBugsWithTheProblemStepsRecorder.aspx)帮助你的用户记录和报告错误。以下是 Hanselman 先生博客中的相同亮点，以及我自己对该工具其他特性的一些注释。

当你有技术倾向时，你会从朋友和家人那里得到很多“支持请求”。甚至在工作中，各种各样的大学可能会出现他们有问题的情况，并希望你帮助解决问题。然而，经常出现的情况是，问题是“无回购”的，或者说是不可重现的，这在许多错误报告中经常列在各种项目的旁边。在 Windows 7 中，微软提供了一个很好的实用程序，希望它可以让你这个专业的极客更容易地让用户帮助自己，为你提供重现问题所需的信息。

这个神奇工具的名字是**问题步骤记录器**(从 Windows 7 开始提供)；这个小东西对于任何专业水平的用户来说都非常简单。UI 只有三个按钮，开始记录，停止记录和添加评论。我要提到的是，有一些设置与帧捕获和放置文件的位置有关，但默认的 25 帧似乎足够了，一旦用户单击停止记录，就会提示他们想在哪里保存结果，所以我认为大多数用户不需要访问这些设置。

获得该工具的最短途径是通过开始菜单的任何输入步骤或 PSR。额外的方法是，开始菜单->控制面板->故障诊断->从朋友那里获得帮助(屏幕左侧)->问题步骤记录器(屏幕底部)

| [![](img/daaeda15251d907bd87afe39fcdf0306.png) ](http://1.bp.blogspot.com/-_iVOFvUAHJw/UHbqEJeKFuI/AAAAAAAAGdU/-jV5JLkutTs/s1600/problem_steps_recorder.png) |
|问题步骤记录器

从 Windows 7 开始提供|

我相信前两个按钮不言自明，第三个按钮，当按下开始记录后，允许用户在当前屏幕上创建一个突出显示的矩形区域，并添加将添加到最终结果中的评论。

结果是一个 MHT 文件。MHT 是 MIME HTML 的缩写，它本质上允许一个文件包含网页的文字和图像，当然还有布局。然后，这被打包成一个 ZIP 文件以便于传输，因为 MHT 将包括以文本形式表示的屏幕截图。文件站点可能会非常大。

用户按下 Stop Record 后，可以使用问号旁边的下拉箭头，发送一封附有结果 ZIP 文件的电子邮件，当然这取决于用户是否在操作系统上安装了电子邮件客户端。

结果包含了相当多有用的信息，最直接的是屏幕截图，上面标注了用户采取的行动以及订单，这甚至可以被视为幻灯片。另一个有用的特性是报告的“附加细节”部分，它显示了具体的程序信息和更多关于正在交互的 UI 元素的细节。

总之，PSR 工具可以被看作是一个简单的基于用户点击的屏幕截图，以及添加符号和详细结果供以后查看的能力。

附加资源:

[http://windows . Microsoft . com/en-US/windows 7/How-do-I-use-Problem-Steps-Recorder](http://windows.microsoft.com/en-US/windows7/How-do-I-use-Problem-Steps-Recorder)