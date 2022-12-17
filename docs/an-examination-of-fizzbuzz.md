# 对 Fizzbuzz 的检查

> 原文：<https://dev.to/zeeter/an-examination-of-fizzbuzz>

## 目录

*   [1。简介](#orgad2678a)
*   [2。第一次尝试](#org409ad2f)
*   [3。第二次尝试](#org248163d)

# 简介

嘶嘶嗡嗡声问题是一种编程挑战，用来显示一个人是否知道如何编程。挑战基本上是这样的，你必须创建一个程序，打印 3 的倍数的 fizz，5 的倍数的 buzz，
以及 fizz 和 buzz 的倍数。
*编辑*我忘了说，如果 n 既不是 3 的倍数，也不是 5 的倍数，它只打印数字。

这些是我在这次挑战中的尝试。这篇文章还在继续，因为我有了新的尝试。

# 第一次尝试

```
for n in range(1,101):
    cond1 = n % 3 == 0
    cond2 = n % 5 == 0

    if cond1 and not cond2:
        print("fizz")
    elif cond2 and not cond1:
        print("buzz")
    elif cond1 and cond2:
        print("fizz buzz")
    else:
        print(n) 
```

Enter fullscreen mode Exit fullscreen mode

所以第一次尝试可以根据需要工作。然而，该方法并不完全支持更改。
然而，我确实是根据条件做出了选择，然而增加额外的条件会很痛苦。
同样，代码会在您运行它的时候执行，这在 python 领域不是一个很好的做法。

# 第二次尝试

```
import sys
import argparse

def if_multiple(n, multiple, string):
    if n % multiple == 0:
        return string
    else:
        return ''

def n_if_empty(output, n):
    if output == '':
        return str(n)
    else:
        return ''

def fizzbuzz(start=1,
          stop=101):

    for n in range(start,stop):

        output = ''
        output += if_multiple(n,3,"fizz")
        output += if_multiple(n,5,"buzz")
        output += n_if_empty(output, n)
        print(output)

def main():
    parser = argparse.ArgumentParser(description="A program that plays fizzbuzz.")
    parser.add_argument('start', type=int, help="The number to count from (Default 1)", default=1)
    parser.add_argument('stop', type=int, help="The number to count to (Default: 100)", default=100)

    args = parser.parse_args()

    fizzbuzz(args.start, (args.stop + 1))

    return None

if __name__ == '__main__':
    main()
    sys.exit() 
```

Enter fullscreen mode Exit fullscreen mode

第二个版本在我看来更好。它不再依赖于混乱的 else if 逻辑，取而代之的是尽管我没有通过 autopep8 运行这段代码，所以它不一定是 pythonic 化的。然而，这个版本相当整洁。如果需要，您可以导入 fizzbuzz 功能，也可以导入其他功能。添加新的逻辑就像添加新的一行一样简单。而改变要打印的字符串就像改变一个变量一样简单。

我从代码块中移除了 bash 调用，但我仍然没有完全弄清楚 org markdown 导出。这段代码应该没有任何语法错误。