# 绝望的程序员的红色——第一部分

> 原文：<https://dev.to/lepinekong/red-for-hopeless-programmers---part-i-3g0>

[第二部分](https://dev.to/lepinekong/red-for-hopeless-programmers---part-ii-258)可用。

你想学编码吗？你努力学习 C++、C#、Java 甚至 Python 或 Javascript，但很快意识到你仍然不能做任何实际的事情，比如阅读谷歌财经的历史报价来分析你最喜欢的股票，或者只是为你的妈妈创建一个简单的食谱数据库，或者教你自己的孩子如何编程。

你绝望地放弃了，幸运的是我是一个伟大的灵魂，我想把你从绝望中拯救出来，这要感谢一颗小宝石:红色。

# 什么是红色？

因为你很急，我会很快告诉你 [Red](https://www.red-lang.org/) (这里下载)是一种新的编程语言，它与一种不太新的编程语言 Rebol 有很强的兼容性。所以如果你在 Red 上搜索一些答案，你也可以搜索 Rebol。

Rebol 如此不为人知的原因是不幸的，我可以就此展开讨论，但也许下次吧。请放心，读完这篇教程后，你可能会同意它应该得到更好的认可——尽管 JSON 的发明者道格拉斯·克洛克福特对 Rebol 的创造者卡尔·萨森拉斯[[http://www.rebol.com/cgi-bin/blog.r?view=0423](http://www.rebol.com/cgi-bin/blog.r?view=0423)](他也是 Amiga 的操作系统架构师)大加赞赏。

# 你将在这里学到多么神奇的东西

你会学到这些实用的东西:

1.  如何从谷歌金融下载历史报价(第一部分)

2.  如何用几行而不是几十行用其他语言轻松地进行网络抓取([第二部分](https://dev.to/lepinekong/red-for-hopeless-programmers---part-ii-258)

3.  如何为你的妈妈创建一个简单的待办事项或食谱数据库应用程序(第三部分)

4.  如何发送击键来自动化你最喜欢的 windows 应用程序(第四部分)

# 入门

要开始使用:

1.  从[http://www.red-lang.org/p/download.html](http://www.red-lang.org/p/download.html)下载红色语言平台。你可以自己尝试 MacOSX 或 Linux 等其他平台，因为我还没有测试过。

2.  安装红色

3.  发射红色

[![Red Console](img/86feeb51b56c9f3ed2c18151e82884d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3B_inlZq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ixhuw8gyqq9ky0djma67.png)

然后你会在红色的控制台，你可以直接输入下面的程序。

# 如何从谷歌财经下载历史行情

简单地说，这些步骤是:

1.  阅读网址获取报价

2.  记住记忆变量中的引用

3.  将变量中包含的引号写入 csv 文件

通过在红色控制台中键入下面的代码，您可以将其翻译成红色代码(如果您太懒，您也可以将它们复制并粘贴到红色控制台中):

```
; 1\. Read the url to get the quotes

Read http://finance.google.com/finance/historical?output=csv&q=AAPL 

; 2\. Memorize the quotes in a memory variable

quotes: Read http://finance.google.com/finance/historical?output=csv&q=AAPL 

; 3\. Write the quotes contained in the variable to a csv file

write %quotes.csv quotes 
```

这么几句话:

*   在红色中，您可以在代码中添加带有“；”的注释

*   要将内容或值赋给变量，请在变量名后使用“:”

*   文件路径必须以%为前缀，例如%quotes.csv

*   步骤 1 和 2 可以在一个单独的红色步骤中完成，因此该程序只是:

```
; 1&2\. Read the quotes url & Memorize the quotes in a memory variable

quotes: Read http://finance.google.com/finance/historical?output=csv&q=AAPL 

; 3\. Write the quotes contained in the variable to a csv file

write %quotes.csv quotes 
```

您可以在红色的当前目录中检查 quotes.csv 是否存在。要了解当前目录，请键入

```
what-dir 
```

## 如何选择开始日期

您可能希望仅在某个开始日期后下载报价。在这种情况下，您可以要求用户输入(当您使用 ask 时，您必须一次只粘贴一行，以便在保持 Ctrl 键不变情况下使用“Ctrl”删除控制台):

```
url-base: http://finance.google.com/finance/historical?output=csv&q=AAPL

start-date: ask {Start date (YYYY-MM-DD): }

url: rejoin [url-base {&startdate=} start-date]

quotes: Read url

write %quotes.csv quotes 
```

[![Ask-Date](img/56e58d4a4be8227a77afbde595d20459.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iaUjahRc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/izp0j0twr5564s4lef39.png)

解释:

*   为什么我用{}而不是“”来表示一个字符串值？实际上，您可以使用红色的“”，但{}更强大，因为它可以包含多行字符串，并在其中嵌入“”和{}，例如:

```
Explanation: {Why did I use {} instead of "" to represent a string value ? 
In fact you can use "" in Red but {} is more powerfull 
as it can contain multiple lines string and embed both "" and {} inside it} 
```

在其他编程语言中，你经常需要使用所谓的“转义序列”来包含双引号或单引号。

*   要连接字符串，可以使用 rejoin，如下例所示:

```
url: rejoin [url-base {&startdate=} date] 
```

重新加入参数必须包含在由[]分隔的块中

## 练习:要求用户输入股票代码、开始日期和结束日期

现在，您可以使用符号、开始日期和结束日期作为参数进行类似的练习:

```
Read http://finance.google.com/finance/historical?output=csv&q={stock-symbol}&startdate={start-date}&enddate={end-date} 
```

## 如何显示要求用户输入的对话框

您可以使用对话框，而不是在控制台中使用“询问”功能让用户输入。

最基本的红色对话框只是一个空块，你可以随意命名(避免使用表单，因为它是一个标准函数[[【http://www.rebol.com/docs/words/wform.html】](http://www.rebol.com/docs/words/wform.html%5D)])，例如 win like window:

```
win: [] 
```

它不会显示出来，直到您使用查看命令:

```
view win 
```

[![Empty Form](img/a5cae6cc91fbe5b934528c5c63d41cb0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hxa5F43A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d4zkrp51ros25mlt0ob6.png)

原因是您可以预先编写表单，只有当它完成时，您才会向最终用户显示表单布局。

因此，让我们组成我们的布局:我们需要

*   1 股票代码字段

*   2 个日期字段(开始日期和结束日期)

*   1 个确定按钮

最简单的形式是:

```
win: []

append win [field field field button]

view win 
```

[![Basic Form](img/d2ae592162b738339a8ce90d6d44105e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T1-B4iaV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ylcattzxuyzot432mijq.png)

带有标签(带有文本关键字)和垂直对齐(带有 return 关键字)的更好的窗口是:

```
win: []

append win [text "stock symbol:"]

append win [field]

append win [return]

append win [text "start date:"]

append win [field]

append win [return]

append win [text "end date:"]

append win [field]

append win [return]

append win [button "OK"]

view win 
```

[![Nicer Form](img/b99b67fa67562c3aac2e5a7c2aa4ff5c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pPWsQ5aj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1u5vq4lzi4hlrbx0h1hx.png)

如果您不需要动态生成窗口(这在实际应用程序中非常有用，所以我首先向您展示了这种方式)，您可以用紧凑的静态方式来构建表单:

```
win: [
    text "stock symbol:" field
    return
    text "start date:" field
    return
    text "end date:" field
    return
    button "OK"
]
view win 
```

[![Nicer Form Compact](img/d712de7295da6986e3a2369e6370660b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bL8KT8cs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rg76swae9y1rn5xzyt1e.png)

现在，当单击“确定”按钮时，表单必须以“未查看”关闭，因此通过添加如下代码来修改代码行:

```
button "OK" [unview] 
```

所以现在完整的代码是:

```
win: [
    text "stock symbol:" field
    return
    text "start date:" field
    return
    text "end date:" field
    return
    button "OK" [unview]
]
view win 
```

当窗口关闭时，您必须获取每个字段的文本值，因此您必须首先命名这些字段，以便能够在按钮“确定”代码块中引用它们:

```
win: [
    text "stock symbol:" stock-symbol: field
    return
    text "start date:" start-date: field
    return
    text "end date:" end-date: field
    return
    button "OK" [
        unview 
        print [stock-symbol/text start-date/text end-date/text]
    ]
]
view win 
```

出于测试目的，我们只是在控制台中打印值

```
 print [stock-symbol/text start-date/text end-date/text] 
```

/text 表示我们引用字段的文本属性值。

[![fields values](img/27566b9afc1024b2adefe289d238951d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D3umZo-5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/puoe4ev95pl0d4d6e13x.png)

单击 OK 按钮后，控制台应打印字段的值:

[![test ok](img/f6273249e0c987439e113b51f0c65389.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m4HQble4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m1qsexuh32xleagxysh5.png)

我们现在可以用下载报价的代码替换打印指令。

为此，我们将调用一个名为“download-quotes”的函数，传递股票代码、开始日期和结束日期:

```
;ask user's inputs:
win: [
    text "stock-symbol" stock-symbol: field
    return
    text "start-date" start-date: field
    return
    text "end-date" end-date: field
    return
    button "OK" [
        unview 
        quotes: download-quotes stock-symbol/text start-date/text end-date/text
        write %quotes.csv quotes
        print "quotes.csv"
    ]
]
view win

;downloading quotes:

download-quotes: function[stock-symbol start-date end-date][

    url-base: http://finance.google.com/finance/historical?output=csv

    url: rejoin [url-base {&q=} stock-symbol 
                {&startdate=} start-date 
                {&enddate=} end-date]

    quotes: Read url    
    return quotes
] 
```

这就是第一部分的全部内容。如果你喜欢这个教程，我可能很快就会开始第二部分:)

## 参考文献

*   Rebol 读取功能[[http://www.rebol.com/docs/words/wread.html](http://www.rebol.com/docs/words/wread.html)

*   Rebol 写函数[[http://www.rebol.com/docs/words/wwrite.html](http://www.rebol.com/docs/words/wwrite.html)

*   如何创建和使用文本字段[[http://www.rebol.com/how-to/fields.html](http://www.rebol.com/how-to/fields.html)

*   REBOL 可视界面(VID)初学者指南[[http://rebol.com/docs/easy-vid.html](http://rebol.com/docs/easy-vid.html)

*   REBOL/查看 VID 开发者指南[[http://www.rebol.com/docs/view-guide.html](http://www.rebol.com/docs/view-guide.html)