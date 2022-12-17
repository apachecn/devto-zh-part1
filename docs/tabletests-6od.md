# 表格测试

> 原文：<https://dev.to/horia141/tabletests-6od>

本文是对[桌面测试](https://github.com/horia141/tabletest)和[桌面测试 3](https://github.com/horia141/tabletest3) 的介绍和论证。这些分别是我写的小 Python 和 Python3 包。它们在编写所谓的“表格测试”或数据驱动测试时使用。

我不会因为说小而简洁的函数是“好东西”而引起争议。这样的功能易于使用，易于理解，易于测试。

为了便于讨论，假设我们有一个小函数，用于将二进制数字串转换成它们所代表的整数。我们就叫它`parse_bin`。它对字符串如`"1010"`进行操作并输出数字。很基本的东西。如果我们用 Python 来编码，它可能看起来像这样:

```
def parse_bin(bin_str):
  """Parse a string of binary digits and produce the integer value."""
  result = 0
  pow = 1
  for digit in reversed(bin_str):
    assert digit == '0' or digit == '1'
    digit_dec = 1 if digit == '1' else 0
    result += digit_dec * pow
    pow = pow << 1
  return result 
```

Enter fullscreen mode Exit fullscreen mode

这个函数在易用性上得分很高，而且希望它也很容易理解。然而，我认为这很难测试。

例如，使用 Python 的标准`unittest`库，测试套件可能看起来像这样:

```
import unittest

class ParseBin(unittest.TestCase):
  def test_parse_bin_empty_string(self):
    self.assertEqual(parse_bin(''), 0)

  def test_parse_bin_zero(self):
    self.assertEqual(parse_bin('0'), 0)

  def test_parse_bin_one(self):
    self.assertEqual(parse_bin('1'), 1)

  def test_parse_bin_leading_zero(self):
    self.assertEqual(parse_bin('00'), 0)

  def test_parse_bin_leading_zero2(self):
    self.assertEqual(parse_bin('01'), 1)

  def test_parse_bin_two(self):
    self.assertEqual(parse_bin('10'), 2)

  def test_parse_bin_three(self):
    self.assertEqual(parse_bin('11'), 3) 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很笨重。它的样板文件太多，行动太少。最糟糕的是，添加另一个测试相当复杂。我们需要定义一个新函数，并为它编写少量非常重复的代码。因此，我们想跳过测试，只做最少的要求，而不是写一组更全面的测试。例如，我们还没有测试非常大的整数和溢出模式，或者无效输入等。虽然这个例子肯定是人为的，但人们可以很容易地想象事情升级为更复杂的函数。

一个自然的第二个版本是去掉普通的测试代码，只做一个单一的、数据驱动的测试。它可能看起来像这样:

```
import unittest

class ParseBin(unittest.TestCase):
  TEST_CASES = [
    ('', 0),
    ('0', 0),
    ('1', 1)
    ('00', 0),
    ('01', 1),
    ('10', 2),
    ('11', 3),
    ('000', 0),
    ('001', 1),
    ('010', 2),
    ('011', 3),
    ('100', 4),
    ('1000000000000000', 2**15),
    ]

  def test_parse_bin(self):
    for (input, expected) in TEST_CASES:
      self.assertEqual(parse_bin(input), expected) 
```

Enter fullscreen mode Exit fullscreen mode

这种方法是一种改进，因为它使得添加新的测试用例变得容易。事实上，我们只需要添加一个`(input, expected)`对来添加一个新的 case，这是我们所能期望的最低限度。这甚至为自动生成的案例打开了大门，而不是手工编码的案例。

然而，这种方法有其自身的局限性。例如，我们负责迭代每个测试用例的样板文件。这有点像我们自己负责调用`setUp`和`tearDown`方法。当然，它们被分成方法，并且是可重用的，但是这种情况看起来应该由测试框架来处理，而不是我们。此外，测试是耦合的。如果一个案例失败，整个套件都会失败。对于这个简单的例子，找出故障发生在哪里是非常简单的。对于更复杂的设置，情况可能不是这样。不考虑其他因素，耦合本身就很麻烦，因为测试独立是一个很好的原则。联系到最后一个问题，复杂的测试人员可能会并行运行测试。由于我们已经将所有以前的独立测试合并成一个单一的测试，我们已经失去了这种能力。测试可能变得太大，需要额外的资源，或者它的执行时间可能变得难以控制。最后，添加一个新测试并在测试运行器输出中看到一个新条目的感觉很好。我们这样写肯定会失去这种乐趣。

在这一点上，有人可能会说治疗比疾病更糟糕。当然，也有很多缺点。然而，我们不需要屈从于笨重的 XOR 测试。我们可以两全其美。

所有这些都是引入 [tabletest](https://github.com/horia141/tabletest) 库的漫长道路，tabletest【】库是一个`unittest`扩展，它允许人们进行数据驱动测试，但是具有分离和独立单元测试的所有优点。

此时，最好让代码自己说话。测试套件的第三个也是最后一个版本看起来像这样:

```
import tabletest

class ParseBin(tabletest.TableTestCase):
  TEST_CASES = [
    ('', 0),
    ('0', 0),
    ('1', 1)
    ('00', 0),
    ('01', 1),
    ('10', 2),
    ('11', 3),
    ('000', 0),
    ('001', 1),
    ('010', 2),
    ('011', 3),
    ('100', 4),
    ('101', 5),
    ('110', 6),
    ('111', 7),
    ('1000000000000000', 2**15),
    ('10000', 16),
    ('100001', 33),
    ('0110001', 49),
    ]

  @tabletest.tabletest(TEST_CASES)
  def test_parse_bin(self, test_case):
    (input, expected) = test_case
    self.assertEqual(parse_bin(input), expected) 
```

Enter fullscreen mode Exit fullscreen mode

这两个版本的区别在于，我们用一个新的版本替换了手动迭代的版本`test_parse_bin`,这个新版本用`tabletest`注释进行了注释。希望使用它的方法是清楚的。

在幕后，这个库为每个测试用例生成一个版本的`test_parse_bin`。因此，执行的代码看起来像第一个版本，而不是第二个版本。我们仍然拥有独立测试和无样板开发的所有优点，而不必自己开发。最后，测试运行人员将为每个测试用例显示一个条目，这将让我们继续编写它们。

用法应该简单明了，没有惊喜。欲知更多信息，请收听该系列的下一篇[文章](///how-tabletest-works.html)。

无论如何，现在就这样了。