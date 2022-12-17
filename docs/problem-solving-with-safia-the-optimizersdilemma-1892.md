# 用 Safia 解决问题:优化器的困境

> 原文：<https://dev.to/captainsafia/problem-solving-with-safia-the-optimizersdilemma-1892>

我要坦白一件事。

我已经厌倦了阅读 Node 代码库。

我知道，我知道。我现在才做了大约三个星期，但我能说什么呢？我的注意力持续时间很短。也许我会在某个时候回来，但现在，我想尝试一些不同的东西。

我最近回忆起我十几岁时第一次开始编程时喜欢做的一些事情。事实证明，我喜欢解决[项目欧拉](https://projecteuler.net)中的一些问题。事实上，我保留了一个小博客，在那里我保存了我正在解决的问题的解决方案。我会避免在这里链接到那个博客，因为有些事情就是需要默默无闻地死去。

无论如何，我想我会从我停下来的地方开始解决这里的一些问题，并在博客上直播我的解决方案。

事实证明，我解决的最后一个问题(或者至少在博客上公开了解决方案)是 2012 年 9 月的第 22 个问题。那会是我高中二年级的开始。感觉像几个世纪以前！

所以记住这一点，我想六年后，我将开始着手解决第 23 个问题。大概是这样的。

> 一个完全数是这样一个数，它的真约数之和正好等于这个数。例如，28 的真约数之和是 1 + 2 + 4 + 7 + 14 = 28，这意味着 28 是一个完全数。
> 
> 如果一个数 n 的真约数之和小于 n，则称为亏数，如果这个和超过 n，则称为余数。
> 
> 由于 12 是最小的余数，1 + 2 + 3 + 4 + 6 = 16，可以写成两个余数之和的最小数是 24。通过数学分析可以看出，所有大于 28123 的整数都可以写成两个余数之和。然而，这个上限不能通过分析进一步降低，即使已知不能表示为两个丰富数之和的最大数小于这个极限。
> 
> 求所有不能写成两个丰数之和的正整数之和。

好吧！所以这里的主要目标是求所有不能写成两个丰数之和的正整数之和。问题文本还告诉我们，每一个大于 28，123 *的数*都可以写成两个余数之和。这就把我们的搜索范围缩小到了 0 到 28，123 之间。这是一个相当大的搜索空间，尽管我们有这些被称为计算机的东西，它们又笨又快，我们可以让它们工作！

我承认，我曾经是那种会坐下来看着这样的问题，并试图马上想出一个聪明的解决方案的程序员。但是我变老了(也变聪明了),并且意识到在大多数情况下，你会发现你只是在问题上抛出一个 for 循环。所以我创建了一个快速的小模板来展示这个解决方案。

```
def abundant_terms_for_sum(x):
    fancy math stuff that I'm unsure of yet

def non_abundant_sums():
  total = 0
  for x in range(28123):
      if not abundant_terms_for_sum(x): total += x
  return total 
```

很基本，对吧？

旁注:我将使用 Python 3 来解决这些问题。这是我十几岁时用来解决这些问题的同一种编程语言。虽然回头看看我的博客，我用 Common Lisp 解决了一部分。也许我现在会试着去做那件事！

现在，自从我第一次在高二开始解决这些问题，我已经上了大约 6 年的高等代数和微积分课。话虽如此，我还是不知道自己在做什么数学。所以我去了可靠的谷歌网站，看看是否有比我更喜欢数字的人想出了一个聪明的方法来确定一个数字是否不能是两个丰富的数字之和。

旁注:如果你自己不能聪明，你总是可以利用另一个人的聪明！

我在网上找不到任何有用的东西，所以这一次我不得不自己动脑筋了。我想这些问题的关键是让脑袋工作起来…

所以，我对这类事情的一般策略是，用我认为可能需要调用的所有函数的支架来创建程序的大纲。

```
def generate_abundant_numbers():
    create a list of the abundant numbers less than 28123

ABUNDANT_NUMBERS = generate_abundant_numbers()

def abundant_terms_for_sum(x):
    for num in ABUNDANT_NUMBERS:
        difference = x - num
        if difference in ABUNDANT_NUMBERS:
            return True
    return False

def non_abundant_sums():
  total = 0
  for x in range(28123):
      if not abundant_terms_for_sum(x): total += x
  return total 
```

所以基本上，我的计划是生成一个列表，列出所有小于我们在一个名为`ABUNDANT_NUMBERS`的全局中设置的边界 28，123 的大量数字。然后，`abundant_terms_for_sum`函数将检查`x`的和的项是否在`ABUNDANT_NUMBERS`中，并进行适当的处理。这里唯一未完成的功能是`generate_abundant_numbers`功能。我做了一些黑客工作，想弄清楚我是否可以使用 for-loops 和 mathy-math 实现一些东西，并得出了以下结论。

```
def get_proper_divisors(n):
  divisors = []
  for x in range(1, n + 1):
    if n % x == 0 and n != x: divisors.append(x)
  return divisors

def generate_abundant_numbers():
  numbers = []
  for x in range(28123):
    proper_divisors = get_proper_divisors(x)
    if sum(proper_divisors) > x:
      numbers.append(x)
  return numbers 
```

现在，这段代码运行了很长时间，我不得不在它运行完的时候修剪我的头发。不完全是，我实际上在它检查第 93 个数字的时候停止了它，但是你知道要点。

这里的罪魁祸首是这样一个事实，有两个从 0 到 28123 的**迭代，所以时间复杂度(哦天哪，我刚刚用了那些词吗？！！？)是`O(n^2)`。**

如果这是一次技术面试，我会茫然地盯着屏幕，向电话另一端的可怜人喋喋不休地说出我的意识流。因为我只是一个人在我的卧室里做这个，所以我会非常努力地盯着代码，直到一些启示通过某种形式的空气传播击中了我。

使劲盯着看。

继续盯着。

和思考。

所以我可以在这里做一些事情。问题陈述 12 是最小的余数。所以我更新了我的代码来反映这一点。

```
def generate_abundant_numbers():
  numbers = []
  for x in range(12, 28123): 
```

接下来我意识到我的`abundant_terms_for_sum`函数有问题。当遍历每个`ABUNDANT_NUMBERS`时，我需要更好地跳过那些我知道肯定不是解决方案的一部分的大量数字。

```
def abundant_terms_for_sum(x):
    for num in ABUNDANT_NUMBERS:
      if num > x: return False
      difference = x - num
      if difference in ABUNDANT_NUMBERS:
        return True
    return False 
```

有了这些改变，我注意到程序运行得快多了。实际上，我没有做任何事情来改变实现的时间复杂度，但是我所做的一些小改动有助于提高我所处理的一般情况的运行时间。

此时，我实际上决定让程序一直运行下去。我仍然没有真正验证我的实现是正确的，所以对我来说，优化一些可能不完全准确的东西有点傻。

所以我让这段相当慢的代码运行了一会儿，然后我出去假装我不是一个真正的机器人，同时我打扫了我的公寓。

一旦运行完成，我将得到的答案粘贴到检查器中，发现我是正确的。真是松了一口气！现在我可以做更多的优化

我做的下一件事是对`proper_divisors`和`generate_abundant_numbers`的工作方式做了一些改进。总的来说，这些变化降低了程序的空间复杂度，因为我是直接计算适当的除数的和，而不是将除数存储在一个数组中，然后将它们相加。这有一点帮助，因为 Python 中的`sum`函数的时间复杂度是`O(n)`。

```
def get_proper_divisors(n):
  total = 0
  for x in range(1, n + 1):
    if n % x == 0 and n != x: total += x
  return total

def generate_abundant_numbers():
  numbers = []
  for x in range(12, 28123):
    sum_proper_divisors = get_proper_divisors(x)
    if sum_proper_divisors > x:
      numbers.append(x)
  return numbers 
```

旁注:我知道我经常使用时间复杂性这个词，如果你是一个新的程序员，这可能会让人害怕。你可以在这里[或者这里](http://podbay.fm/show/1304168963/e/1511913661?autostart=1)或者[这里](https://rob-bell.net/2009/06/a-beginners-guide-to-big-o-notation/)读到更多关于时间复杂度的内容，但基本上这只是一种回答“这个程序需要运行多长时间”这个问题的奇特方式

我做的下一件事是重构`non_abundant_sums`函数，以利用列表理解。

```
def non_abundant_sums():
  return sum([x for x in range(28123) if not abundant_terms_for_sum(x)]) 
```

我目前的解决方案是这样的。

```
def get_proper_divisors(n):
  total = 0
  for x in range(1, n + 1):
    if n % x == 0 and n != x: total += x
  return total

def generate_abundant_numbers():
  numbers = []
  for x in range(12, 28123):
    sum_proper_divisors = get_proper_divisors(x)
    if sum_proper_divisors > x:
      numbers.append(x)
  return numbers

ABUNDANT_NUMBERS = generate_abundant_numbers()

def abundant_terms_for_sum(x):
    for num in ABUNDANT_NUMBERS:
      if num > x: return False
      difference = x - num
      if difference in ABUNDANT_NUMBERS:
        return True
    return False

def non_abundant_sums():
  return sum([x for x in range(28123) if not abundant_terms_for_sum(x)])

print(non_abundant_sums()) 
```

老实说，这仍然是相当 hecking 缓慢。

首先，运行`get_proper_divisors`函数需要很长时间。我使用一种非常常见的因式分解优化算法对它进行了优化，这种算法依赖于一个数的因子的属性之一。

```
def get_proper_divisors(n):
  limit = math.sqrt(n)
  if limit.is_integer(): total = -limit
  else: total = 1
  for x in range(2, int(limit) + 1):
    if n % x == 0: 
      total += x + int(n / x)
  return total 
```

我做的下一件事是去除对`abundant_terms_for_sum`的依赖，只使用 Python 的`any`函数来检查是否有任何丰富的术语加起来达到特定的总和。

```
def non_abundant_sums():
  total = 0
  for x in range(28123):
    if not any((x - i in ABUNDANT_NUMBERS) for i in ABUNDANT_NUMBERS): total += x
  return total 
```

尽管有这些变化，程序运行仍然有点慢。具体来说，代码中有两个 for 循环迭代了 28，123 次，一个在`non_abundant_sums`中，一个在`generate_abundant_numbers`中。我决定将这两个函数结合在一起，并避免预先分配从属数字。我最后还使用了一个`set`来存储日期，因为我意识到我们并不在乎数据集中有重复的求和条目。

```
def non_abundant_sums():
  total = 0
  numbers = set()
  for x in range(28123):
    if get_proper_divisors(x) > x:
      numbers.add(x)
    if not any((x - i in numbers) for i in numbers):
      total += x
  return total 
```

太棒了。现在程序运行得稍微快了一点。这是好奇者的最终代码。

```
import math

def get_proper_divisors(n):
  limit = math.sqrt(n)
  if limit.is_integer(): total = -limit
  else: total = 1
  for x in range(2, int(limit) + 1):
    if n % x == 0: 
      total += x + int(n / x)
  return total

def non_abundant_sums():
  total = 0
  numbers = set()
  for x in range(28123):
    if get_proper_divisors(x) > x:
        numbers.add(x)
    if not any((x - i in numbers) for i in numbers):
      total += x
  return total

print(non_abundant_sums()) 
```

所以基本上，我开始写了很多非常简单的代码，然后我删掉了很多。这通常是我解决问题时的情况。把我能做的都放到屏幕上，然后看看我能不能做得更好！

在那里的某个地方有一个重要的人生教训…