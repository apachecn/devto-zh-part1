# MS Access 的巧妙之处

> 原文：<https://dev.to/funkysi1701/clever-things-with-ms-access-40e>

我讨厌 MS Access，尤其是用它开发，因为你不能用它做任何聪明的事情。

**不对，不对，不对！**

我已经编写了一些聪明的东西，使用它进行开发变得可行。我仍然更愿意使用 Visual Studio，但这也大大改善了我的体验。

## 源代码控制

Access 文件是二进制的(我使用 ADP/ADE 文件格式，但我假设其他 Access 文件格式也有同样的问题)，所以你无法区分它们来查看发生了什么变化。这很糟糕。

然而，有一个解决方案。一个叫做 Access SVN 的工具，可以从 https://accesssvn.codeplex.com/下载，这给了你一个将 Access 中的所有表格和报告提取到文本文件的方法。在每次提交之前，我会在我的 ADP 文件上手动运行这个工具，并提取到文本文件，然后我会将这些文本文件提交到源代码控制，并可以很容易地看到每次提交中发生了什么变化。

尽管名为 Access SVN，这个工具并不依赖于 subversion，你可以使用任何源代码控制系统，我使用的是 git。

这个工具中还包含了一种使用命令行来实现这一点的方法，因此您可以在您的构建服务器上将它作为一个构建步骤。我还没有广泛使用它，但是语法相当简单

```
asvn.exe e "path to Access file" "path to txt files" "*.*" 
```

Enter fullscreen mode Exit fullscreen mode

最后的过滤器*。*允许您指定要提取的内容，以便您可以提取以 D 开头并带有“*”的所有表格/报告。D* ”。我在使用*时遇到了麻烦。*因为我的表单/报表的名称包含 windows 文件名中不允许的字符。我确信有办法解决这个问题，但我还没有机会深入研究它。

## 测试 MS 访问

使用 MS Access 肯定无法进行测试吗？前几天我可能会同意这种说法，直到我发现了一种测试一个特性是否被启用的简洁方法。

首先是一些背景知识。我使用 MS Access 2003 进行开发，因为设计视图更容易使用，但是因为它不受支持，我的所有用户都使用 MS Access 2010。MS Access 2010 有一个名为选项卡式文档的功能，它允许所有表单和报表在新的选项卡中打开，因此您可以轻松地在它们之间切换。此功能只能在 MS Access 2010 中启用，如果用 MS Access 2003 打开则不起作用。

如果您在 Access 文件上使用 Access SVN，并打开和关闭选项卡式文档，您将看到数据库属性文件中显示 UseMDIMode: 0 和 UseMDIMode: 1。UseMDIMode: 0 表示选项卡式文档已打开。

在 powershell 中，我现在可以编写一个测试来查看 UseMDIMode: 0 是否可以在数据库属性文件
中找到

```
Get-Content "General\Database properties.dbp.txt" | Select-String "UseMDIMode: 0" -quiet 
```

Enter fullscreen mode Exit fullscreen mode

如果测试通过，将返回 True，如果测试失败，将返回 null。

在我的构建服务器上，在运行这个测试之前，我用 asvn.exe 编写了从 ADP 文件中提取 Database properties.dbp.txt 的脚本。虽然并不严格要求数据库 properties.dbp.txt 应该在源代码控制中，但是有人可能会忘记从 ADP 中提取文本文件，在这一步中，您总是在测试二进制文件中启用了什么。

## MS 访问连接字符串

在使用 MS Access 进行开发时，我经常交换数据库连接以指向我的本地机器或构建服务器。我总是试着记住只将这个集合提交给实时数据库，以避免明显的问题。

前几天，我在 stack overflow 上发现了一种编写这个脚本的方法。我喜欢这个！我可以在我的部署过程中包括这一步，它将使用您的生产环境需要的内容来覆盖源代码控制中的连接字符串。

运行这一步所需要的是，(注意参数之间是空格而不是逗号)

```
cscript connect.vbs Project.adp "ServerName" "DatabaseName" 
```

Enter fullscreen mode Exit fullscreen mode

connect.vbs 的内容可以在 [stackoverflow 文章](https://stackoverflow.com/questions/16411430/change-access-server-connection-from-command-line)中找到。如果您的环境需要，也可以传递用户名和密码。

## 编制的 ADE

我用 MS Access 做的最后一件聪明的事情是将我的 ADP 文件转换成编译后的 ADE 版本。要手动执行此操作，工具菜单中有一个选项。

为了实现自动化，我运行了

```
cscript createADE.vbs "path to ADP" "path to ADE" 
```

Enter fullscreen mode Exit fullscreen mode

createADE 的内容来自这个[论坛帖子](https://social.msdn.microsoft.com/Forums/office/en-US/01fd48a9-258e-4405-86f1-adfb2f1057ee/create-an-access-2007-ade-from-a-adp-via-commandline?forum=accessdev)，我所做的唯一改变是注释掉一些 echo 语句，这样它将作为我构建过程的一部分安静地运行。应该注意，cscript 和 wscript 几乎是相同的，两者都可以运行这些脚本，但是在命令行环境中 cscript 更好，而 wscript 应该在 windows 环境中使用。

我很惊讶自己在编写 MS Access 的构建和部署过程脚本方面做了多少工作。我仍然不喜欢用 Access 进行开发，但是这确实改善了一些事情。