# Python 中 4 个有用的东西

> 原文：<https://dev.to/r0f1/4-useful-things-in-python>

只是我想到的一些事情。自从我的上一篇文章得到了一些关注，我想我应该多写一些我在 Python 中遇到的事情。这些东西最初来自 Java，大部分对我来说都是一个惊喜。

### 颚化符

```
# You can index lists from the front a = ['a', 'b', 'c', 'd', 'e']

print(a[0]) # 'a' print(a[1]) # 'b' 
# You can also index them from the back
# starting with -1 and going backwards print(a[-1]) # 'e' print(a[-2]) # 'd' 
# The tilde operator when applied to a number `x` calculates `-x - 1`, 
# which can be useful for list indexing 
#
# x  ~x  
# 0  -1
# 1  -2
# 2  -3
# 3  -4 
# 4  -5 
print(a[~0]) # 'e' = 0th element from the back print(a[~1]) # 'd' = 1st from the back 
```

Enter fullscreen mode Exit fullscreen mode

[所以链接](https://stackoverflow.com/a/8305225%5D)

### 【0】和 0

```
# Consider this function def foo(data):
    """Prints the status of my list."""
    if data:              # Don't do this
        print("The list contains some items.")
    else:
        print("We got an empty list.")

foo([]) # ok -> "We got an empty list." foo([1,2,3,4]) # ok -> "The list contains some items." 
# Empty lists are treated as false in Python.
# However, False and 0 are also false, which can lead to some 
# unexpected behavior if your client, does not know that you 
# expect him to pass a list. 
foo([0])   # still ok -> "The list contains some items." foo(0)     # NOT ok -> "We got an empty list." foo(False) # NOT ok -> "We got an empty list." 
def foo(data):
    if len(data) > 0:     # Safer version
        print("The list contains some items.")
    else:
        print("We got an empty list.") 
```

Enter fullscreen mode Exit fullscreen mode

### 默认值

Python 中的默认值不应该是可变的，比如列表或字典。默认值在其所属的`def`语句执行时被评估。也就是说，它们只被评估一次。

```
# Don't do this def foo(bar=[]): 
    bar.append(1)
    return bar

foo() # returns [1] --> ok foo() # returns [1,1] --> not ok = the same list as before foo() # returns [1,1,1] --> also not ok = again, the same list 
# Do this instead def foo(bar=None):
    if bar is None:
        bar=[]
    ... 
```

Enter fullscreen mode Exit fullscreen mode

### 很多方式调用同一个函数

```
def draw_line(length, type="solid", color="black"):
    print(length, type, color)

draw_line(5)                                # 1) 5 solid black draw_line(length=5)                         # 2) 5 solid black draw_line(5, "dashed")                      # 3) 5 dashed black draw_line(5, "dashed", "green")             # 4) 5 dashed green draw_line(5, type="dashed", color="green")  # 5) 5 dashed green 
# Can we take this a step further? Definitely. draw_line(type="dashed", length=5)          # 6) 5 dashed black 
args = [5] # could also be a tuple (5,) draw_line(*args)                            # 7) 5 solid black 
args = {"length": 5, "color": green} 
draw_line(**args)                           # 8) 5 solid green 
```

Enter fullscreen mode Exit fullscreen mode

[一些解释](https://docs.python.org/3/tutorial/controlflow.html#unpacking-argument-lists)

### 更多关于函数

```
# Pre-fill functions with the arguments you use often 
def draw_line(length, type="solid", color="black"):
    print(length, type, color)

from functools import partial

draw_green_line = partial(draw_line, color="green")
draw_green_line(5)                          # 5 solid green 
draw_unit_line = partial(draw_line, length=1)
draw_unit_line()                            # 1 solid black 
# If you want to use keyword arguments but you do not want
# to provide default values, you can do that as well 
def draw_line(*, length, type, color): # all three are required
    ...

# Allow some positional arguments, but forbid 3) and 4) def draw_line(length, *, type="solid", color="black"): # only length is required
    ... 
```

Enter fullscreen mode Exit fullscreen mode

关于如何调用函数还有什么想法吗？你还知道什么 pythonic 编码的东西，在过去对你有帮助吗？下面评论。

[相关神秘链接](https://codegolf.stackexchange.com/questions/54/tips-for-golfing-in-python)