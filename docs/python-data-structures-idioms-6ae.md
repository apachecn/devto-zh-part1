# Python 数据结构习惯用法

> 原文：<https://dev.to/mushketyk/python-data-structures-idioms-6ae>

作为开发人员，我们大部分时间都花在编写操纵基本数据结构的代码上:遍历列表、创建地图、过滤集合中的元素。因此，了解如何在 Python 中有效地做到这一点并使您的代码更具可读性和效率是非常重要的。

# 使用列表

## 遍历一个列表

在 Python 中有很多方法可以迭代列表。最简单的方法就是保持列表中的当前位置，并在每次迭代中递增:

```
## SO WRONG l = [1, 2, 3, 4, 5]
i = 0
while i < len(l):
    print l[i]
    i += 1 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但是 Python 提供了一种更方便的方法来使用**函数。**范围**功能可用于生成从 0 到 N 的数字，这可用作 C:
中循环的**模拟****

```
## STILL WRONG for i in range(len(l)):
    print l[i] 
```

Enter fullscreen mode Exit fullscreen mode

虽然这更简洁，但有更好的方法，因为 Python 允许我们直接迭代列表，类似于其他语言中的 **foreach** 循环:

```
# RIGHT for v in l:
    print v 
```

Enter fullscreen mode Exit fullscreen mode

## 以相反的顺序遍历列表

我们如何以相反的顺序迭代一个列表？一种方法是使用不可读的 3 个参数版本的 **range** 函数，并提供列表中最后一个元素的位置(第一个参数)、列表中第一个元素之前的一个元素的位置(第二个参数)以及逆序前进的负步长(第三个参数):

```
# WRONG for i in range(len(l) - 1, -1, -1):
    print l[i] 
```

Enter fullscreen mode Exit fullscreen mode

但是正如您可能已经猜到的，Python 应该提供了一种更好的方法。我们可以只使用 [**反转**](https://docs.python.org/2/library/functions.html#reversed) 函数中的**进行**循环:

```
# RIGHT for i in reversed(l):
    print i 
```

Enter fullscreen mode Exit fullscreen mode

## 访问最后一个元素

访问列表中最后一个元素的常用习语是:获取列表的长度，从中减去 1，使用结果号作为最后一个元素的位置:

```
# WRONG l = [1, 2, 3, 4, 5]
>>> l[len(l) - 1]
5 
```

Enter fullscreen mode Exit fullscreen mode

这在 Python 中很麻烦，因为它支持从列表末尾访问元素的负索引。So -1 是最后一个元素:

```
# RIGHT >>> l[-1]
5 
```

Enter fullscreen mode Exit fullscreen mode

负索引也可以用来访问倒数第二个元素，依此类推:

```
# RIGHT >>> l[-2]
4
>>> l[-3]
3 
```

Enter fullscreen mode Exit fullscreen mode

## 使用顺序拆包

在其他编程语言中，从列表中提取多个变量的值的一种常见方式是使用索引:

```
# WRONG l1 = l[0]
l2 = l[1]
l3 = l[2] 
```

Enter fullscreen mode Exit fullscreen mode

但是 Python 支持序列解包，让我们可以从一个列表中提取多个变量的值:

```
# RIGHT l1, l2, l3 = [1, 2, 3]

>>> l1
1
>>> l2
2
>>> l3
3 
```

Enter fullscreen mode Exit fullscreen mode

## 使用列表理解

假设我们想要过滤 18 岁或以下用户发布的电影的所有等级。

你写过多少次这样的代码:

```
# WRONG under_18_grades = []
for grade in grades:
    if grade.age <= 18:
        under_18_grades.append(grade) 
```

Enter fullscreen mode Exit fullscreen mode

在 Python 中不再这样做，而是使用带有 if 语句的列表理解。

```
# RIGHT under_18_grades = [grade for grade in grades if grade.age <= 18] 
```

Enter fullscreen mode Exit fullscreen mode

## 使用枚举功能

有时你需要遍历一个列表并跟踪每个元素的位置。比方说，如果你需要在一个 shell 中显示一个菜单项，你可以简单地使用 **range** 函数:

```
# WRONG for i in range(len(menu_items)):
    menu_items = menu_items[i]
    print "{}. {}".format(i, menu_items) 
```

Enter fullscreen mode Exit fullscreen mode

更好的方法是使用 [**枚举**](https://docs.python.org/2/library/functions.html#enumerate) 函数。它是一个迭代器，返回包含元素位置和元素本身的对:

```
# RIGHT for i, menu_items in enumerate(menu_items):
    print "{}. {}".format(i, menu_items) 
```

Enter fullscreen mode Exit fullscreen mode

## 使用键进行排序

在其他编程语言中，对元素进行排序的一种典型方式是提供一个比较两个对象以及要排序的集合的函数。在 Python 中，它看起来像:

```
people = [Person('John', 30), Person('Peter', 28), Person('Joe', 42)]

# WRONG def compare_people(p1, p2):
    if p1.age < p2.age:
        return -1
    if p1.age > p2.age:
        return 1
    return 0

sorted(people, cmp=compare_people)

[Person(name='Peter', age=28), Person(name='John', age=30), Person(name='Joe', age=42)] 
```

Enter fullscreen mode Exit fullscreen mode

但这不是最好的方法。因为我们所要做的就是比较两个 **Person** 类的实例，也就是比较它们的 **age** 字段的值。为什么我们要为此编写一个复杂的比较函数？

具体到这种情况， [**排序的**](https://docs.python.org/2/library/functions.html#sorted) 函数接受**键**函数，该函数用于提取一个键，该键将用于比较一个对象的两个实例:

```
# RIGHT sorted(people, key=lambda p: p.age)
[Person(name='Peter', age=28), Person(name='John', age=30), Person(name='Joe', age=42)] 
```

Enter fullscreen mode Exit fullscreen mode

## 使用全部/任意功能

如果您想检查集合中的所有值或任何值是否为真，一种方法是遍历列表:

```
# WRONG def all_true(lst):
    for v in lst:
        if not v:
            return False
    return True 
```

Enter fullscreen mode Exit fullscreen mode

但是 Python 已经具备了 [**所有**](https://docs.python.org/2/library/functions.html#all)[**任何**](https://docs.python.org/2/library/functions.html#any) 的功能。**如果传递给它的 iterable 中的所有值都为 True，则 all** 返回 True，而**如果传递给它的值中至少有一个为 True，则 any** 返回 True:

```
# RIGHT all([True, False])
>> False

any([True, False])
>> True 
```

Enter fullscreen mode Exit fullscreen mode

为了检查所有的条目是否符合特定的条件，您可以使用 list comprehension 将一个任意对象的列表转换为一个布尔列表:

```
all([person.age > 18 for person in people]) 
```

Enter fullscreen mode Exit fullscreen mode

或者你可以传递一个生成器(只需省略列表理解两边的方括号):

```
all(person.age > 18 for person in people) 
```

Enter fullscreen mode Exit fullscreen mode

这不仅可以节省你两次击键的时间，还可以省略中间列表的创建(稍后会详细介绍)。

## 使用切片

您可以使用一种称为切片的技术来获取列表的一部分。访问列表时，您可以提供以下三个值
，而不是在方括号中提供一个索引

```
lst[start:end:step] 
```

Enter fullscreen mode Exit fullscreen mode

所有这些参数都是可选的，如果您省略其中一些参数，您可以获得列表的不同部分。如果只提供起始位置，它将返回从指定索引开始的列表中的所有元素:

```
# RIGHT >>> lst = range(10)
>>> lst[3:]
[3, 4, 5, 6, 7, 8, 9] 
```

Enter fullscreen mode Exit fullscreen mode

如果只提供了结束位置，切片将返回到所提供位置的所有元素:

```
>>> lst[:-3]
[0, 1, 2, 3, 4, 5, 6] 
```

Enter fullscreen mode Exit fullscreen mode

你也可以得到两个索引之间的部分列表:

```
>>> lst[3:6]
[3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，切片中的步长等于 1，这意味着返回起始位置和结束位置之间的所有元素。如果你想得到每两个元素或每三个元素，你需要提供一个步长值:

```
>>> lst[2:8:2]
[2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

# 不要创建不必要的对象

## 使用 xrange

如果你需要在一个范围内生成一致的整数值，range 是一个有用的函数，但是它有一个缺点:它返回一个包含所有生成值的列表:

```
# WRONG
# Returns a too big list for i in range(1000000000):
    ... 
```

Enter fullscreen mode Exit fullscreen mode

这里的解决方法是使用 [**xrange**](https://docs.python.org/2/library/functions.html#xrange) 函数。它立即返回一个迭代器而不是创建一个列表:

```
# RIGHT
# Returns an iterator for i in xrange(1000000000):
    ... 
```

Enter fullscreen mode Exit fullscreen mode

与 **range** 函数相比， **xrange** 的缺点是它的输出只能迭代一次。

### Python 3 中的新功能

在 Python 3 **中，xrange** 被移除， **range** 函数的行为类似于 Python 2.x 中的 **xrange** 。如果您需要多次迭代 Python 3 中 **range** 的输出，您可以将其输出转换为一个列表:

```
>>> list(range(10))
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9] 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 izip

如果需要从两个集合中的元素生成对，一种方法是使用 **zip** 函数:

```
# WRONG names = ['Joe', 'Kate', 'Peter']
ages = [30, 28, 41]
# Creates a list zip(names, ages)

[('Joe', 30), ('Kate', 28), ('Peter', 41)] 
```

Enter fullscreen mode Exit fullscreen mode

相反，我们可以使用 [**izip**](https://docs.python.org/2/library/itertools.html#itertools.izip) 函数返回一个迭代器，而不是创建一个新的列表:

```
# RIGHT from itertools import izip
# Creates an iterator it = izip(names, ages) 
```

Enter fullscreen mode Exit fullscreen mode

### Python 3 中的新功能

在 Python 3 **中，izip** 函数被移除， **zip** 的行为类似于 Python 2.x 中的 **izip** 函数

## 使用发电机

列表理解是 Python 中一个强大的工具，但是由于它可以使用大量的内存，因为每次列表理解都会创建一个新的列表:

```
# WRONG 
# Original list lst = range(10)
# This will create a new list lst_1 = [i + 1 for i in lst]
# This will create another list lst_2 = [i ** 2 for i in lst_1] 
```

Enter fullscreen mode Exit fullscreen mode

避免这种情况的一种方法是使用生成器而不是列表理解。语法上的差别很小:您应该使用圆括号而不是方括号，但是差别是至关重要的。以下示例不创建任何中间列表:

```
# RIGHT 
# Original list lst = range(10)
# Won't create a new list lst_1 = (i + 1 for i in lst)
# Won't create another list lst_2 = (i ** 2 for i in lst_1) 
```

Enter fullscreen mode Exit fullscreen mode

如果您可能只需要处理结果集合的一部分来获得一个结果，比如说找到匹配某个条件的第一个元素，这就特别方便。

# 习惯性使用字典

## 避免使用按键()功能

如果你需要迭代字典中的键，你可能倾向于在哈希映射上使用 **keys** 函数:

```
# WRONG for k in d.keys():
    print k 
```

Enter fullscreen mode Exit fullscreen mode

但是有一个更好的方法，你使用 iterate over a dictionary 它对它的键进行迭代，所以你可以简单地做:

```
# RIGHT for k in d:
    print k 
```

Enter fullscreen mode Exit fullscreen mode

这不仅可以节省你的输入时间，还可以避免像 **keys** 方法那样创建 dict 中所有键的副本。

## 迭代键和值

如果你使用 **keys** 方法，在这样的字典中迭代键和值真的很容易:

```
 #WRONG for k in d:
    v = d[k]
    print k, v 
```

Enter fullscreen mode Exit fullscreen mode

但是有更好的方法。您可以使用从字典中返回键值对的 **items** 函数:

```
# RIGHT for k, v in d.items():
    print k, v 
```

Enter fullscreen mode Exit fullscreen mode

这种方法不仅更简洁，而且效率更高。

## 用字典理解

创建字典的一种方法是逐个给它赋值:

```
# WRONG 
d = {}
for person in people:
    d[person.name] = person 
```

Enter fullscreen mode Exit fullscreen mode

相反，你可以用字典理解把它变成一行:

```
# RIGHT d = {person.name: person for person in people} 
```

Enter fullscreen mode Exit fullscreen mode

# 使用收款模块

## 使用命名元组

如果你需要一个类似结构的类型，你可以用一个 ****init**** 方法和一堆字段来定义一个类:

```
# WRONG class Point(object):
    def __init__(self, x, y):
        self.x = x
        self.y = y 
```

Enter fullscreen mode Exit fullscreen mode

然而，Python 库中的 [**集合**](https://docs.python.org/2/library/collections.html) 模块提供了一个 [**名为 tuple**](https://docs.python.org/2/library/collections.html#collections.namedtuple) 的类型，将它变成了一行程序:

```
# RIGHT from collections import namedtuple
Point = namedtuple('Point', ['x', 'y']) 
```

Enter fullscreen mode Exit fullscreen mode

此外**命名的 duple**实现了 **__str__** 、 **__repr__** 和 **__eq__** 方法:

```
>>> Point(1, 2)
Point(x=1, y=2)
>>> Point(1, 2) == Point(1, 2)
True 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 defaultdict

如果我们需要统计一个元素在一个集合中出现的次数，我们可以使用一种常见的方法:

```
# WRONG d = {}
for v in lst:
    if v not in d:
        d[v] = 1
    else:
        d[v] += 1 
```

Enter fullscreen mode Exit fullscreen mode

**collections** 模块为这种情况提供了一个非常方便的类，叫做 **defaultdict** 。它的构造函数接受一个函数，该函数将用于计算一个不存在的键的值:

```
>>> d = defaultdict(lambda: 42)
>>> d['key']
42 
```

Enter fullscreen mode Exit fullscreen mode

为了重写计数示例，我们可以将 **int** 函数传递给 **defaultdict** ，如果不带参数调用该函数，则返回零:

```
# RIGHT from collections import defaultdict
d = defaultdict(int)
for v in lst:
    d[v] += 1 
```

Enter fullscreen mode Exit fullscreen mode

当你需要在一个集合中创建任何类型的项目分组时，defaultdict 是有用的，但是你只需要得到元素的计数，你可以使用**计数器**类来代替:

```
# RIGHT from collections import Counter

>>> counter = Counter(lst)
>>> counter
Counter({4: 3, 1: 2, 2: 1, 3: 1, 5: 1}) 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章最初发表在[酿造代码](https://brewing.codes/2017/01/12/python-data-structures-idioms/)博客上。**