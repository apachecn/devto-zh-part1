# Python 异常处理–浮点错误

> 原文：<https://dev.to/airbrake/python-exception-handling--floatingpointerror-6f6>

今天，我们通过查看**floating pointer error**开始我们深入的 [**Python 异常处理**](https://dev.to/airbrake/the-python-exception-class-hierarchy-e06) 系列。与大多数编程语言一样，Python 中的`FloatingPointError`表示浮点计算出现了问题。然而，*不像*其他大多数语言，Python 默认不会引发`FloatingPointError`。这种能力必须通过在构建本地 Python 环境时包含 [`fpectl`](https://docs.python.org/3/library/fpectl.html) 模块来实现。

在本文中，我们将通过首先查看它在整个 [Python 异常类层次结构](https://dev.to/airbrake/the-python-exception-class-hierarchy-e06)中的位置来探索`FloatingPointError`。我们还将讨论如何启用`fpectl`模块，以及这样做如何允许在您自己的代码中引发`FloatingPointErrors`。我们开始吧！

## 技术破落

所有 Python 异常都继承自 [`BaseException`](https://docs.python.org/3/library/exceptions.html#BaseException) 类，或者从其中的继承类扩展。此错误的完整异常层次结构如下:

*   [T2`BaseException`](https://docs.python.org/3/library/exceptions.html#BaseException)
    *   [T2`Exception`](https://docs.python.org/3/library/exceptions.html#Exception)
    *   [T2`ArithmeticError`](https://docs.python.org/3/library/exceptions.html#ArithmeticError)
        *   `FloatingPointError`

## 完整代码示例

下面是我们将在本文中使用的完整代码示例。如果您想亲自试验代码，看看一切是如何工作的，可以复制并粘贴它。

```
import fpectl
from gw_utility.logging import Logging

def main():
    Logging.line_separator("FLOATING POINT")
    test_floating_point()

    Logging.line_separator("DIVISION BY ZERO")
    test_division_by_zero()

    Logging.line_separator("FLOATING POINT DIVISION BY ZERO", 60)
    test_floating_point_division_by_zero()

def test_floating_point():
    try:
        Logging.log(round(24.601 / 3.5, 4))
    except FloatingPointError as exception:
        # Output expected FloatingPointErrors.
        Logging.log_exception(exception)
    except Exception as exception:
        # Output expected Exceptions.
        Logging.log_exception(exception, False)

def test_division_by_zero():
    try:
        # Divide by zero.
        Logging.log(24 / 0)
    except FloatingPointError as exception:
        # Output expected FloatingPointErrors.
        Logging.log_exception(exception)
    except Exception as exception:
        # Output expected Exceptions.
        Logging.log_exception(exception, False)

def test_floating_point_division_by_zero():
    try:
        # Divide by floating point zero and round.
        Logging.log(round(24.601 / 0.0, 4))
    except FloatingPointError as exception:
        # Output expected FloatingPointErrors.
        Logging.log_exception(exception)
    except Exception as exception:
        # Output expected Exceptions.
        Logging.log_exception(exception, False)

if __name__ == " __main__":
    main()

import math
import sys
import traceback

class Logging:

    separator_character_default = '-'
    separator_length_default = 40

    @classmethod
    def __output(cls, *args, sep: str=' ', end: str='\n', file=None):
        """Prints the passed value(s) to the console.

        :param args: Values to output.
        :param sep: String inserted between values, default a space.
        :param end: String appended after the last value, default a newline.
        :param file: A file-like object (stream); defaults to the current sys.stdout.
        :return: None
        """
        print(*args, sep=sep, end=end, file=file)

    @classmethod
    def line_separator(cls, value: str, length: int=separator_length_default, char: str=separator_character_default):
        """Print a line separator with inserted text centered in the middle.

        :param value: Inserted text to be centered.
        :param length: Total separator length.
        :param char: Separator character.
        """
        output = value

        if len(value) < length:
            # Update length based on insert length, less a space for margin.
            length -= len(value) + 2
            # Halve the length and floor left side.
            left = math.floor(length / 2)
            right = left
            # If odd number, add dropped remainder to right side.
            if length % 2 != 0:
                right += 1

            # Surround insert with separators.
            output = f'{char * left} {value} {char * right}'

        cls.__output(output)

    @classmethod
    def log(cls, *args, sep: str=' ', end: str='\n', file=None):
        """Prints the passed value(s) to the console.

        :param args: Values to output.
        :param sep: String inserted between values, default a space.
        :param end: String appended after the last value, default a newline.
        :param file: A file-like object (stream); defaults to the current sys.stdout.
        """
        cls.__output(*args, sep=sep, end=end, file=file)

    @classmethod
    def log_exception(cls, exception: BaseException, expected: bool=True):
        """Prints the passed BaseException to the console, including traceback.

        :param exception: The BaseException to output.
        :param expected: Determines if BaseException was expected.
        """
        output = "[{}] {}: {}".format('EXPECTED' if expected else 'UNEXPECTED', type(exception). __name__ , exception)
        cls.__output(output)
        exc_type, exc_value, exc_traceback = sys.exc_info()
        traceback.print_tb(exc_traceback) 
```

Enter fullscreen mode Exit fullscreen mode

## 应该什么时候用？

正如介绍中所讨论的，在`FloatingPointError`出现之前，你需要确保你的本地 Python 版本包含了 [`fpectl`](https://docs.python.org/3/library/fpectl.html) 模块。由于这个模块*在默认情况下没有*包含在大多数 Python 版本中，所以如果需要的话，您可能不得不用它显式地构建您的 Python。在编译 Python 时，可以通过使用`--with-fpectl`标志来完成添加`fpectl`模块。浏览 Python 的编译过程已经超出了本文的范围，但是一旦`fpectl`成为包含的模块，您就可以开始测试`FloatingPointError`。

对于我们的示例代码，我们没有做任何引人注目的事情。事实上，`FloatingPointError`在*其他* `ArithmeticErrors`正常出现的情况下被有效地引发，除了你使用浮点数*和*`fpectl`模块被启用。例如，您可能会引发一个`FloatingPointError`，而您通常会通过使用浮点值除以零来获得一个`ZeroDivisionError`。

我们已经从`test_floating_point()` :
开始创建了一些简单的测试方法

```
def test_floating_point():
    try:
        Logging.log(round(24.601 / 3.5, 4))
    except FloatingPointError as exception:
        # Output expected FloatingPointErrors.
        Logging.log_exception(exception)
    except Exception as exception:
        # Output expected Exceptions.
        Logging.log_exception(exception, False) 
```

Enter fullscreen mode Exit fullscreen mode

执行这段代码会像预期的那样工作，执行浮点计算并将结果四舍五入到四位小数，然后将结果输出到我们的日志:

```
------------ FLOATING POINT ------------
7.0289 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们远离使用浮点值，使用常规整数，同时尝试除以零:

```
def test_division_by_zero():
    try:
        # Divide by zero.
        Logging.log(24 / 0)
    except FloatingPointError as exception:
        # Output expected FloatingPointErrors.
        Logging.log_exception(exception)
    except Exception as exception:
        # Output expected Exceptions.
        Logging.log_exception(exception, False) 
```

Enter fullscreen mode Exit fullscreen mode

这引发了一个意外的`ZeroDivisionException`，因为即使`fpectl`被启用，我们也没有在计算中使用浮点值:

```
----------- DIVISION BY ZERO -----------
[UNEXPECTED] ZeroDivisionError: division by zero
  File "D:/work/Airbrake.io/Exceptions/Python/BaseException/Exception/ArithmeticError/FloatingPointError/main.py", line 30, in test_division_by_zero
    Logging.log(24 / 0) 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们在使用浮点值时尝试相同的除以零:

```
def test_floating_point_division_by_zero():
    try:
        # Divide by floating point zero and round.
        Logging.log(round(24.601 / 0.0, 4))
    except FloatingPointError as exception:
        # Output expected FloatingPointErrors.
        Logging.log_exception(exception)
    except Exception as exception:
        # Output expected Exceptions.
        Logging.log_exception(exception, False) 
```

Enter fullscreen mode Exit fullscreen mode

正如您可能会怀疑的那样，这给我们带来了一个`FloatingPointError`:

```
------------- FLOATING POINT DIVISION BY ZERO --------------
[EXPECTED] FloatingPointError: invalid value encountered in divide
  File "D:/work/Airbrake.io/Exceptions/Python/BaseException/Exception/ArithmeticError/FloatingPointError/main.py", line 42, in test_floating_point_division_by_zero
    Logging.log(round(24.601 / 0.0, 4)) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有了使用`FloatingPointErrors`的基本知识。然而，在您开始向 Python 添加`fpectl`模块以区分`FloatingPointErrors`和普通的`ArithmeticErrors`之前，有一些注意事项需要注意。用于浮点运算的 [`IEEE 754`](https://en.wikipedia.org/wiki/IEEE_754) 标准为浮点数的格式化、舍入、允许的操作和异常处理实践定义了许多通用标准。然而，您的代码必须被明确告知以本地处理器生成的`SIGFPE`信号的形式捕获`IEEE 754`异常。因此，虽然 Python 被配置为通过`fpectl`模块这样做，但许多其他定制脚本/应用程序并不是这样。

另一个主要考虑是通常不鼓励使用`fpectl`模块，很大程度上是因为它*不是线程安全的*。线程安全应用程序(即大多数开发得当的 Python 应用程序)允许在多个线程之间安全地共享数据结构，而不用担心一个线程操纵或更改另一个线程正在使用的一些数据(或者另一个线程看到不同的数据)。然而，使用`fpectl`模块意味着您的浮点数据不再是线程安全的，这可能会在多线程应用程序中引起重大问题。为了安全起见，通常建议您避免使用`fpectl`,使用另一种形式的应用程序逻辑来检查算术错误。

Airbrake 强大的[错误监控软件](https://airbrake.io/account/new?utm_source=blog&utm_medium=end-post&utm_campaign=airbrake-python-exception-hierarchy)为您的所有开发项目提供实时错误监控和自动异常报告。Airbrake 最先进的 web 仪表盘可确保您全天候接收应用程序运行状况和出错率的状态更新。无论你在做什么，Airbrake 都可以轻松地与所有最流行的语言和框架集成。此外，Airbrake 可以轻松定制异常参数，同时让您完全控制主动错误过滤系统，因此您只需收集最重要的错误。

今天就来看看 [Airbrake 的错误监控软件](https://airbrake.io/account/new?utm_source=blog&utm_medium=end-post&utm_campaign=airbrake-python-exception-hierarchy)，亲眼看看为什么这么多世界上最好的工程团队使用 Airbrake 来革新他们的异常处理实践！