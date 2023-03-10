# 用 Python 平息您的抑制列表焦虑

> 原文：<https://dev.to/sparkpost/calm-your-suppression-list-anxieties-with-python-d92>

### “建立好名声需要许多善行，失去好名声只需要一件坏事。”

本杰明·富兰克林

从我们便利的[入门指南](https://www.sparkpost.com/docs/getting-started/getting-started-sparkpost/#important-coming-from-other-email-services)中，你知道从你的老供应商那里带着你的抑制列表有多重要。Ben Franklin 是对的——如果你向陈旧的、退订的、被退回的地址发送邮件，你的[电子邮件声誉](https://www.sparkpost.com/blog/email-suppression/)将会受到严重影响。这影响到你给真实订户的信息是否被接受，无论是现在还是将来，所以最好听从医生的建议。

在本文中，我们将设置一个易于使用的工具来管理您的禁止列表。如果你想知道更多关于抑制的“是什么”和“为什么”的背景知识，这篇文章是一个很好的起点。

### 擦洗起来

我们看到的来自旧提供商的输出抑制列表通常是不干净的。重复条目、带有多个@符号的无效条目、无效字符、电话号码而不是电子邮件地址，等等。我们在各种晦涩难懂的国际字母表中看到过带有奇怪字符的文件。这可能会让你考虑砍掉这些列表，但我们会探索尽可能多地保留它们的技术。

列表可以很大，达到近百万个条目。手动处理小块将会花费很长时间。我们需要一个机器人外科医生！

### 计划治疗

让我们列出我们的需求，并将其转化为设计目标。

*   让上手容易，使用简单。
*   尽最大努力理解您的文件格式，即使它似乎包含奇怪的字符。
*   无需人工操作即可检查和上传任意大小的列表。
*   预先检查输入文件，并在进行过程中给出有用的警告。
*   检查应该彻底而快速。如果有错误，我们想知道它们在文件中的确切位置，以及错误在哪里。具体来说，我们需要:
    *   确保电子邮件地址格式正确(即遵循 RFC)
    *   检查其他字段值，如 transactional/non _ transactional 标志。
*   一种“检查一切但不改变任何东西”的模式，使查找和修复错误的输入数据变得容易。
*   允许从 SparkPost 中检索整个禁止列表，或者选择有时间限制的部分。
    *   了解时区，同时接受当地的时间。特别要记住，开始时间和结束时间可能在夏令时变化的任何一边。
    *   保持简单。该 API 支持按域、来源、类型、描述等进行搜索——然而，这可以通过事后过滤检索到的文件来完成。如果你想要这些特性，[在 Git 仓库上提出一个问题](https://github.com/tuck1s/sparkySuppress/issues/new),我们会研究它。
*   跨主帐户和子帐户工作。
*   使为缺失/可选文件信息提供默认值变得容易。

这引导我们制作一个具有以下选项的工具:

*   **检查**文件的格式(导入前)。带上你的压抑总是个好主意。
*   **在 SparkPost 中更新**您的禁止列表(即，如果您的列表当前为空，则创建)。
*   **从 SparkPost 中检索**您的禁止列表，例如，如果您想将禁止列表放回您的上游活动管理工具中。
*   **从 SparkPost 中删除**你的抑制列表，即清除你的抑制列表。也许你误上传了一些条目。我们希望这是一个罕见的用例，但它是为你准备的。

### 运行时间

[sparkySuppress](https://github.com/tuck1s/sparkySuppress) 是一个用 Python 编写的工具，帮助你管理你的抑制列表。 [Github repo](https://github.com/tuck1s/sparkySuppress) 包含一个全面的自述文件。如果你需要的话，这里有一些关于安装 Python 3 的帮助。

您可以使用`sparkpost.ini`文件配置 sparkySuppress，该文件用于设置您不经常更改的内容，比如您的 API 键、时区、批处理大小等等。如果您愿意，可以将除 API 键之外的所有内容都设置为默认值。

我们使用优秀的 [email_validator](https://github.com/JoshData/python-email-validator) 库检查输入文件中的电子邮件地址。我们用它来给出错误地址的综合报告，例如:

```
Line 2 ! bad@email@address.com The email address is not valid. It must have exactly one @-sign.
Line 3 ! invalid.email@~{}gmail.com The domain name ~{}gmail.com contains invalid characters (Codepoint U+007E not allowed at position 1 in '~{}gmail.com'). 
```

Enter fullscreen mode Exit fullscreen mode

的！将条目标记为有错误。我们将用这样的警告 w 标记有可恢复问题的条目:

```
Line 1 w need valid transactional & non_transactional flags: {'recipient': 'test.recip@gmail.com'} 
```

Enter fullscreen mode Exit fullscreen mode

### 一个优秀的人物

文本文件并不像看起来那么简单！不寻常的文件字符编码可能是一个障碍，尤其是当您无法控制禁止列表导出最初是如何创建的时候。

UTF-8 是最现代、最强大的编码，但有些系统可能不使用它。例如，从一些旧版本的 Excel 导出的输出文件将是拉丁语-1，而不是 UTF 语-8。

`sparkpost.ini`中的`FileCharacterEncodings`设置提供了一种简单的方法来控制输入文件的处理方式。该工具依次使用每种编码读取您的文件，如果发现异常，将尝试下一种编码，依此类推。所以如果你有:

```
FileCharacterEncodings=utf-8,utf-16,ascii,latin-1 
```

Enter fullscreen mode Exit fullscreen mode

您将看到该工具尝试每种编码方式，直到找到一种能够正确读取整个文件的编码方式。您可以在此处选择[所示的任何标准编码。](https://docs.python.org/3.6/library/codecs.html#standard-encodings) 

```
$ ./sparkySuppress.py check klist-1.csv
Trying file klist-1.csv with encoding: utf-8
        Near line 1125 'utf-8' codec can't decode byte 0x9a in position 7198: invalid start byte
Trying file klist-1.csv with encoding: utf-16
        Near line 1 UTF-16 stream does not start with BOM
Trying file klist-1.csv with encoding: ascii
        Near line 1125 'ascii' codec can't decode byte 0x9a in position 7198: ordinal not in range(128)
Trying file klist-1.csv with encoding: latin-1
        File reads OK.

Lines in file: 8496 
```

Enter fullscreen mode Exit fullscreen mode

将条目从 SparkPost 检索回文件时，会使用列表中的第一个编码。

### 表现良好

Delete 有点特殊——它使用多线程，因为每次调用都要删除一次。单线程时更新和检索工作速度很快，因为每个调用处理一个批处理。使用默认的批处理大小和线程设置，您应该会体验到良好的性能，但是如果需要，您可以调整它们。

### 行医

如果您还没有来自旧提供商的数据，这里有一个用于创建抑制列表的[工具](https://www.sparkpost.com/blog/recipient-suppression-lists-python/)，您可以使用它来创建一个虚拟文件进行练习。

差不多就是这样！你现在是一个熟练的抑制列表外科医生。你很快就会在竞选活动中表现出色。

### 最后…

如果你正在探索这个工具，并想给作者反馈，欢迎你访问我们的[社区 Slack 频道](http://slack.sparkpost.com)——有一个专门针对 Python 的频道，#python。或者，打开一个 Github 项目[发布](https://github.com/tuck1s/sparkySuppress/issues/new)或[拉取请求](https://github.com/tuck1s/sparkySuppress/compare)。

如果你不喜欢 Python (whut？)有一些较低级别的命令行 SparkPost 项目在 API 上提供了一个很薄的“包装器”,可以用来操作禁止列表。查看 [Node.js](https://github.com/SparkPost/node-sparkpost-cli) 和 [Go](https://github.com/SparkPost/sparkpost-cli) ，如果你想了解更多关于抑制列表的 API 和 UI，[这里是一个开始](https://www.sparkpost.com/blog/how-to-view-and-validate-your-suppression-lists/)的好地方。还有一个 [node.js 工具](https://www.sparkpost.com/docs/tech-resources/download-suppression-list/)可以再次从 SparkPost 检索你的列表以供检查。

如果你喜欢点击，SparkPost 用户界面有一个内置的[列表/抑制](https://app.sparkpost.com/lists/suppressions)上传功能。这为您提供了一个很好的示例模板，并且当您有格式完美的不太大的文件时非常理想，每个文件最多有 10，000 个收件人。

用 Python 平息你的抑制列表焦虑的帖子[首先出现在](https://www.sparkpost.com/blog/suppression-list-python/)[的 SparkPost](https://www.sparkpost.com) 上。