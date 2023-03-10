# 展开装饰者，第 1 部分

> 原文：<https://dev.to/rpalo/unwrapping-decorators-part-1>

# 前言

我在之前的一篇文章中承诺过要谈论 Python decorators。聪明的读者注意:显然有一种叫做装饰者模式的东西。虽然可以使用 Python“小写 Decorator 来实现 Decorator 模式，但这只是它们的一种可能用途。关于命名问题的更长更详细的讨论，A 型人应该阅读相关的 [PEP](https://www.python.org/dev/peps/pep-0318/#on-the-name-decorator) 。对于本文的其余部分，当我说 decorator 时，我指的是 Python decorator 语言特性。现在。前进！

# 先决条件

我必须设定一个底线，这样每个人都在同一页上。如果你熟悉函数概念，比如函数作为变量、参数、返回对象以及函数中的函数——而且你不想听我唠叨这些——你可以[跳到精彩的部分](#Decorators)。

## 函数为变量

对于那些还和我在一起的人，上帝保佑你们。如果你不知道，就像你给一个变量赋值一样，你可以不用调用函数就给它赋值。

```
>>> a = "soup"
>>> b = 4
>>> def how_much_food(food, quantity):
...     return "I've got {} {}s!".format(quantity, food)
>>> gerald = how_much_food
>>> gerald(a, b)
"I've got 4 soups!" 
```

Enter fullscreen mode Exit fullscreen mode

## 起参数和返回值的作用

很酷，对吧？现在，直接的结果是，你可以在其他函数之间传递函数，就像其他变量一样。

```
def call_this(f):
    f()
    print("Called it!")

def call_with_three(f):
    f(3)

def woof(times=1):
    return "Woof!" * times

>>> call_this(woof)
"Woof!"
"Called it!"
>>> call_with_three(woof)
"Woof!Woof!Woof!" 
```

Enter fullscreen mode Exit fullscreen mode

## 在其他函数内定义函数

您甚至可以在其他函数中定义函数！这可*超级*厉害。这就引出了我一直以来最喜欢的编码难题之一:通过下面的测试。

`assert five(plus(three())) == 8`

```
def three(operator=None):
    if operator is None:
        return 3
    else:
        return operator(3)

def five(operator):
    if operator is None:
        return 5
    else:
        return operator(5)

def plus(second_number):
    def inner(first_number):
        return first_number + second_number
    return inner

# Get it?
# five(plus(three()))
# five(plus(3)) -> def inner: return first_number + 3
# five(inner) -> inner(5) -> 5 + 3 -> 8 
```

Enter fullscreen mode Exit fullscreen mode

就像我说的，这是一个难题，所以如果你第一次没有得到它，试着在纸上写出来，像做数学题一样做替换。总之，长话短说，函数是整洁的小对象，你可以随意使用，在任何你可以使用文字的地方定义。一个函数不会被调用，直到你在最后加上一些()。现在，主要事件。

# 装修工

Decorators 用于包装其他函数以添加独立的功能，而不会污染正在讨论的函数。这有助于每个职能保持单一职责。我相信先学习蹩脚的方法来做某事，这样你就会欣赏漂亮方法的美，所以让我们看看我们会怎么做...

## 蹩脚的方式

想象一下，我们有一个函数，每次调用它的内部函数时，它都会吠叫。为什么？因为举例很难。

```
def pre_bark(func):
    def inner():
        print("Woof!")
        return func()
    return inner

def hello():
    print("Hello!") 
```

Enter fullscreen mode Exit fullscreen mode

为了将`hello`包含在 woofing 功能中，我们必须这样做:

```
>>> hello = pre_bark(hello)
>>> hello()
"Woof!"
"Hello!" 
```

Enter fullscreen mode Exit fullscreen mode

你看到发生了什么吗？我们用`inner`代替`hello`，打印“Woof！”在调用原来的`hello`函数之前。不漂亮。不漂亮是因为定义好之后还要把`hello`包起来。有人可能会在调用函数定义之前扫一眼，愉快地期待`Hello!`，却得到一个意外的`Woof!`。最少惊喜的路径通常是最好的路径。如果它看起来更像这样呢:

## 蛮道

```
@pre_bark
def hello():
    print("Hello!") 
```

Enter fullscreen mode Exit fullscreen mode

您将能够很快看到这个函数做了什么，它被一个叫做`pre_bark`的东西稍微修改了一下。现在我们在谈话。然后是晚上，然后是早上——你的第一个室内设计师。感觉很好。

## 但是我的论点呢？

为了拦截传递给函数的任何参数，内部函数将以*args，**kwargs 的形式接受这些参数。我现在不会深入这个问题，但是如果你不熟悉的话，这个有一个很好的解释。简而言之:将*args 看作是传递的所有位置参数的列表，将**kwargs 看作是传递的所有关键字参数的字典。这不太准确，但对政府工作来说已经够近了。

```
def print_them_args(func):

    def the_name_of_this_one_doesnt_matter(*args):
        print("{} called with {}".format(func.__name__, [*args]))
        return func(*args)
    return the_name_of_this_one_doesnt_matter

@print_them_args
def add(a, b):
    return a + b

>>> add(5, 8)
add called with [5, 8]
13 
```

Enter fullscreen mode Exit fullscreen mode

# 第一部分的结论

我本来打算继续，因为我们仍然要讨论传递参数给 decorator，堆叠 decorator，我们甚至还没有开始讨论你可以用 decorator 和类做什么！但是后来我看了看我的单词计数仪，它告诉我，我已经远远超出了最勇敢的注意力持续时间。下周我会完成剩下的工作。我相信每个人都会按下刷新按钮，焦急地等待戏剧性的结局。

[![Dog mashing keyboard pillow](img/e6508ffcdfa0771c1827650af3b315d2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qdelMUFJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://assertnotmagic.com/img/cliffhanger.gif)

* * *

*最初发布于[我的博客](http://assertnotmagic.com)。*