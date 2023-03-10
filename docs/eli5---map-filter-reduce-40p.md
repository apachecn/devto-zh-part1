# ELI5 -“映射-过滤-减少”

> 原文：<https://dev.to/__namc/eli5---map-filter-reduce-40p>

我最近接触的许多开发人员将 map-reduce-filter 与函数式编程联系在一起，他们实际上害怕这种组合。这是他们避免使用函数式语言的原因之一，因为**上帝禁止他们学习如何使用 map、reduce 和 filter**——Ruby、Python、Javascript、PHP、Erlang、Scala 和许多其他语言中的 3 个函数。

那么贴图-减少-过滤到底有什么大不了的呢？

> 它们只是函数式编程中使用的高阶函数。它们可以处理任何可迭代的东西(列表、元组、集合、字典，甚至字符串)。

让我们考虑一个问题陈述，以便能够更清楚地理解。(为了方便起见，我们使用 Python，但是我将添加一个要点，说明如何在各种其他语言中做同样的事情)

**问题陈述:
我们需要[0，9]** 范围内所有整数的偶平方和

### 贴图(变换)

这个函数接受一个一元函数和一个列表。一元函数是接受一个变量的函数。map 函数的结果是一个相同大小的转换值列表。值的转换取决于带参数的一元函数的定义。

**在**时使用:您想要将一个集合中的所有元素转换为另一个值集合。

**参数**:一元变换函数&要变换的数组

为了解决问题陈述，比方说，我们的第一步是在数字[0，9]
的范围上映射计算平方的函数

```
>>> nums = map(lambda x : x*x, [i for i in range(10)])
>>> nums
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81] 
```

Enter fullscreen mode Exit fullscreen mode

Lambdas 通常用来定义映射函数。您也可以使用常规函数。

### 滤镜

这个函数接受一个谓词(一个返回布尔值的一元函数)和一组值；并给出一个结果集，其中只包含谓词返回 true 的那些值集。结果集的长度可能相同，也可能不同。

**在**时使用:您想要根据条件从集合中删除元素。

**参数**:一元谓词(应该只返回一个布尔值)&要过滤的数组。

在上述问题的背景下，让我们试着从列表中移除所有的奇数方块。为此，如果元素是偶数，让我们的谓词返回 true。

```
>>> def is_even(x):
        return x % 2 == 0
>>> evens = filter(is_even, nums)
>>> evens
[0, 4, 16, 36, 64] 
```

Enter fullscreen mode Exit fullscreen mode

理解通常使编写过滤器更容易。这些也可以用来代替函数。

### 减少(积累)

这个函数采用一个二元函数和一组值，它产生一个单一的值。它通过对一组值运行二元函数，并将它们累加成一个值来实现这一点。

**在**时使用:您想要使用给定的一组值的累加值或级联值。

**参数**:二元函数和值的集合。

在上述问题的背景下，我们将把所有的偶数平方相加。

```
>>> reduce(lambda x,y : x+y, evens)
120 
```

Enter fullscreen mode Exit fullscreen mode

如果我们看看 functools 模块中 reduce 的定义，

```
def reduce(f,alist)
    if alist == []:
        return None

    answer = alist[0]
    for v in alist[1::
        answer = f(answer,v)
    return v 
```

Enter fullscreen mode Exit fullscreen mode

我们看到 reduce 函数从设置 answer 作为数组的第一个元素开始。然后它从左到右遍历数组，对每个元素调用一个回调函数。累积值在回调之间传递。遍历完所有元素后，返回累积值。

### 结合贴图-过滤-还原

我们看到上面写了很多小函数，那只是为了理解。高阶函数的真正威力在于组合。所以我们的问题陈述可以用一行代码解决😅

```
>>> reduce(lambda a, b: a + b, map(lambda x: x * x, filter(lambda y: not y % 2, range(10))))
120 
```

Enter fullscreen mode Exit fullscreen mode

不要慌！！为了简单起见，我们可以将其分解。

```
f_add = lambda a, b: a + b
f_square = lambda x: x * x
f_is_even = lambda y: not y % 2 
```

Enter fullscreen mode Exit fullscreen mode

现在复杂的表达式可以重写为

```
>>> reduce(f_add , map(f_square , filter(f_is_even, range[0, 10])) 
```

Enter fullscreen mode Exit fullscreen mode

**作为旁注，使用贴图没有限制，滤镜&一起减少。您可以自己组合，并根据需要对功能进行排序。**

### 其他语言

保持问题陈述与上面相同，看看 map-reduce-filter 如何在其他编程语言中使用，这根本不是一门火箭科学🚀

#### 红宝石

```
def main()

    nums = [*0..9]

    ans = nums.map{|x| x * x}              # map operation (x*x) over nums
              .select{|x| x % 2 == 0}      # select event elements
              .reduce{|a, x| a + x}        # reduce the list by `+` operation

    p nums                                 
    p ans                                  

end
main() 
```

Enter fullscreen mode Exit fullscreen mode

#### Clojure

```
(reduce  (fn  [a  b]  (+  a  b))  (map  (fn  [x]  (*  x  x))  (filter  even?  (range  10)))) 
```

Enter fullscreen mode Exit fullscreen mode

#### Javascript

(我真的不擅长这个😕)

```
let numbers = [ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 ]

ans = numbers 
    .filter(function(num) {
        return (num%2 === 0);
    })
    .map(function(num) { 
        return num*num;
    })
    .reduce(function(total, num){
        return total += num;
    }); 
```

Enter fullscreen mode Exit fullscreen mode

在其他语言中也尝试一下吧！