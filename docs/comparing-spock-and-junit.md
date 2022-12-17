# 比较斯波克和朱尼特

> 原文：<https://dev.to/raulavila/comparing-spock-and-junit>

JUnit 被认为是测试 Java 应用程序的标准框架。我认为短期内没有人会挑战它的地位。此外，JUnit 5 将很快发布，它将支持闪亮的 Java 8 特性，这在最近的 JUnit 4 中是一个障碍。

然而，一个新的测试框架正在努力推进。最初创建它是为了测试 Groovy 代码，但是考虑到 Groovy 运行在 JVM 上，它也可以用来测试 Java 代码！

### 斯波克

Spock 是“规范和模仿”的首字母缩写。我想现在很容易弄清楚它允许我们做什么:创建我们系统的规范，添加功能来创建我们依赖关系的模拟。我说的是规范，而不是测试，这在开始时可能会有点混乱，但希望通过示例可以很好地理解。简而言之，一个规范只是一种兴奋剂。

我写这篇文章的目的是通过一个清晰的例子来展示 Spock 和 JUnit 之间的主要区别。关于 Spock 提供了什么，我不想说太多细节，你已经有了官方文档。

### 我们需要测试的类

我们将创建一个名为`Calculator`的简单类，我们想要测试它:

```
public class Calculator {

    private Audit audit;

    public Calculator(Audit audit) {
        this.audit = audit;
    }

    public long add(int operand1, int operand2, Mode mode) {
        audit.register(String.format("%d + %d (%s)",
                        operand1,
                        operand2,
                        mode));

        if (mode == Mode.ABSOLUTE) {
            operand1 = Math.abs(operand1);
            operand2 = Math.abs(operand2);
        }

        return operand1 + operand2;
    }

    public static enum Mode {ABSOLUTE, STRAIGHT;}
} 
```

Enter fullscreen mode Exit fullscreen mode

这个类的特征(无论如何，它不是一个很好的最佳实践的例子)是清楚的。

### JUnit 中的测试

这将是 JUnit 类，它将涵盖`Calculator` :
中的所有特性

```
@RunWith(Parameterized.class)
public class CalculatorTest {

    @Mock
    private Audit audit;

    @InjectMocks
    private Calculator calculator;

    @Before
    public void setUp() throws Exception {
        MockitoAnnotations.initMocks(this);
    }

    private int operand1;
    private int operand2;
    private long expectedResultStraight;
    private long expectedResultAbsolute;

    @Parameterized.Parameters
    public static Collection data() {
        Object[][] data = new Object[][] {
                { 2, 2, 4, 4 },
                { -2, 2, 0, 4 },
                { -3, -3, -6, 6 },
                { 0, 0, 0, 0 }
        };

        return Arrays.asList(data);
    }

    public CalculatorTest(int operand1,
                          int operand2,
                          long expectedResultStraight,
                          long expectedResultAbsolute) {

        this.operand1 = operand1;
        this.operand2 = operand2;
        this.expectedResultStraight = expectedResultStraight;
        this.expectedResultAbsolute = expectedResultAbsolute;
    }

    @Test
    public void testAddStraight() throws Exception {
        long sum = calculator.add(operand1, operand2, STRAIGHT);
        assertThat(sum, is(expectedResultStraight));

        verify(audit).register(
                String.format("%d + %d (STRAIGHT)", operand1, operand2));
    }

    @Test
    public void testAddAbsolute() throws Exception {
        long sum = calculator.add(operand1, operand2, ABSOLUTE);
        assertThat(sum, is(expectedResultAbsolute));

        verify(audit).register(
                String.format("%d + %d (ABSOLUTE)", operand1, operand2));
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

我认为这里有几个不便之处:

*   我们在 JUnit 的基础上添加了两个库来改进我们的测试代码: [Hamcrest](http://hamcrest.org/) ，以提高可读性，以及 [Mockito](http://mockito.org/) ，以创建我们的依赖关系的模拟。
*   为了恰当地使用数据驱动测试，我们需要我们的测试由`Parameterized` runner 运行，而不是 JUnit 默认 runner。
*   我们需要从测试方法中写出的样板代码的数量是相当可观的。我们需要使用一种丑陋的方法(在`data`方法中)准备一个数据矩阵，我们需要一个`setUp`方法来初始化依赖关系，一个构造函数来初始化每个测试中的数据，等等。

所有这些问题本身并不坏，我们都知道 Java 作为一种简洁的语言并不流行。实际上，它的冗长在某些场合可能是好的，因为它迫使我们对我们的设计进行良好的实践和仔细的思考。但是当我们需要编写测试时，跳过其中一些问题不是更好吗？

### 斯波克前来救援

让我们看看如何在 Spock:
中实现我们对`Calculator`的规范

```
class CalculatorSpec extends Specification {

    Audit audit = Mock()

    @Subject
    Calculator calculator = new Calculator(audit)

    def "Calculator can add operands in straight mode"() {
        when: "We add two operands in straight mode"
        long sum = calculator.add(operand1, operand2, STRAIGHT)

        then: "The result of the sum matches the expected one"
        sum == expectedResult

        where:
        operand1 | operand2 || expectedResult
        2        | 2        || 4
        -2       | 2        || 0
        -3       | -3       || -6
    }

    def "Calculator can add operands in absolute mode"() {
        when: "We add two operands in absolute mode"
        long sum = calculator.add(operand1, operand2, ABSOLUTE)

        then: "The result of the sum matches the expected one"
        sum == expectedResult

        where:
        operand1 | operand2 || expectedResult
        2        | 2        || 4
        -2       | 2        || 4
        -3       | -3       || 6
    }

    def "Audit object intercepts all calls to the Calculator"() {
        when: "We add two operands in any mode"
        calculator.add(2, 2, ABSOLUTE)
        calculator.add(2, 2, STRAIGHT)

        then: "The Audit object registers the call"
        1 * audit.register("2 + 2 (ABSOLUTE)")
        1 * audit.register("2 + 2 (STRAIGHT)")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到:

*   这些测试是用 Groovy 编写的。
*   测试类的名称有`Spec`后缀，并扩展了`Specification`。这个名字实际上意味着我们的类不仅仅是测试代码，它实际上是生成人类可读的规范。
*   我们可以创建一个调用方法`Mock`的模拟，这是框架的一部分。
*   注释告诉我们我们正在测试/描述哪个类
*   我们的方法可以使用字符串命名，没有任何限制。这意味着我们不需要遵循编程语言给出的任何约定，所以描述我们将要测试的东西要容易得多，而不用担心创建长而不可读的方法名。
*   测试方法按照[排列行为断言模式](http://c2.com/cgi/wiki?ArrangeActAssert)或“给定时间”被清楚地分成几个部分。我们的例子非常简单，我们不需要在`given:`部分做任何事情，所以我们跳过它。
*   每个部分都可以有自己的描述，因此我们可以准确地解释我们在那里准备、运行或验证什么。
*   `then:`部分包含将使用 [Groovy 真理](http://groovy-lang.org/semantics.html#Groovy-Truth)验证的断言。我们不需要使用 assert 方法，只需要布尔表达式。
*   使用模拟的验证是非常清楚的:`1 * audit.register("2 + 2 (ABSOLUTE)")`验证我们正在用那些参数调用`audit`中的`register`方法一次，如果没有发生，它将失败。用 mocks 模拟行为也很简单，更多细节请看[官方文档](http://spockframework.org/spock/docs/1.0/interaction_based_testing.html)。
*   数据矩阵非常简洁，我们可以在`where:`部分看到。不再需要创建二维数组。

最后，让我们看看框架如何显示断言错误，因为它以图形方式显示，我们可以看到错误、所有相关变量的值等。这里有一个例子:

```
Condition not satisfied:

sum == expectedResult
|   |  |
4   |  5
    false 
```

Enter fullscreen mode Exit fullscreen mode

### 斯波克 vs 朱尼特

我并不试图说服这里的任何人使用一个框架来反对另一个，只是想比较两者之间的差异。使用 Spock 的好处是显而易见的，但是它确实迫使我们在我们的技术堆栈中增加一门新的语言。根据我的经验，公司不太愿意这么做，因为这会让招聘变得更复杂。无论如何，我认为如果你是一名 Java 开发人员，学习 Groovy 和 Spock 并不具有挑战性。

为了在 Maven 项目中使用 Spock，有必要正确配置我们的 POM 文件。如果我们用 Gradle 当然更容易。