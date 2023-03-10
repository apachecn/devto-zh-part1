# 编写自己的 diff 以获得乐趣

> 原文：<https://dev.to/dzeban/write-your-own-diff-for-fun-3ckl>

前几天，当我在看`git diff`的时候，我想“它是如何工作的？”。比较所有可能的线对的强力想法看起来效率不高，并且实际上它具有指数级的算法复杂度。一定有更好的方法，对吗？

结果是，`git diff`，像一个普通的`diff`工具一样，被建模为一个叫做最长公共子序列的问题的解决方案。这个想法真的很巧妙——当我们试图区分两个文件时，我们把它看作两个行序列，并试图找到一个最长的公共子序列。那么任何不在这个子序列中的东西就是我们的差异。听起来不错，但是如何有效地实现它(没有指数级的复杂性)？

LCS 问题是一个经典问题，最好用动态编程来解决——这是算法设计中的一种高级技术，大致意思是记忆化的迭代。

我一直在与动态编程作斗争，因为它主要是通过一些(在我看来)对我来说很难解决的人为问题出现的。但是现在，当我看到一些非常有用的东西可以帮助我写一个 diff 时，我就是无法抗拒。

我使用了维基百科上一篇关于 LCS 的文章作为我的指南，所以如果你想检查算法的本质，请点击链接。我将向您展示我的实现(当然是 GitHub 上的[)，以展示您可以多么轻松地解决这种看似困难的问题。](https://github.com/alexdzyoba/diff)

我选择 Python 来实现它，并立即感到欣慰，因为您可以复制粘贴伪代码，并通过最小的更改来使用它。下面是维基百科文章中的 diff 打印函数的伪代码:

```
function printDiff(C[0..m,0..n], X[1..m], Y[1..n], i, j)
    if i > 0 and j > 0 and X[i] = Y[j]
        printDiff(C, X, Y, i-1, j-1)
        print " " + X[i]
    else if j > 0 and (i = 0 or C[i,j-1] ≥ C[i-1,j])
        printDiff(C, X, Y, i, j-1)
        print "+ " + Y[j]
    else if i > 0 and (j = 0 or C[i,j-1] < C[i-1,j])
        printDiff(C, X, Y, i-1, j)
        print "- " + X[i]
    else
        print "" 
```

Enter fullscreen mode Exit fullscreen mode

而在 Python 中:

```
def print_diff(c, x, y, i, j):
    """Print the diff using LCS length matrix by backtracking it"""

    if i >= 0 and j >= 0 and x[i] == y[j]:
        print_diff(c, x, y, i-1, j-1)
        print("  " + x[i])
    elif j >= 0 and (i == 0 or c[i][j-1] >= c[i-1][j]):
        print_diff(c, x, y, i, j-1)
        print("+ " + y[j])
    elif i >= 0 and (j == 0 or c[i][j-1] < c[i-1][j]):
        print_diff(c, x, y, i-1, j)
        print("- " + x[i])
    else:
        print("") 
```

Enter fullscreen mode Exit fullscreen mode

这不是我的 diff printing 的实际函数，因为它不处理少数极端情况——它只是为了说明 Python 的神奇之处。

区分的本质是构建包含所有子序列长度的矩阵`C`。构建它可能会令人望而生畏，直到您开始查看简单的案例:

*   LCS 的“一”和“一”就是“一”。
*   LCS 的“AA”和“AB”是“A”。
*   LCS 的“AAA”和“ABA”是“AA”。

迭代构建我们可以定义 LCS 函数:

*   2 个空序列的 LCS 是空序列。
*   “${prefix1}A”和“${prefix2}A”的 LCS 是 LCS(${prefix1}，${prefix2}) + A
*   “${prefix1}A”和“${prefix2}B”的 LCS 是 LCS(${prefix1}A，${prefix2})和 LCS(${prefix1}，${prefix2}B)中最长的

这基本上是动态编程的核心——从简单的基础案例开始迭代地构建解决方案。但是，请注意，只有当问题具有所谓的“最优”结构时，它才起作用，这意味着它可以通过重用以前的记忆步骤来构建。

下面是为所有子序列构建长度矩阵的 Python 函数:

```
def lcslen(x, y):
    """Build a matrix of LCS length.

    This matrix will be used later to backtrack the real LCS.
    """

    # This is our matrix comprised of list of lists.
    # We allocate extra row and column with zeroes for the base case of empty
    # sequence. Extra row and column is appended to the end and exploit
    # Python's ability of negative indices: x[-1] is the last elem.
    c = [[0 for _ in range(len(y) + 1)] for _ in range(len(x) + 1)]

    for i, xi in enumerate(x):
        for j, yj in enumerate(y):
            if xi == yj:
                c[i][j] = 1 + c[i-1][j-1]
            else:
                c[i][j] = max(c[i][j-1], c[i-1][j])
    return c 
```

Enter fullscreen mode Exit fullscreen mode

有了 LCS 长度的矩阵，我们现在可以通过回溯建立实际的 LCS。

```
def backtrack(c, x, y, i, j):
    """Backtrack the LCS length matrix to get the actual LCS"""

    if i == -1 or j == -1:
        return ""
    elif x[i] == y[j]:
        return backtrack(c, x, y, i-1, j-1) + x[i]
    else:
        if c[i][j-1] > c[i-1][j]:
            return backtrack(c, x, y, i, j-1)
        else:
            return backtrack(c, x, y, i-1, j) 
```

Enter fullscreen mode Exit fullscreen mode

但是对于 diff，我们不需要实际的 LCS，我们需要相反的。所以差异打印实际上是稍微改变了回溯函数，增加了两种情况来改变序列的头部:

```
def print_diff(c, x, y, i, j):
    """Print the diff using LCS length matrix by backtracking it"""

    if i < 0 and j < 0:
        return ""
    elif i < 0:
        print_diff(c, x, y, i, j-1)
        print("+ " + y[j])
    elif j < 0:
        print_diff(c, x, y, i-1, j)
        print("- " + x[i])
    elif x[i] == y[j]:
        print_diff(c, x, y, i-1, j-1)
        print("  " + x[i])
    elif c[i][j-1] >= c[i-1][j]:
        print_diff(c, x, y, i, j-1)
        print("+ " + y[j])
    elif c[i][j-1] < c[i-1][j]:
        print_diff(c, x, y, i-1, j)
        print("- " + x[i]) 
```

Enter fullscreen mode Exit fullscreen mode

为了调用它，我们将输入文件读入 Python 字符串列表，并将其传递给我们的 diff 函数。我们还添加了一些常见的 Python 节:

```
def diff(x, y):
    c = lcslen(x, y)
    return print_diff(c, x, y, len(x)-1, len(y)-1)

def usage():
    print("Usage: {} <file1> <file2>".format(sys.argv[0]))

def main():
    if len(sys.argv) != 3:
        usage()
        sys.exit(1)

    with open(sys.argv[1], 'r') as f1, open(sys.argv[2], 'r') as f2:
        diff(f1.readlines(), f2.readlines())

if __name__ == '__main__':
    main() 
```

Enter fullscreen mode Exit fullscreen mode

就这样:

```
$ python3 diff.py f1 f2
+ """Simple diff based on LCS solution"""
+ 
+ import sys
  from lcs import lcslen

  def print_diff(c, x, y, i, j):
+ """Print the diff using LCS length matrix by backtracking it"""
+ 
       if i >= 0 and j >= 0 and x[i] == y[j]:
           print_diff(c, x, y, i-1, j-1)
           print(" " + x[i])
       elif j >= 0 and (i == 0 or c[i][j-1] >= c[i-1][j]):
           print_diff(c, x, y, i, j-1)
- print("+ " + y[j])
+ print("+ " + y[j])
       elif i >= 0 and (j == 0 or c[i][j-1] < c[i-1][j]):
           print_diff(c, x, y, i-1, j)
           print("- " + x[i])
       else:
- print("")
- 
+ print("") # pass? 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 https://github.com/alexdzyoba/diff 查看完整的源代码。

就是这样。下次见！