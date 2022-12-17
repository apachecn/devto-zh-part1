# 在 R 中读写 CSV

> 原文：<https://dev.to/ladvien/read-and-write-csvs-in-r-2c0o>

让我们使用 HMIS 数据，但是仍然有一个问题，那就是如何将数据从一个数据源加载到 R 中。关于数据导入，r 实际上非常简洁。我们可以从网站、SQL 数据库、文本文件、Excel 文件或 CSV 加载数据。

处理 HMIS 数据时，两种最常见的数据源类型是 Excel 和 CSV。因此，花一点时间来研究如何将这些文件导入 r 是值得的。

## 将 CSV 导入 R

CSV 代表逗号分隔值格式。这是一种近乎古老的文件格式，用于按行和列存储数据。如果您的计算机上有一个 CSV 文件(在 Windows 中),请右键单击它并在记事本中打开它。内容应该是这样的:

```
PersonalID,FirstName,LastName
B7YIOJIGF9CDP6FV7TANQXLMQRMBTVTB,Bob,Person
ASGJ4F95HS85N39DJ12AJB094M59DJ45,Jane,People 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果您在 Excel 中打开同一个 CSV，它看起来会像这样:

| PersonalID | 西方人名的第一个字 | 姓 |
| --- | --- | --- |
| b 7 yiojigf 9 CDP 6 Fv 7 tanqxlmqrmmbtvtb | 上下移动 | 人 |
| asgj 4 f 95 hs 85n 39 DJ 12 ajb 094m 59 DJ 45 | 简（女子名） | 人 |

老实说，第二个例子对人类来说更容易理解(除非你是个古怪的人)。在大多数情况下，我们将在 Excel 或 RStudio 的 dataview 中查看 CSV，它看起来与 Excel 相似。**然而**需要注意的是，更易于阅读的版本可以隐藏数据错误，这些错误只有通过查看原始 CSV 才能看到。现在没什么好担心的，但要记住。

好了，让我们探索一下如何将一个 CSV 文件加载到 r 中。有许多方法可以做到这一点，但是让我们从一个超级简单的方法开始:

```
pathToCsv  <-  file.choose()  myCsvAsADataFrame  <-  read.csv(pathToCsv) 
```

Enter fullscreen mode Exit fullscreen mode

这两个命令在执行时会强制 R 创建一个文件选择框。这将允许我们轻松地选择 CSV 加载到 R 中。一旦选择并按下`Ok`，R 就会将所选文件的路径加载到变量`pathToCsv`中。

下一个命令`read.csv()`采用提供的路径，试图加载它指向的文件并将其转换成数据帧。一旦 R 从选择的文件创建了一个数据帧，它就把它保存在变量`myCsvAsADataFrame`中

仅此而已。数据现在被加载到 R 中，可以用其他命令操作它。

## 编写 CSV

从 R 中保存数据是导入数据的另一端。这个过程通常被称为导出数据。实际上，它只是简单地将一个 R 数据帧转换成一个文件——一旦以文件形式存在，它就可以加载到 Excel 中或通过电子邮件发送给同事(但是，请不要通过电子邮件发送个人信息——记住，CSV 只是文本)。

使用`write.csv()`将数据写入 CSV。

例如:

```
write.csv(theDataFrameToWrite,  "NameOfFile.csv") 
```

Enter fullscreen mode Exit fullscreen mode

就这样，很简单，是吧？嗯，有几个陷阱。当 R 保存一个 CSV 文件的时候，它做了一些事情，这些事情对于以其他方式使用数据是很烦人的。例如，假设我们在 R 中有这样的数据:

| 个人 id | 名字 | severely subnormal 智力严重逊常 |
| --- | --- | --- |
| 123 jjkldfwe 234234 jgd 0238d 2342346 |  | 123-45-6589 |
| b 7 yiojigf 9 CDP 6 Fv 7 tanqxlmqrmmbtvtb | 鲍勃·波弟 |  |
| dsk 329 GJB 9234j 5 jfsdf 94056 ndunvdf | 弗兰克 | 123-99-9999 |

但是，在将其写入文件后，它将看起来像这样:

| 个人 id | 名字 | severely subnormal 智力严重逊常 |
| --- | --- | --- |
| 123 jjkldfwe 234234 jgd 0238d 2342346 | **娜** | 123-45-6589 |
| b 7 yiojigf 9 CDP 6 Fv 7 tanqxlmqrmmbtvtb | 鲍勃·波弟 | **娜** |
| dsk 329 GJB 9234j 5 jfsdf 94056 ndunvdf | 弗兰克 | 123-99-9999 |

注意增加的`NA`。将安娜放在没有数据的地方是一种很好的做法。不幸的是，在处理 HMIS 数据集时，标准是留下空白。为了让 R 符合这个标准，我们在`write.csv()`函数中使用了一个选项。

例如:

```
write.csv(theDataFrameToWrite,  "NameOfFile.csv",  na="") 
```

Enter fullscreen mode Exit fullscreen mode

`na=""`告诉 R 写文档，不要把空白变成`NA`。上面代码的结果应该类似于:

| 个人 id | 名字 | severely subnormal 智力严重逊常 |
| --- | --- | --- |
| 123 jjkldfwe 234234 jgd 0238d 2342346 |  | 123-45-6589 |
| b 7 yiojigf 9 CDP 6 Fv 7 tanqxlmqrmmbtvtb | 鲍勃·波弟 |  |
| dsk 329 GJB 9234j 5 jfsdf 94056 ndunvdf | 弗兰克 | 123-99-9999 |