# 动态编程-编辑(Levenshtein)距离

> 原文：<https://dev.to/ankursheel/dynamic-programming--editlevenshtein-distance>

*本文已从[这里](http://ankursheel.com/blog/dynamic-programming-editlevenshtein-distance/)T3 交叉发布*

在本文中，我们将看看如何使用在[简介文章](http://ankursheel.com/blog/dynamic-programming-series-introduction/)中提到的步骤来实现编辑距离问题的动态编程解决方案。

* * *

# **编辑距离**

**问题陈述:**给定两个不同长度(m，n)的输入字符串(S1，S2)，我们需要使用最少次数(或最小代价)的编辑操作将第一个字符串转换成第二个字符串。可以用第二个字符串中的字符替换第一个字符串中的现有字符，删除现有字符或将新字符插入第一个字符串。**例子:**

1.  输入序列*“星期日”*和*“星期六”*的编辑距离为 3。最后三个字符和第一个字符相同。我们需要将*【un】*转换为*【atur】*。这可以通过插入字符*‘a’*，插入字符‘t’并用字符*‘r’*替换字符*‘n’*来实现
2.  输入序列*“猫”*和*“汽车”*的编辑距离为 2。前两个字符是相同的。我们需要将*‘t’*转换成*‘RS’*。这可以通过插入字符*‘r’来实现，并将字符‘t’替换为字符‘s’。*

编辑距离可用于拼写检查和光学字符识别的校正系统。你可以在这里阅读更多关于编辑距离[的信息。](https://en.wikipedia.org/wiki/Edit_distance)

* * *

# **剖析**

正如在[介绍文章](http://ankursheel.com/blog/dynamic-programming-series-introduction/)中提到的，我们将使用[谷歌基准](https://github.com/google/benchmark)来帮助描述我们的解决方案。如果你想了解更多关于我们将如何使用 google benchmark，你可以阅读[的介绍文章](http://ankursheel.com/blog/dynamic-programming-series-introduction/)。对于编辑距离问题，最坏的情况发生在序列之间不匹配的时候。出于本文的目的，我们将通过创建两个长度为 ***n*** 的字符串来分析最坏的情况，并用***【a】***填充其中一个，用**【b】**填充另一个。然后，我们将尝试查看函数返回需要多长时间。 ***n*** 将是基准名称的一部分。如果你想看看性能指标评测的运行情况，你可以在这里找到代码。

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

设 2 串为长度*的**X**T3】mT5】和长度*的**Y**nT11】设 EditDistance* *(i，j)* *为运算次数。然后我们可以将问题形式化如下** 

```
EditDistance(i, j) = n - j : if i = 0;
EditDistance(i, j) = m - i : if  j = 0;
EditDistance(i, j) = EditDistance(i + 1, j + 1) if X[i] = Y[j]
EditDistance(i, j) = 1 + min (
                              EditDistance(i,  j + 1) // insert
                              EditDistance(i + 1,  j) // delete
                              EditDistance(i + 1,  j + 1) // replace
                             ) if X[i] != Y[j] 
```

编辑距离的长度将是**编辑距离** **(0，0)** 。

* * *

## **朴素递归方法**

上述形式可以很容易地转换成下面的递归方法

```
int cEditDistance::Recursive(const string& first, const string& second, int index1, int index2)
{
  int length1 = first.length() - index1;
  int length2 = second.length() - index2;

  if (length1 == 0)
  {
    return length2;
  }

  if (length2 == 0)
  {
    return length1;
  }

  if (first[index1] == second[index2])
  {
    return Recursive(first, second, index1 + 1, index2 + 1);
  }
  else
  {
    int insertCost = Recursive(first, second, index1, index2 + 1);
    int deleteCost = Recursive(first, second, index1 + 1, index2);
    int replaceCost = Recursive(first, second, index1 + 1, index2 + 1);

    int minCost = insertCost;
    if (deleteCost < minCost)
    {
      minCost = deleteCost;
    }

    if (replaceCost < minCost)
    {
      minCost = replaceCost;
    }
    return 1 + minCost;
  }
  return -1;
} 
```

在递归方法中，很难获得操作字符串，所以我们只返回操作的数量。

* * *

## **带记忆的自顶向下递归方法**

编辑距离子问题由两个输入字符串的一对后缀组成。为了存储和查找子问题的解，我们可以使用一个 2d 数组。我们将使用一个 ***-1*** 来告诉算法还没有存储任何东西。为了获得实际的操作，我们将使用另一个 2D 数组来存储我们在该阶段执行的操作。

```
string cEditDistance::Memonized(const string& first, const string& second)
{
  int length1 = first.length();
  int length2 = second.length();

  m_results.Init(length1 + 1, length2 + 1);
  m_Operations.Init(length1, length2);
  int operations = Memonized(first, second, 0, 0);
  return GetSteps(first, second, operations);
}

int cEditDistance::Memonized(const string& first, const string& second, int index1, int index2)
{
  if (m_results.GetElement(index1, index2) == -1)
  {
    int length1 = first.length() - index1;
    int length2 = second.length() - index2;

    if (length1 == 0)
    {
      m_results.SetElement(index1, index2, length2);
    }
    else if (length2 == 0)
    {
      m_results.SetElement(index1, index2, length1);
    }
    else if (first[index1] == second[index2])
    {
      m_results.SetElement(index1, index2, Memonized(first, second, index1 + 1, index2 + 1));
      m_Operations.SetElement(index1, index2, 0);
    }
    else
    {
      int insertCost = Memonized(first, second, index1, index2 + 1);
      int deleteCost = Memonized(first, second, index1 + 1, index2);
      int replaceCost = Memonized(first, second, index1 + 1, index2 + 1);

      int minCost = insertCost;
      m_Operations.SetElement(index1, index2, 1);

      if (deleteCost < minCost)
      {
        minCost = deleteCost;
        m_Operations.SetElement(index1, index2, 2);
      }

      if (replaceCost < minCost)
      {
        minCost = replaceCost;
        m_Operations.SetElement(index1, index2, 3);
      }

      m_results.SetElement(index1, index2, 1 + minCost);
    }
  }
  return m_results.GetElement(index1, index2);
} 
```

在上面的算法中，***【m _ results(0)***，给出了运算的次数。我们将在下一节中查看 ***GetSteps()*** ，看看如何在计算完***m _ operations***array 后得到子序列。

* * *

## **得到子序列**

一旦我们填充了 ***m_results*** 数组，我们就可以通过向前遍历数组来找到序列。

```
string cEditDistance::GetSteps(const std::string& first, const std::string& second, int numberOfOperations)
{
  int index1 = 0;
  int index2 = 0;
  stringstream steps;
  stringstream currentString;

  steps << "Number Of Operations : " << numberOfOperations << endl;

  if (numberOfOperations == 0)
  {
    return steps.str();
  }

  steps << "Starting String : " << first << endl;

  while (index1 < first.length() && index2 < second.length())
  {
    if (m_Operations.GetElement(index1, index2) == 0)  // match
    {
      currentString << first[index1];
      index1 = index1 + 1;
      index2 = index2 + 1;
    }
    else if (m_Operations.GetElement(index1, index2) == 1)  // insert
    {
      steps << "Insert character " << second[index2] << " : Current String " << currentString.str();
      currentString << second[index2];
      steps << " : Updated String " << currentString.str() << endl;
      index2 = index2 + 1;
    }
    else if (m_Operations.GetElement(index1, index2) == 2)  // delete
    {
      currentString << first[index1];
      steps << "Delete character " << first[index1] << " : Current String " << currentString.str();
      currentString.seekp(-1, ios_base::end);
      currentString << " ";
      steps << " : Updated String " << currentString.str() << endl;
      currentString.seekp(-1, ios_base::end);
      index1 = index1 + 1;
    }
    else if (m_Operations.GetElement(index1, index2) == 3)  // replace
    {
      steps << "Replace character " << first[index1] << " with character " << second[index2] << " : Current String ";
      currentString << first[index1];
      steps << currentString.str();
      currentString.seekp(-1, ios_base::end);
      currentString << second[index2];
      steps << " : Updated String " << currentString.str() << endl;
      index1 = index1 + 1;
      index2 = index2 + 1;
    }
  }
  if (second.length() - index2 > 0)
  {
    steps << "Current String \"" << currentString.str() << "\" : Insert remaining " << second.length() - index2
          << " characters from the second string at position " << index2 << endl;
  }
  if (first.length() - index1 > 0)
  {
    if (index1 == 0)
    {
      currentString << first;
    }
    steps << "Current String \"" << currentString.str() << "\" : Delete remaining " << first.length() - index1
          << " characters from position " << index1 << endl;
  }
  steps << "Final String : " << second << endl;
  return steps.str();
} 
```

为了找到操作，我们遍历 **m_operations** ，直到 **index1 > = first.length** 或 **index2 > = second.length** ，因为在这一点上，所有剩余的字符都需要插入或删除。如果有匹配的，那么

*   如果在 **m_operations** 的元素指示一个**匹配**，我们增加**索引 1** 和**索引 2** 。
*   如果在 **m_operations** 处的元素表示一个**插入，那么我们在* *秒【索引 2】**处插入字符，并增加**索引 2。**
*   如果在 **m_operations** 处的元素指示一个**删除，我们首先删除在* *处的字符【索引 1】**并增加**索引 1。**
*   如果在 **m_operations** 处的元素表示一个**替换**，我们用**第二个【index2】替换第一个**【index 1】**处的字符，并增加**index1** 和 **index2。**

这样继续下去，我们就可以进行手术了。

* * *

## **用动态规划的自下而上方法**

为了提出一种 DP 方法，我们只需通过反向遍历字符串来改变存储结果的方式。

```
string cEditDistance::DP(const std::string& first, const std::string& second)
{
  int length1 = first.length();
  int length2 = second.length();

  m_results.Init(length1 + 1, length2 + 1);

  for (int index1 = length1; index1 >= 0; index1--)
  {
    for (int index2 = length2; index2 >= 0; index2--)
    {
      if (index1 == length1)
      {
        m_results.SetElement(index1, index2, length2 - index2);
      }
      else if (index2 == length2)
      {
        m_results.SetElement(index1, index2, length1 - index1);
      }
      else if (first[index1] == second[index2])
      {
        m_results.SetElement(index1, index2, m_results.GetElement(index1 + 1, index2 + 1));
        m_Operations.SetElement(index1, index2, 0);
      }
      else
      {
        int insertCost = m_results.GetElement(index1, index2 + 1);
        int deleteCost = m_results.GetElement(index1 + 1, index2);
        int replaceCost = m_results.GetElement(index1 + 1, index2 + 1);

        int minCost = insertCost;
        m_Operations.SetElement(index1, index2, 1);

        if (deleteCost < minCost)
        {
          minCost = deleteCost;
          m_Operations.SetElement(index1, index2, 2);
        }

        if (replaceCost < minCost)
        {
          minCost = replaceCost;
          m_Operations.SetElement(index1, index2, 3);
        }

        m_results.SetElement(index1, index2, 1 + minCost);
      }
    }
  }

  int operations = m_results.GetElement(0, 0);
  return GetSteps(first, second, operations);
} 
```

自底向上的方法优于记忆的缺点是，即使问题可以通过计算数组的一部分来解决，这种方法也会填充整个数组。

* * *

# **结论**

我的机器上的基准测试结果
编辑距离基准测试 [![Edit distance benchmarks](img/e532792fcc36ebcbaa48f12c22214c97.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3VB61Aaa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://ankursheel.com/wp-content/uploads/2017/05/edit_distance_benchmarks.jpg) 
如果您想查看代码或者自己运行基准测试，您可以在这里找到代码。

在本系列的下一篇文章中，我们将研究可以通过动态编程解决的另一个问题。你以前试过动态编程吗？你的体验如何？请在评论中告诉我。