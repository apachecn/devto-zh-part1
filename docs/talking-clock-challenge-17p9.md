# 会说话的钟挑战

> 原文：<https://dev.to/zeeter/talking-clock-challenge-17p9>

# 目录

1.  [简介](#orgd636e2d)
2.  [描述](#org82c9658)
    1.  [输入描述](#org185d42f)
    2.  [输出描述](#orgcc22dc4)
    3.  [可选挑战](#orgf0dd946)
3.  [我的解决方案](#org004c7f3)
    1.  [功能测试](#orgf30d35c)
    2.  [解析输入](#org8b561fc)
    3.  [将数字转换成文字](#org455ad6b)
        1.  [获取小时字符串](#orge9533a1)
        2.  [获取分钟字符串](#org083d48c)
        3.  [求周期](#org164c237)
    4.  [将所有这些放在一起](#org2109678)

# 简介

挑战可以在这里找到[。](https://www.reddit.com/r/dailyprogrammer/comments/6jr76h/20170627_challenge_321_easy_talking_clock/)

# 描述

创建一个可以将时间字符串转换成文本的程序。

## 输入描述

输入是一个小时(0-23)，后跟一个冒号，再后跟一分钟(0-59)

```
00:00
01:30
12:05
14:01
20:29
21:00 
```

## 输出描述

程序应该输出时间的话，使用 12 小时格式。(上午 12 时)

```
It's twelve am
It's one thirty am
It's twelve oh five pm
It's two oh one pm
It's eight twenty nine pm
It's nine pm 
```

## 可选挑战

使用声音剪辑让程序真正说话，就像在这里找到的。

# 我的解决方案

所以我有一个不需要测试的解决方案。然而，我一直试图养成遵循测试驱动开发的习惯。此外，我将使用一种有文化的编程技术来编写本文中的程序
。我想说明的另一件事是，我也几乎完全夸大了这一点，
当我写作的时候，我会有点狂热。

## 功能测试

首先，下面的代码块是这个挑战的功能测试。

```
def talkingclock(time):
    hour,minute = map(int, time.split(":"))
    if hour >= 12:
        period = "pm"
    else:
        period = "am"
    numnames = (
        'one','two','three',
        'four','five','six',
        'seven','eight','nine',
        'ten','eleven','twelve')

    if hour > 12:
        hour -= 12
    else:
        pass

    hour_string = numnames[hour-1]
    minute_string = ""

    onetable = ("one","two","three",
                "four", "five", "six",
                "seven","eight","nine")

    teentable = ("ten","eleven","tweleve","thirteen",
                 "fourteen", "fifteen","sixteen",
                 "seventeen","eighteen","nineteen")

    tentable = ("","ten","twenty","thirty","forty","fifty",)

    if minute in range(1,10):
        minute_string = "oh " + onetable[minute-1]
    elif minute in range(10,20):
        minute_string = teentable[minute-10]

    elif minute in range(20,60):
        ones = minute % 10
        tens = minute - ones
        if ones > 0:
            minute_string = tentable[tens//10] + " " + onetable[ones-1]
        else:
            minute_string = tentable[tens//10]

    output = "It's " + hour_string + ' '
    if minute_string == '':
        output += period
    else:
        output += minute_string + ' ' + period

    return output

testtable = [i for i in zip(si.split(),so.split('\n\t'))]

results = [["Input","Expected Output","Actual Output", "Passed"]]

for test in testtable:

    si,so = test
    to = talkingclock(si)
    results.append([si,so,to,so==to])

return results 
```

该测试的工作原理是收集样本输入和样本输出，如挑战部分所述，它将它们分成适当的行
，并将它们存储为列表理解。我会在这里使用元组，因为数据永远不会改变。然而，如果在定义 listcomp 时用括号替换方括号，python 会创建生成器对象
。在那种情况下，我也可以使用独立的压缩功能。
总之，输入字符串被输入到 talkingclock 函数中，输出与测试输出进行比较。
这产生了上面的结果，如果其中任何一个错误，我们可以查看它出现在哪一行，并单独测试该输入。
虽然这不是最优雅的测试解决方案，但它是一个相当实用的测试，因为我们从中获得了一个很好的表格。

## 解析输入

通话时钟的输入格式为 24 小时制(HH:MM)。为了做到这一点，我们使用字符串的 split 方法在冒号处分割字符串。
这会产生一个小时和分钟的字符串。这些然后被转换成整数，并分别分配给
变量小时和分钟。

```
hour,minute = map(int, time.split(":")) 
```

为了测试我们的解析器的工作，我们需要寻找三样东西。

1.  它返回一个小时值
2.  它返回一个分钟值
3.  小时和分钟都是整数

    def testparser(time):
    小时，分钟= map(int，time . split(":))
    返回小时，分钟

    th，tm = testparser(测试)

    isint = all(map(lambda x:type(x)= = int，(th，TM))
    has hour = th！= None
    hasmin = tm！=无

    结果=(
    (“Returns hour:"，hashour)，
    (“Returns minutes:"，hasmin)，
    (“All is ints:"，isint))

    返回结果

假设当您运行这个测试时，它们都通过了，我们知道解析器可以运行了。

## 将字体转换为文字

既然我们已经将输入解析为正确的形式，我们需要处理它以得到正确的字符串。

### 获取小时字符串

获取小时字符串有一些要求。

1.  小时字符串将始终介于 1 和 12 之间。
2.  小时字符串将是数字的英文名称(例如 1)

    numnames =(
    ‘一’，‘二’，‘三’，【T1’，‘四’，‘五’，‘六’，【T2’，‘七’，‘八’，‘九’，【T3’，‘十’，‘十一’，‘十二’)

    如果小时> 12:
    小时-= 12
    否则:
    通过

    hour_string = numnames[hour-1]

    def testhourstring(hour):
    numnames =(
    '一'，'二'，'三'，【T2 '，'四'，'五'，'六'，【T3 '，'七'，'八'，'九'，【T4 '，'十'，'十一'，'十二')

    ```
    if hour > 12:
        hour -= 12
    else:
        pass

    hour_string = numnames[hour-1]
    return hour_string 
    ```

    nname = (
    '一'，'二'，'三'，
    '四'，'五'，'六'，【T2 '，'七'，'八'，'九'，【T3 '，'十'，'十一'，'十二')

    zeroistwelve =(testhourstring(0)= = ' tweleve ')
    oneisoneffect = all([testhourstring(I)= = nname[(I-1)]for I in range(1，13)])
    thirteenisoneffect = all([testhourstring(I)= = nname[(I-1)]for I in range(1，13)]

    结果= (
    (“测试”，“通过”)，
    (“0 产生 12 级:“，zeroistwelve)，
    (“1-13 产生正确名称:“，oneisoneect)，
    (“13-23 产生正确名称:“，thirteenisonect)
    )

    回送结果

### 获取分钟字符串

分钟字符串比小时字符串稍微复杂一些。实际上，我已经把它分成了几个部分

1.  从 1 到 9 的 1 的表。
2.  从 10 岁到 19 岁的青少年桌子
3.  10 比 50 的十进制表格

对于 1-9 的数字，该字符串是通过从 1 表中取出数字并将其附加到“oh”短语上而形成的。因为英语很糟糕，我不得不制作青少年表格，因为 11-19 的数字都必须是特殊的，不符合其他数字标准。
最后，对于 20-59 之间的数字，这个数字是通过读取十位空间得到的，如果它在一位空间中有一个数字，就把这个数字加到它上面。
十进制表还包括 10，以使我的一些数学更容易，代价是使元组成为更长的元素，我认为这是一个有效的
权衡。

```
minute_string = ""

onetable = ("one","two","three",
            "four", "five", "six",
            "seven","eight","nine")

teentable = ("ten","eleven","tweleve","thirteen",
             "fourteen", "fifteen","sixteen",
             "seventeen","eighteen","nineteen")

tentable = ("","ten","twenty","thirty","forty","fifty",)

if minute in range(1,10):
    minute_string = "oh " + onetable[minute-1]
elif minute in range(10,20):
    minute_string = teentable[minute-10]

elif minute in range(20,60):
    ones = minute % 10
    tens = minute - ones
    if ones > 0:
        minute_string = tentable[tens//10] + " " + onetable[ones-1]
    else:
        minute_string = tentable[tens//10]

def testminstring(minute):
    minute_string = ""

    onetable = ("one","two","three",
                "four", "five", "six",
                "seven","eight","nine")

    teentable = ("ten","eleven","tweleve","thirteen",
                 "fourteen", "fifteen","sixteen",
                 "seventeen","eighteen","nineteen")

    tentable = ("","ten","twenty","thirty","forty","fifty",)

    if minute in range(1,10):
        minute_string = "oh " + onetable[minute-1]
    elif minute in range(10,20):
        minute_string = teentable[minute-10]

    elif minute in range(20,60):
        ones = minute % 10
        tens = minute - ones
        if ones > 0:
            minute_string = tentable[tens//10] + " " + onetable[ones-1]
        else:
            minute_string = tentable[tens//10]

    return minute_string

singledigit = testminstring(5) == "oh five"

teens = testminstring(15) == "fifteen"

justtens = testminstring(20) == "twenty"

tensandones = testminstring(35) == "thirty five"

doesnone = testminstring(0) == ""

results = (("test","passed"),
           ("Does single digits: ", singledigit),
           ("Does teens: ", teens),
           ("Does just tens: ", justtens),
           ("Does tens: ", tensandones),
           ("Does zero: ", doesnone)
)

return results 
```

与小时测试不同，这个测试使用了一些硬编码值。然而，我需要测试一些特定的条件，我认为一个不太健壮的测试在这里会更好。与我的小时测试一样，您可以用 list comp 测试所有可能的有效输入。但是在这种情况下，我不确定这样做是否值得。

### 寻找时期

要找到时间段，(上午或下午)是一个非常简单的任务。这是通过获取小时变量，并检查它是否大于或等于 12 来完成的。
如果是，则周期为 pm，否则为 am。
重要的是这段代码必须在得到小时字符串之前运行，因为小时变量是从那个操作中改变的。
这可以通过存储原始值来限制，但是我认为这只会使问题过于复杂。

```
if hour >= 12:
    period = "pm"
else:
    period = "am"

def testgetperiod(hour):
    if hour >= 12:
        period = "pm"
    else:
        period = "am"
    return period

amworks = all([testgetperiod(t) == 'am' for t in range(0,12)])
pmworks = all([testgetperiod(t) == 'pm' for t in range(12,23)])

results = (("test","passed"),
           ("Am works: ",amworks),
           ("Pm works: ",pmworks),
)

return results 
```

有了这么简单的一个零件，就真的不需要测试了。但我想忠实于试验山羊的叫声。

## 把所有的东西放在一起

现在我们已经有了所有相关的部分，我们只需要按照正确的顺序把它们串在一起。我在这里主要寻找的是分钟字符串是否为空。
如果是，小时和周期之间应该只有一个空格。
在我对这个问题的最后一次尝试中，我在这个阶段犯了一个错误，因此我小心谨慎。

```
def talkingclock(time):
    hour,minute = map(int, time.split(":"))
    if hour >= 12:
        period = "pm"
    else:
        period = "am"
    numnames = (
        'one','two','three',
        'four','five','six',
        'seven','eight','nine',
        'ten','eleven','twelve')

    if hour > 12:
        hour -= 12
    else:
        pass

    hour_string = numnames[hour-1]
    minute_string = ""

    onetable = ("one","two","three",
                "four", "five", "six",
                "seven","eight","nine")

    teentable = ("ten","eleven","tweleve","thirteen",
                 "fourteen", "fifteen","sixteen",
                 "seventeen","eighteen","nineteen")

    tentable = ("","ten","twenty","thirty","forty","fifty",)

    if minute in range(1,10):
        minute_string = "oh " + onetable[minute-1]
    elif minute in range(10,20):
        minute_string = teentable[minute-10]

    elif minute in range(20,60):
        ones = minute % 10
        tens = minute - ones
        if ones > 0:
            minute_string = tentable[tens//10] + " " + onetable[ones-1]
        else:
            minute_string = tentable[tens//10]

    output = "It's " + hour_string + ' '
    if minute_string == '':
        output += period
    else:
        output += minute_string + ' ' + period

    return output 
```

现在有了这个版本的程序，输出字符串不会打印到
stdout。出于几个原因，我没有提到这一点。如果我决定将来做可选挑战
，直接打印到 stdout 不会有太大帮助。
其次，我想让测试尽可能简单。如果你注意到，
上面的部分从不导入代码，这包括 unittest。
Unittest 对于更复杂的测试来说是一个超级有用的框架，但是我还没有弄清楚如何用它来制作漂亮的表格。我可能会考虑在未来使用 nose，但我还没有完全实现。