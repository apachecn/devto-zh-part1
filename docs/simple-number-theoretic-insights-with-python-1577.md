# Python 中简单的数论见解

> 原文：<https://dev.to/maxvonhippel/simple-number-theoretic-insights-with-python-1577>

有时我会从`ZxZ`到`Z`了解一个新的操作符，或者等价类，或者其他类似的函数。经常观察在`x`和`y`轴上的数字`0 - 9`的网格可以阐明这种泛函的有趣性质。在这里，我将给出一些代码来做到这一点。

在我继续之前，这里有一张`I/O`的图片，来自我将在这篇博文/文章/事情中写的代码。我把它放在开头，这样任何读到它的人都知道代码的目标是什么。

[![I/O Examples](img/94767ccc02c162d551ccf5dedecbf4e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WoL9Cg4c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/taae353uijgo5es5faia.png)

首先让我们定义几个泛函，或函数，或运算，或别的什么。这样，我们可以很快看到我们的`number_table`代码的效用。

这是我的，但是我鼓励你写你自己的。注意我是用`Python3`编码的。代码很简单，我认为可以很容易地移植到您选择的语言上。

```
# Euclid's Algorithm for Greatest Common Denominator (GCD):
# See https://en.wikipedia.org/wiki/Euclidean_algorithm for more info euclid = lambda a, b: a if b == 0 else euclid(b, a % b)

# Two numbers are called "relatively prime" if their gcd is 1 relatively_prime = lambda a, b: int(euclid(a,b) == 1)

modulo = lambda a, b: a % b

and_ab = lambda a, b: a & b 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们已经研究了几个很酷的小函数，让我们开始制作数字网格。我喜欢用数字给我的数字网格着色，所以我用 Python 做了一个给数字着色的小函数。

```
number_color = lambda n: "\033[1;3" + str(n) + ";40m " + str(n) 
```

Enter fullscreen mode Exit fullscreen mode

对于大于 8 的数字，它实际上不起任何作用，但这没关系，因为如果这困扰着你，那么你可以找到一个聪明的方法来解决它。(这并不困扰我。)此外，如果您对大于 9 的数字感兴趣，无论如何您都必须对我的代码进行一点修改，因为我目前在我的表的间距中没有考虑到这一点。

现在，这是我的`number_table`代码:

```
def number_table(method):
    # We assume method is defined
    print("    " + " ".join([number_color(n) for n in range(1,10)]))
    print("     _________________________")
    for a in range(1, 10):
        line = number_color(a) + ": "
        for b in range(1, 10):
            r = method(a, b)
            line += number_color(r) + " "
        print(line) 
```

Enter fullscreen mode Exit fullscreen mode

...这里还有更多:

[![Primality](img/410b455f6f5c935bfe0fff21be1b87ca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M8oxANB_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ojifpvx66jvjkaa168v1.png)

希望这证明有用！