# 动态规划-最长公共子序列

> 原文：<https://dev.to/ankursheel/dynamic-programming---longest-common-subsequence>

*本文已从[这里](http://ankursheel.com/blog/dynamic-programming-longest-common-subsequence/)T3 交叉发布*

在本文中，我们将看看如何使用在[简介文章](http://ankursheel.com/blog/dynamic-programming-series-introduction/)中提到的步骤来获得最长公共子序列问题的动态规划解决方案。

* * *

# **【LCS】**

**问题陈述:**给定两个序列，求两个序列中存在的最长子序列的长度。子序列是以相同的相对顺序出现的序列，但不一定是连续的。**例子:**

1.  输入序列“ABCDGH”和“AEDFHR”的 LCS 是“ADH”
2.  LCS 对于输入序列“人类”和“黑猩猩”是“HMAN”

最长公共子序列用于解决一些问题，例如计算两个 DNA 序列有多相似；以及比较同一文件的两个不同版本。你可以在这里阅读更多关于最长公共序列[的信息。](https://en.wikipedia.org/wiki/Longest_common_subsequence_problem)

* * *

# **剖析**

正如在[介绍文章](http://ankursheel.com/blog/dynamic-programming-series-introduction/)中提到的，我们将使用[谷歌基准](https://github.com/google/benchmark)来帮助描述我们的解决方案。如果你想了解更多关于我们将如何使用 google benchmark，你可以阅读[的介绍文章](http://ankursheel.com/blog/dynamic-programming-series-introduction/)。

对于 LCS 问题，最坏的情况发生在序列之间不匹配的时候。出于本文的目的，我们将通过创建两个长度为 ***n*** 的字符串来分析最坏的情况，并用***‘a’***填充其中一个，用**‘b’**填充另一个。然后，我们将尝试查看函数返回需要多长时间。 ***n*** 将是基准名称的一部分。

如果您想了解基准测试的运行情况，您可以在这里找到代码。

* * *

# **步骤**

在[介绍文章](http://ankursheel.com/blog/dynamic-programming-series-introduction/)中，我们提出了以下步骤来找到解决我们问题的动态编程方法

1.  找出重叠的子问题。
2.  从递归解决方案开始
3.  修改递归解决方案以使用自顶向下的记忆版本。
4.  通过使递归成为迭代解决方案来消除递归。
5.  如果不需要保留之前的所有结果，只保留需要的结果。

* * *

## **重叠子问题**

设 2 根弦的长度为**X***T3】mT5】和 **Y** 长度为*T9】nT11】设 **LCS(i，j)** 为 LCS 的长度。然后我们可以将问题形式化如下** 

```
LCS(i,j) = 0 if X[i] = '\0' || Y[j] = '\0'
LCS(i,j) = 1 + LCS(i+1, j+1) if X[i] = Y[j]
LCS(i,j) = max(LCS(i+1, j), LCS(i, j+1) if X[i] != Y[j] 
```

Enter fullscreen mode Exit fullscreen mode

最长公共子序列的长度将是 **LCS(0，0)** 。

* * *

## **朴素递归方法**

上述形式可以很容易地转换成下面的递归方法

```
int cLCS::Recursive(const char* const first, const char* const second)
{
  if (*first == '\0' || *second == '\0')
  {
    return 0;
  }
  if (*first == *second)
  {
    return 1 + Recursive(first + 1, second + 1);
  }
  return max(Recursive(first + 1, second), Recursive(first, second + 1));
} 
```

Enter fullscreen mode Exit fullscreen mode

在递归方法中，很难得到实际的 LCS 字符串，所以我们只返回 LCS 的长度。

* * *

## **带记忆的自顶向下递归方法**

LCS 子问题由两个输入字符串的一对后缀组成。为了存储和查找子问题的解，我们可以使用一个 2d 数组。我们将使用一个 ***-1*** 来告诉算法还没有存储任何东西。

```
string cLCS::Memonized(const string& first, const string& second)
{
  int length1 = first.length();
  int length2 = second.length();
  if (length1 == 0 || length2 == 0)
  {
    return "";
  }
  Memonized(first.data(), second.data(), 0, 0);
  return GetText(first, second);
}

int cLCS::Memonized(const char* const first, const char* const second, int i, int j)
{
  if (m_results[GetIndex(i, j)] == -1)
  {
    if (first[i] == '\0' || second[j] == '\0')
    {
      m_results[GetIndex(i, j)] = 0;
    }
    else if (first[i] == second[j])
    {
      m_results[GetIndex(i, j)] = 1 + Memonized(first, second, i + 1, j + 1);
    }
    else
    {
      int val1 = Memonized(first, second, i + 1, j);
      int val2 = Memonized(first, second, i, j + 1);
      m_results[GetIndex(i, j)] = max(val1, val2);
    }
  }
  return m_results[GetIndex(i, j)];
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的算法中，***【m _ results(0)***，给出了 LCS 的长度。我们将在下一节中查看 ***GetText()*** ，看看如何在计算完 ***m_results*** 数组后得到子序列。

* * *

## **得到子序列**

一旦我们填充了 ***m_results*** 数组，我们就可以通过向前遍历数组来找到序列。

```
string cLCS::GetText(const string& first, const string& second)
{
  if (m_results[0] == 0)
  {
    return "";
  }
  int i = 0;
  int j = 0;
  stringstream ss("");
  while (i < first.length() && j < second.length())
  {
    if (first[i] == second[j])
    {
      ss << first[i];
      i++;
      j++;
    }
    else if (m_results[GetIndex(i + 1, j)] >= m_results[GetIndex(i, j + 1)])
    {
      i++;
    }
    else
    {
      j++;
    }
  }
  return ss.str();
} 
```

Enter fullscreen mode Exit fullscreen mode

为了找到最长的公共子序列，我们分别使用索引 ***i*** 和 ***j*** 遍历两个字符串- ***第一个*** 和 ***第二个*** 。如果第一个***【I】=第二个【j】***，那么我们将这个字符添加到结果字符串中，并同时递增 ***i*** 和 ***j*** 。如果不匹配，这意味着子序列是通过删除第一个***【I】***或第二个【j】形成的。如果***m _ results[I+1][j]β>=βm _ results[I][j+1]***，这就意味着子序列是先删除***【I】***后形成的。否则就是删除了 ***第二个【j】***形成的。这样继续下去，我们可以得到公共子序列。

* * *

## **用动态规划的自下而上方法**

为了提出一种 DP 方法，我们只需通过反向遍历数组来改变存储结果的方式。

```
string cLCS::DP(const std::string& first, const std::string& second)
{
  int length1 = first.length();
  int length2 = second.length();
  if (length1 == 0 || length2 == 0)
  {
    return "";
  }

  for (int i = length1; i >= 0; i--)
  {
    for (int j = length2; j >= 0; j--)
    {
      if (first[i] == '\0' || second[j] == '\0')
      {
        m_results[GetIndex(i, j)] = 0;
      }
      else if (first[i] == second[j])
      {
        m_results[GetIndex(i, j)] = 1 + m_results[GetIndex(i + 1, j + 1)];
      }
      else
      {
        m_results[GetIndex(i, j)] = max(m_results[GetIndex(i + 1, j)], m_results[GetIndex(i, j + 1)]);
      }
    }
  }
  return GetText(first, second);
} 
```

Enter fullscreen mode Exit fullscreen mode

自底向上的方法优于记忆的缺点是，即使问题可以通过计算数组的一部分来解决，这种方法也会填充整个数组。

* * *

## **自下而上的动态规划方法(优化)**

我们可以优化上面的解决方案，因为一旦我们计算了数组 ***m_results*** 的行 ***i*** ，我们就不再需要 ***i + 1*** 的值。然而，我们不能使用这种方法重新创建子序列，因此我不会在这里展示它。

* * *

# **结论**

如果您想查看代码或者自己运行基准测试，您可以在这里找到代码[—](https://github.com/AnkurSheel/DynamicProgramming)

在本系列的下一篇文章中，我们将研究可以通过动态编程解决的另一个问题。

你以前试过动态编程吗？你的体验如何？请在评论中告诉我。