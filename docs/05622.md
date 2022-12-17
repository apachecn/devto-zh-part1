# HMIS，R，SQL -工作挑战四

> 原文：<https://dev.to/ladvien/hmis-r-sql-work-challenge-four-395l>

# 创建可重用代码

编写可重用的报告代码对于成为一名高效的报告专家至关重要。到目前为止，希望您已经看到了 SQL-R 的威力，尤其是在 HMIS 数据方面。但是你可能还是会觉得慢。或者有这样的想法，“如果我将这些数据放入 Excel，我可以用 1/10 的时间手动过滤它们。”那可能是真的。但是，在手动过滤一个又一个数据集之后，很明显，从长远来看，找到一种自动执行某些任务的方法可以节省很多时间。因此，为日常工作编写一个 R 脚本可以节省大量单调的时间。

然而，还有一个问题，每个任务通常会与之前的任务有轻微的差异。这将导致您为当前项目编写 95%的相同代码，并稍作调整。这一点也不节省时间。在编程界，95%相同的代码被称为[模板代码](https://en.wikipedia.org/wiki/Boilerplate_code)。

好吧，这就是问题所在。解决办法？功能。

函数只不过是你保存到变量中以便重用的一段代码。

定义一个函数是这样的:

```
myNewFunction  <-  function(){  # Code you want to run goes here.  } 
```

Enter fullscreen mode Exit fullscreen mode

然后，每当你想使用这段代码时，可以这样调用它:

```
myNewFunction() 
```

Enter fullscreen mode Exit fullscreen mode

如果你想通过函数来使用:

```
 myNewFunction  <-  function(clientDf){  clientDf$VeteranStatus  }  clientDf  <-  read.csv(clientCsvPath)  myNewFunction(clientDf) 
```

Enter fullscreen mode Exit fullscreen mode

关于函数最酷的事情是能够`return`数据。函数返回函数最后一行的所有数据。这可能是一个棘手的概念，但从根本上来说，它是简单的。

在这里，`clientDf`将被返回。

```
myNewFunction  <-  function(clientDf){  clientDf$VeteranStatus[clientDf$VeteranStatus  ==  "1"]  clientDf  }  clientDf  <-  read.csv(clientCsvPath)  veteranList  <-  myNewFunction(clientDf) 
```

Enter fullscreen mode Exit fullscreen mode

然后，结果被传回函数，在函数中可以将结果赋给一个新变量。

您可能会注意到，这与我们一直使用的许多代码相似。喜欢`read.csv`。那是因为`read.csv`是 R 的制作者写的函数，包含在内供我们使用。

```
clientDf  <-  read.csv(clientCsvPath) 
```

Enter fullscreen mode Exit fullscreen mode

这就是 R 成为强大工具的原因。许多聪明的人写了一套函数，叫做库。感受开源的力量。

是时候回馈社区，写一些我们自己的函数了

## 需要的数据

对于这项工作挑战，您需要:

1.  Client.csv
2.  注册. csv
3.  Project.csv
4.  Exit.csv

## 目标

编写将执行以下操作的函数:

*   连接`clientDf`、`enrollmentDf`、`projectDf`、`exitDf`，返回合并后的数据帧。
*   使以下各列可读:
    *   性别
    *   退伍军人身份
    *   禁用条件
    *   与 oH 的关系
    *   ResidencePriorLengthOfStay
    *   LOSUnderThreshold
    *   以前的街道 h
    *   过去三年
    *   无家可归的过去三年
    *   目的地
*   获取每个人的最新 hud 评估
*   筛选积极参与项目的客户(除了夜间和街头外展项目)
*   根据用户定义的参数编写一个函数来过滤`enrollmentDf`。

**奖金**

*   写一个函数，返回长期无家可归者的名单。

对于最后一个函数，这里有一个例子，

```
clientsWithDisablingCondition  <-  getSubpopulation(df,  "DisablingCondition",  "Yes") 
```

Enter fullscreen mode Exit fullscreen mode

你写的函数应该是`getSubpopulation()`。第一个参数是用户传递给函数的数据帧。第二个参数是要查看的列。最后是用户希望在列中查看哪个响应。

## 资源

以下是对每个步骤都有帮助的资源:

*   R 编程 A-Z -视频 21-R 中的功能
*   [粘贴()](https://www.r-bloggers.com/paste-paste0-and-sprintf/)