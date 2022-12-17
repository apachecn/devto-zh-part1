# Python 中最让你困惑的是什么？

> 原文：<https://dev.to/r0f1/what-is-the-most-confusing-thing-to-you-in-python>

只是一些陷阱和陷阱，我花了一段时间来适应 Python。

### 列表方法与内置方法

```
# sort(), sorted(), reverse(), reversed()
# sort() modifies the existing list and is a function of the list object
# sorted() returns a new list and is a built-in function 
a = [5,2,8,1,9,3]
sorted(a) # ok -> [1, 2, 3, 5, 8, 9] a.sort()  # ok -> [1, 2, 3, 5, 8, 9] a.sorted() # ERROR 
# however, min() and max() are only built-in functions a.min() # ERROR min(a) # ok -> 1 
# unless you work with numpy, where min() and max() do exist :S import numpy as np
n = np.array([5,2,4,9,1,7])
n.min() # ok -> 1 
```

Enter fullscreen mode Exit fullscreen mode

### string.join()

```
# I have to call .join() on a string to concatenate a list = confusing. 
# concatenating a list of strings ", ".join(["apple", "banana", "cherry"]) # 'apple, banana, cherry' 
# even uglier, when list is not a string ", ".join([str(s) for s in [1,2,3,4]])   # '1, 2, 3, 4' 
```

Enter fullscreen mode Exit fullscreen mode

### 列表列表

```
 # The wrong way a = [[]] * 5
# [[], [], [], [], []] a[0].append(1)
# [[1], [1], [1], [1], [1]] 
# The right way a = [[] for _ in range(5)] 
```

Enter fullscreen mode Exit fullscreen mode

### '是'和' == '整数

```
# Internally some int objects are cached. When you compare 2 ints, you should use ==. 
# However, using 'is' can also work in some cases. 
x = 2000
y = 2000
x is y # False -> good, because this should not be allowed anyways. 
x = 5
y = 5
x is y # True -> But this works....
# See also
# https://stackoverflow.com/questions/306313/is-operator-behaves-unexpectedly-with-integers 
```

Enter fullscreen mode Exit fullscreen mode

你在 Python 中有没有发现什么有趣或搞笑的东西，让你第一次看到的时候很困惑？请在下面的评论中分享它们。