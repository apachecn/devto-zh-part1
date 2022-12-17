# 属性测试:为什么您应该生成测试数据

> 原文：<https://dev.to/checkroth/property-testing-why-you-should-generate-test-data>

我在我的第一份真正的工作中喝了函数式编程清凉饮料。虽然我目前没有用函数式编程进行专业开发，但我仍然尽可能地应用函数式概念——属性测试是一个大问题。

我将讨论三个要点:

*   房产测试有什么特别的？
*   当我不能预先确定预期的结果时，我怎样才能避免使我的测试过于复杂呢？
*   什么时候*我不应该使用属性测试？*

# 属性测试有什么特别的？

在函数库的所有可用工具中，Haskell 的 *quickcheck* 是我见过的在其他非函数语言中被重用最多的工具。

乍一看，属性测试看起来像是“生成测试数据”的一种花哨说法，从某种意义上说，的确如此。那么为什么是“属性”测试呢？

当你用预先确定的参数测试一个函数时，你真正测试的是这个函数对那些输入起作用。你可以添加许多测试参数，足以覆盖一个“可接受的”范围，但是这是许多不必要的工作，没有人会真的去做(虽然我见过)。

当您测试 *property* 时，您并不关心输入是什么——您并不是在测试当给定一些输入时函数会返回一些值。您正在测试该函数，当给定一个符合其要求的参数(比如一个“int”)时，它将做*某事*，并对给定的任何输入返回适当的响应。

我们来看一个很简单的例子，一个加法器:

```
def adder(i1, i2):
  return i1 + i2 
```

Enter fullscreen mode Exit fullscreen mode

简单，取两个`int`并返回总和。就是这个属性——这个函数“返回两个 int 的和”。

让我们来看一个非常标准的测试:

```
class Tests:
  @pytest.mark.parametrize("input1,input2,expect", [(1, 1, 2), (2, 2, 4)])
  def test_adder(self, input1, input2, expect):
    assert adder(input1, input2) == expect 
```

Enter fullscreen mode Exit fullscreen mode

本次测试通过:`2 passed in 0.02 seconds`。我们认为 1 和 1 的和是 2，2 和 2 的和是 4。但那不是测试的内容。

让我们再添加一些“加法器”:

```
def adder2(i1: int, i2: int):
  if i1 == 1:
    return 2
  if i1 == 2:
    return 4

def adder3(i1: int, i2: int):
  return i1*2 
```

Enter fullscreen mode Exit fullscreen mode

让我们对这些进行同样的测试:

```
 @pytest.mark.parametrize("input1,input2,expect", [(1, 1, 2), (2, 2, 4)])
  def test_adder1(self, input1, input2, expect):
    assert adder2(input1, input2) == expect

  @pytest.mark.parametrize("input1,input2,expect", [(1, 1, 2), (2, 2, 4)])
  def test_adder2(self, input1, input2, expect):
    assert adder3(input1, input2) == expect 
```

Enter fullscreen mode Exit fullscreen mode

测试仍然通过:`6 passed in 0.02 seconds`
我们的测试没有测试这个函数的*属性*，只是测试了一些*显式*输入的一些*显式*结果。

现在让我们来看看 [pytest-quickcheck](https://pypi.python.org/pypi/pytest-quickcheck)
这个库给了我们一种指定测试参数的新方法:`@pytest.mark.randomize`。让我们用它重写我们的第一个测试。

```
 @pytest.mark.randomize(i1=int, i2=int)
    def test_adder(self, i1, i2):
        expect = i1 + i2
        assert adder(i1, i2) == expect 
```

Enter fullscreen mode Exit fullscreen mode

注意，我在测试中构建了 expect 当我们不知道输入时，我们不能预先确定输出。
该测试将通过:`9 passed in 0.03 seconds`

现在让我们添加其余的测试:

```
 @pytest.mark.randomize(i1=int, i2=int)
    def test_adder2(self, i1, i2):
        expect = i1 + i2
        assert adder2(i1, i2) == expect

    @pytest.mark.randomize(i1=int, i2=int)
    def test_adder3(self, i1, i2):
        expect = i1 + i2
        assert adder3(i1, i2) == expect 
```

Enter fullscreen mode Exit fullscreen mode

你不会期望这些测试工作- adder2 是一个 if 语句，当它的第一个参数不是 1 或 2 时就会爆炸。加法器 3 根本不是加法器，它是乘法。

```
E       assert -4819122279534621102 == -6480923863972990104
E        +  where -4819122279534621102 = adder3(-2409561139767310551, -4071362724205679553)

tests2.py:33: AssertionError 
```

Enter fullscreen mode Exit fullscreen mode

我们的测试实际上是测试我们的`adder`是*加上*。这可能看起来有点过了(特别是因为测试完全重新实现了函数)，但是当您测试更复杂的函数时，这就变得更有意义了。

# 当我不能预先确定预期的结果时，我如何避免使我的测试过于复杂？

你可能会认为属性测试的“弱点”是你必须在测试中写太多的逻辑(很多人对此不屑一顾)!为什么你必须在你的测试中写逻辑，特别是当它们开始与你首先测试的东西相似的时候？

首先，在测试中重新实现你的整个功能实际上是没问题的。尤其是在像加法器这样的情况下。重要的部分是你的测试成为你的项目的一个静态部分，它将做两件事:证明你的函数工作，并防止你在将来破坏函数。

你的很多测试将会针对更复杂的函数- `adder`是一个过于简单的例子。让我们看一个使用更复杂结构的函数。
这个函数将获取一个包含 x 和 y 坐标的字典集合，并根据集合的条目所在的位置计算一个图的象限的统计数据。

```
def calculate_graph_quadrants(entries: List[Dict[Any, Any]]):
    total = len(entries)
    quadrants = {
        'quad1': { 'count': 0 },
        'quad2': { 'count': 0 },
        'quad3': { 'count': 0 },
        'quad4': { 'count': 0 }
    }

    for entry in entries:
        quadrant = calc_quadrant(entry['x'], entry['y'])
        key = 'quad{}'.format(quadrant)
        quadrants[key]['count'] += 1
    for _, quad_stats in quadrants.items():
        quad_stats['share'] = quad_stats['count'] / total
    return quadrants

def calc_quadrant: 
    # takes an x and y, returns quadrant of graph coordinates land
    ... 
```

Enter fullscreen mode Exit fullscreen mode

这看起来比`adder`更难测试——我们必须构建这个`entries: List[Dict[Any, Any]]`，并以某种方式确定每个象限的期望计数和份额。我们可以生成这样的数据:

```
 @pytest.mark.randomize(xl=list_of(int, items=20),
                           yl=list_of(int, items=20))
    def test_calculate_graph_quadrants(self, xl, xy):
        pass 
```

Enter fullscreen mode Exit fullscreen mode

但是我不建议这样做——这需要完全重新实现这个函数。我甚至不打算写测试是什么样子的，因为这不值得花力气。

为一个复杂的函数编写一个*干净的*属性测试的关键是在不降低测试强度的情况下，尽可能多地从你生成的数据中*减少*未知。

```
 @pytest.mark.randomize(count=int, min_num=1, max_num=50)
    def test_calculate_graph_quadrants(self, count):
        q1 = [{'x': -1, 'y': 1} for _ in range(count)]
        q2 = [{'x': 1, 'y': 1} for _ in range(count)]
        q3 = [{'x': -1, 'y': -1} for _ in range(count)]
        q4 = [{'x': 1, 'y': -1} for _ in range(count)]
        entries = list(itertools.chain(q1, q2, q3, q4) 
```

Enter fullscreen mode Exit fullscreen mode

我们不是在测试确定坐标落入哪个象限的能力——那是不同的功能。我们可以假设它按预期工作，并在以后为该功能编写另一个测试。所以我们真正需要做的是让变量成为我们想要传递给函数的每个象限的多少个条目。您可以使每个长度不同，但这没有什么价值——我们可以通过为每个预期象限传递相同数量的数据来简化测试。

因为我们知道每个象限中有多少个条目，所以预期的结果实际上很容易计算:

```
 @pytest.mark.randomize(count=int, min_num=1, max_num=50)
    def test_calculate_graph_quadrants(self, count):
        q1 = [{'x': -1, 'y': 1} for _ in range(count)]
        q2 = [{'x': 1, 'y': 1} for _ in range(count)]
        q3 = [{'x': -1, 'y': -1} for _ in range(count)]
        q4 = [{'x': 1, 'y': -1} for _ in range(count)]
        entries = list(itertools.chain(q1, q2, q3, q4)
    # repeat the count for each quadrant, so the total is simple
    total = count*4
    # Each quadrant has the same count - the expected share will also be the same.
    expected_share = count / total
    # we could also do expected_share = .25
    expect = { q: {'count': count, 'share': expected_share}
               for q in ['quad1', 'quad2', 'quad3', 'quad4']}
    assert calculate_graph_quadrants(entries) == expect 
```

Enter fullscreen mode Exit fullscreen mode

假设`calc_quadrant`是准确的，这个测试将会通过。那还不算太糟，我们在测试中真的没有那么多逻辑。通过控制我们做什么和不想被随机化，我们可以很容易地确定我们的函数应该返回什么。

*旁注:*我们都是工具局限性的奴隶。如果我们有一个像 Scala 的`scalacheck`这样的库，我们将能够为`entries`编写一个生成器，不用在测试中编写逻辑就能构建同样的东西。

# 当*的时候*我不应该用属性测试吗？

你应该*总是*使用属性测试。但是有些库并不适合你可能想要的有限范围内的一代。下面这些都可以用`scalacheck`的定制生成器来完成，但是用`pytest-quickcheck`根本不可能。

让我们用一个针对`calc_quadrant`的测试来演示一下。下面是函数:

```
def calc_quadrant(x: int, y: int):
    if x < 0:
        if y >= 0:
            return 1
        elif y < 0:
            return 3
    if x >= 0:
        if y < 0:
            return 4
        elif y >= 0:
            return 2 
```

Enter fullscreen mode Exit fullscreen mode

我们可以说这个函数有四个严格定义的属性，从 x 和 y 的符号(+/-)的四个逻辑路径延伸而来。我们*可以*随机生成 x 和 y，并建立这样的断言:

```
@pytest.mark.randomize(x=int, y=int)
def test_calc_quadrant(self, x: int, y: int):
    if x < 0:
        if y >= 0:
            expect = 1
        elif y < 0:
            expect = 3
    if x >= 0:
        if y < 0:
            expect = 4
        elif y >= 0:
            expect = 2
    assert calc_quadrant(x, y) == expect 
```

Enter fullscreen mode Exit fullscreen mode

看，我们刚刚把整个函数复制到测试中。我知道我说过可以在测试中重新实现你的函数，但是在这种情况下，我真的不想这么做。我们可以使用`parametrize`获得有价值的结果，给出 x 和 y 的所有排列。

```
@pytest.mark.parametrize("x,y,expect", [(-1, 1, 1), (1, 1, 2), (-1, -1, 3), (1, -1, 4)])
def test_calc_quadrant(self, x: int, y: int, expect: int):
    assert calc_quadrant(x, y) == expect 
```

Enter fullscreen mode Exit fullscreen mode

不过，我们仍然希望在这里使用属性测试，以涵盖测试输入的任何范围都将在我们的有限范围(1-4)内产生输出。

```
@pytest.mark.randomize(x=int, y=int)
def test_calc_quadrant_in_range(self, x: int, y: int):
    expect_range = [1, 2, 3, 4]
    assert calc_quadrant(x, y) in expect_range 
```

Enter fullscreen mode Exit fullscreen mode

这样我们就可以证明测试只会产生一个对应于四个象限之一的 int。结合手动象限测试，我们已经覆盖了足够的功能，让大多数人满意。显然，`calc_quadrant` *可以*专门匹配 x 和 y 值的-1 和 1，否则只返回`1`，这将满足两个测试。

是否实现前一个属性测试(它重新实现了函数)实际上取决于您。我个人只在这是黑盒测试的情况下才会这么做，我不能只看代码就知道它不只是匹配特定的值。否则，我会说对象限的手动测试和对在范围内的结果的属性测试是“足够接近”的。