# 非凡的 Python

> 原文：<https://dev.to/dmerejkowsky/exceptional-python>

*原载于[我的博客](https://dmerej.info/blog/post/exceptional-python/)*

# 简介:错误管理

有时候，事情会出错。在英语中，我们有很多表达方式:

*   意想不到的事情发生了
*   出了点问题
*   混乱(情况正常，一切正常)
*   狗屎击中了风扇
*   该死的飞机撞到了山上！
*   ...

编写软件时，我们倾向于使用错误代码或异常。

# 我为什么要在乎？

你应该关心，因为错误和意想不到的事情*会*发生。

当他们这样做的时候，你将需要所有的信息，你可以理解发生了什么，并修复潜在的错误。

这同样适用于 API 的用户。如果你不能很好地处理错误，每次 API 被误用，用户不能理解错误所在，他们会很快感到沮丧。如果你幸运的话，他们可能会去文档中寻找答案，但是他们中的许多人会放弃。

最后，您的最终用户需要知道当错误发生时您会怎么做。他们想知道*和*发生了什么，更重要的是，他们应该做些什么来修复它。

因此，正确的错误处理是关键。你肯定不希望你的产品出现在[每日 WTF 错误系列](https://thedailywtf.com/series/errord)中，是吗？

# 第一部分:各种错误处理技术

注意:这部分主要是理论性的。如果您想要更具体的东西，可以直接跳到第 2 部分。

# 迎头痛击

当您使用 bash 时，通常会运行几个外部命令，例如:

```
cd project
git pull
pylint project
pytest 
```

Enter fullscreen mode Exit fullscreen mode

这些命令中的每一个都有一个返回代码，您可以使用一个名为`$?`的特殊变量来访问它。

当命令成功时，返回代码是 0，当出错时，返回代码可以是许多其他值。正如托尔斯泰所说:

> 幸福的家庭都是相似的；每个不幸的家庭各有各的不幸。

例如，`pytest`为 6 种不同的可能返回代码:

*   当所有测试都通过时为 0
*   1 当收集并运行了测试，但一些测试失败时
*   2 当用户中断测试执行时
*   还有更 <sup id="fnref1">[1](#fn1)</sup>

这种方法有两个问题:

*   你必须一直检查返回代码，否则代码会继续执行，忽略之前的错误。(不过，您可以通过调用脚本顶部的`set -e`来解决这个问题)
*   当某件事失败时，你唯一的信息就是一个数字。(可能还有输出，这取决于脚本是如何编写的)

## [C 中的](#in-c)

当你用 C 写的时候，你应该检查你调用的每个函数的返回值。

有时，它是一个整数。例如:

```
int size = ...;
FILE* fp = ...;
char buff[size];
int n = fread(buff, size, 1, fp);
if (n < size) {
  // Handle "short read"
} 
```

Enter fullscreen mode Exit fullscreen mode

其他时候，你得到一个空指针，你必须检查一个叫做`errno` :
的特殊变量

```
FILE* fp = fopen("foo.cfg", "r");
if (!fp) {
  if (errno == ENOENT) {
    fprintf(stderr, "Could not find foo.cfg\n");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，你甚至没有任何错误信息，你得到的只是一个数字。(ENOENT 只是一个`#define ENOENT 2`)

注意:在 [moreutils](https://joeyh.name/code/moreutils/) 中有一个名为`errno`的工具，如果你出于某种原因想要将 errno 的值转换成人类可读的消息，它可以帮助你。

所有这些意味着您必须仔细检查您调用的所有函数的返回代码。

如果你正在使用`gcc`，你可以在调用者不检查返回值时触发一个警告，比如:

```
/* in foo.h */
int foo() __attribute__((warn_unused_result));

/* in foo.c */
{
  foo();  // triggers a warning
} 
```

Enter fullscreen mode Exit fullscreen mode

但是人们可以忽略这个警告…

# 在围棋中

在 Go 中，函数可以返回几个值。

可能失败的函数应该返回一个`Error`对象，就像这样:

```
file, err := os.Open("foo.cfg")
if err != nil {
  ...
}
// Do something with file 
```

Enter fullscreen mode Exit fullscreen mode

与 C 相反，很难忽略返回值。

你可以使用类似于
的东西

```
file, _ := os.Open("foo.cfg"); 
```

Enter fullscreen mode Exit fullscreen mode

但是有棉绒会禁止你这样做。

不像 C，你可以在你的错误中添加各种元数据。

您所需要的就是声明一个自定义结构并实现一个`Error`方法:

```
type InvalidConfigError struct {
    Path string;
    Details string
}

func (e *InvalidConfig) Error() string {
    return fmt.Sprintf("%s: %s", e.Path, e.Details)
}

func readConf() (Config, error) {
  // ...
  file, errOpen := os.Open("foo.cfg");
  if errOpen := nill {
    return nil, &InvalidConfigError{"foo", "could not open file"};
  }

  parsed, errParse := parseYaml(file);
  if errParse != nill {
    return nil, &InvalidConfigError{"foo", "invalid YAML syntax"};
  }

  return parsed, nil;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用它有点痛苦，因为你必须尝试转换它

```
func main() {
    config, err := readConf();
    if err != nil {
        invalidConfig, ok := err.(*InvalidConfig)
        // ok will be false if conversion fails
        if ok {
            fmt.Printf("%s:%s", invalidConfig.Path, invalidConfig.Details);
        }
    }
    // Do someting with config ...
} 
```

Enter fullscreen mode Exit fullscreen mode

# [Java 中的](#in-java)

在 Java 中，你用异常代替`throw`和`catch`关键字。

像 Go 一样，您可以创建自己类型的异常。

但是有两个关键的区别:

*   在`catch`块中，您可以指定您希望处理哪种错误。例如，`catch (FooException)`将捕获所有属于`FooException`类的异常(可能通过继承)
*   它们还带有一个*回溯*，列出了异常发生时调用的所有函数。

不过，有个问题。我们来看这个例子:

```
public class Foo {

    public static void readConf() {
        FileInputStream fs = new FileInputStream("foo.cfg");
    }

    public static void main() {
      readConf();
      ....
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你试图编译它，你会得到一个错误:

```
Foo.java:5: error: unreported exception FileNotFoundException;
must be caught or declared to be thrown
  FileInputStream fs = new FileInputStream("foo.cfg"); 
```

Enter fullscreen mode Exit fullscreen mode

Java 告诉你你可以选择:

*   立即处理错误
*   或者让打电话的人来做。

如果在调用者链中没有人捕获到异常，那么这个异常就被称为“未被捕获”，整个程序在打印回溯之后终止。

因为 Java 就是 Java，所以你必须明确它，就像这样:

```
public void readConf() throws FileNotFoundException {
    FileInputStream fs = new FileInputStream("foo.cfg");
}

public static void main(String[] args) throws FileNotFoundException {
    readConf();
    System.out.println("hello");
} 
```

Enter fullscreen mode Exit fullscreen mode

这有点痛苦，所以有些 Java 程序员喜欢只使用“未检查的”错误，这样他们就不必显式声明每个方法可以抛出什么。

```
/* This error is unchecked because it inherits RuntimeException */
class FooException extends RuntimeException {
  private string path;
  private string details;
    // ...
}

public static void readConf() {
    try {
        FileInputStream fs = new FileInputStream("foo.cfg");
    }
    catch (FileNotFoundException e) {
        throw new FooException("foo.cfg", "file not found");
    }
}

// Look Ma, no 'throws' declaration!
public static void main(String[] args) {
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意我们必须如何包装`FileNotFoundException`并重新抛出我们自己的错误。

# 其他语言

我本可以谈论函数式语言，在那里你使用一个复杂的类型系统:

```
type alias Person = { name : String , age : Maybe Int }

canBuyAlcohol user =
  case user.age of
    Nothing -> false
    Just age -> (age >= 21) 
```

Enter fullscreen mode Exit fullscreen mode

或者关于 Javascript，可以在回调中处理错误:

```
const prom = new Promise((resolve, reject) => {
  if (ok) {
    resolve('value');
  } else {
    reject('error');
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

但是它与 Python 有很大的不同，所以我没有太多的话要说。

# [Python 中的](#in-python)

用 Python，一般用`try`和`except`。

但好的一面是你有很大的灵活性:

*   你可以像在 C 中一样使用`errno`:

```
try:
    open("foo.cfg")
except OSError as e:
    if e.errno == errno.ENOENT:
        sys.stderr.write("File not found") 
```

Enter fullscreen mode Exit fullscreen mode

虽然在 Python3 中你宁愿做:

```
try:
   open("foo.cfg")
except FileNotFoundError:
   print("File not found", file=sys.stderr) 
```

Enter fullscreen mode Exit fullscreen mode

*   您可以使用元组作为返回值，如 Go:

```
def run_cmd(cmd):
    result = subprocess.run(cmd, stdout=subprocess.PIPE)
    rc = result.returncode
    out = result.stdout

    return (rc == 0, out.decode().strip())

ok, out = run_cmd(["git", "rev-parse", "--abbrev-ref", "@{u}"])
if ok:
    print("You are tracking", out)
else:
    print("You are not tracking any branch") 
```

Enter fullscreen mode Exit fullscreen mode

也可以通过回调来处理错误:

```
def try_something(on_error=None);
  try:
      ...
  except Error as e:
      on_error(e)

def log_error(e):
    log.error(e)

try_something(on_error=log_error) 
```

Enter fullscreen mode Exit fullscreen mode

## 自定义异常

在 Python2 中，异常过去总是有一个名为`.message`的字符串属性。

这是因为可能会出现构造函数只接受一个参数的错误，使用一种特殊的语法，比如:

```
# Only works in Python2! 
def foo():
    raise Exception, "Oh noes!"
    # calls Exception("Oh noes") 
try:
    foo()
cacth Exception, e:
    print(e) 
```

Enter fullscreen mode Exit fullscreen mode

在 Python3 中，特殊语法消失了，所有异常现在都有一个`args`属性，它只是一个任意类型的元组。

这意味着当你创建你自己的异常类型时，你需要调用`super().__init__()`来正确设置`args`属性，比如:

```
class InvalidConfigError(RuntimeError)
    def __init__(self, path, details):
        self.path = path
        self.details = details
        super().__init__(path, details)

    def __str__(self):
        return f"{self.path}: {self.details}"

def read_config(path):
    try:
      ...
    except FileNotFoundError:
        raise InvalidConfigError(path, "not found")

    try:
      ...
    except YAMLError:
        raise InvalidConfigError(path, "invalid YAML syntax")

def main():
    try:
        read_config()
    except InvalidConfigError as e:
        sys.exit(e) 
```

Enter fullscreen mode Exit fullscreen mode

## 断言并退出

请注意，除了未捕获的异常，Python 程序还有其他终止方式。

例如，您可以使用`sys.exit(42)`使用给定的错误代码强制退出。

因为在用非零代码退出时向`stderr`显示一条消息是很常见的，所以可以使用`sys.exit("<a message here>")`显示一条错误消息，然后立即退出。

你也可以使用断言，比如:

```
def foo(self):
    assert self.bar, "Calling foo() when self.bar is empty" 
```

Enter fullscreen mode Exit fullscreen mode

然而`sys.exit()`和`assert`正在使用异常，即`SystemExit`和`AssertionError`。(唯一的区别是`sys.exit()`不打印回溯)。

因此您可以像往常一样捕获断言失败和`sys.exit`调用:

```
try:
   some_script()
except SystemExit as e:
   print("script failed with", e.code) 
```

Enter fullscreen mode Exit fullscreen mode

那么，考虑到所有这些灵活性，在 Python 中处理错误的最好方法是什么呢？

# 第二部分:具体例子

## 简介

我将用 [tsrc](https://github.com/tankerapp/tsrc) 作为例子。

代码示例取自其源代码(为了本文的目的，有时会简化一些)。

简而言之，`tsrc`是一个命令行工具，可以帮助你处理多个存储库，还包含一些与 GitLab 交互的功能。

这个项目中大约有 2000 行 Python 代码，它有三种不同的使用方式:

*   生产代码:当您键入`tsrc`命令时会执行什么
*   测试:当自动测试运行时，执行什么
*   库:我们有用 Python 写的 C.I 脚本，这些脚本重用了一些`tsrc`代码。(例如，当试图构建一个拉请求时，将所有的存储库重置到正确的分支)

我们的目标是找到在这三种情况下正确处理错误的方法。

## 层次错误

Python 程序员中的一个惯例是为他们自己的项目引发的所有异常定义一个基类。

通常类名以“Error”结尾。`tsrc`不偏离这条路线:

```
# in tsrc/errors.py 
class Error():
    def __init__(self, *args):
        super().__init__(self, *args)
        self.message = " ".join(str(x) for x in args)

    def __str__(self):
        return self.message 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的是:

*   我们定义了一个`__str__`方法，使得错误在打印出来时看起来很漂亮。当我们处理错误报告时，这将非常重要
*   基类叫做`Error`。它已经在一个`tsrc.errors`名称空间中，所以不需要像`TsrcError`这样带前缀的名称。 <sup id="fnref2">[2](#fn2)</sup>

接下来，我们有几个从`tscr.Error` :
继承的定制类

```
 class GitCommandError(tsrc.Error):
    def __init__(self, working_path, cmd, *, output=None):

class GitLabError(tsrc.Error):
    def __init__(self, status_code, message):
        ... 
```

Enter fullscreen mode Exit fullscreen mode

请注意每个错误是如何用一组特定的属性构造的。

如果一个`git`命令失败，你需要知道工作目录、运行的确切命令和命令的输出(如果你捕获了它)。

对于`GitLabError`也是一样:当对 GitLab API 的请求失败时，您会想知道 HTTP 状态代码和响应的内容。

## 什么时候扔

让我们看一个具体的例子:`tsrc sync`命令。

这是一个同步当前工作空间的所有存储库的命令。

在编写该命令的实现时，我们可能会遇到一些错误:

*   网络中断，所以所有对`git fetch`的呼叫都失败了
*   一些存储库是脏的，所以对`git merge`的调用失败
*   ...
*   诸如此类。

这些错误是我们不希望发生的:

*   语法错误
*   一个 IndexError，因为我们使用了`my_list[3]`，而列表只有两个元素
*   AssertionError，因为我们触发了`assert`故障
*   我们忘记捕捉第三方库中的错误

所以这里是我们在`tsrc` : *中遵循的规则:每当*发生我们预期的错误时，我们抛出`tsrc.Error`的派生类。

例如，当我们运行`git`命令时，不是让`CalledProcessError`不被捕获，而是确保使用我们自己的`GitCommandError`类:

```
def run_git(working_dir, *cmd):

    process = subprocess.run(cmd, cwd=working_dir)

    if process.returncode != 0:
        raise GitCommandError(working_dir, cmd) 
```

Enter fullscreen mode Exit fullscreen mode

这意味着`tsrc`中出现的异常不是“异常”,在某种意义上，它们甚至可能在“正常”使用期间发生。

这也意味着如果出现了一个*没有*从`tscr.Error`继承的错误，那么我们的产品代码中可能有一个 bug。

## 主()包装器

在`tsrc` :
的
`main()`入口点中明确了“预期”和“意外”错误之间的区别

```
def main_func():
    """ Deals with command-line arguments
    and calls appropriate functions
    """

def main():
    try:
        main_func()
    except tsrc.Error as e:
        # "expected" failure, display it and exit
        if e.message:
            print("Error:", e.message)
        sys.exit(1)

if __name__ == "__main__":
    main() 
```

Enter fullscreen mode Exit fullscreen mode

因此，程序可以以下列方式终止:

*   一个`tscr.Error`实例已经被引发并且未被捕获:如果它不为空，则显示它的消息，并以非零返回代码退出。
*   出现了另一种我们没有发现的异常:让程序崩溃。
*   有人打电话给`sys.exit()`:就像打电话的人期望的那样终止。

因此，只要我们注意如何构造我们提出的`tsrc.Error`类的实例，最终用户将得到一个好的错误消息，告诉他发生了什么。

例如:

```
def load_manifest();
    ....
    raise tsrc.Error("Invalid manifest file ...")

def main_sync():
    ...
    load_manifest() 
```

Enter fullscreen mode Exit fullscreen mode

```
$ tsrc sync Error: Invalid manifest file 
```

Enter fullscreen mode Exit fullscreen mode

注意回溯是如何隐藏的。

另一方面，如果我们引发了另一种类型的异常，将会打印出一个回溯*来让我们调查这个错误。*

## CI 脚本中的错误处理

在编写 CI 代码时，我们可以使用完全相同的技术。

在我们的例子中，脚本的消费者将是读取管道输出的开发人员，试图找出合并请求被拒绝的原因。

因此，正确处理错误至关重要。

好消息是，我们*也*有“预期的”失败，比如代码无法编译，或者测试失败，我们有“意外的”失败，比如网络中断，或者脚本本身有问题。

因此，我们可以重用相同的技术，其中`notify()`是负责告诉开发人员构建结果的函数(通过电子邮件，或者在合并请求上添加注释):

```
class BuildFailed(CIError):
    ...

class TestsFailed(CIError):
    ...

def notify(message):
    ...

def ci():
    fetch_sources()
    build()
    run_tests()

def main():
    try:
      ci()
    except BuildFailed:
        notify("build failed")
    except TestsFailed:
        notify("tests failed")
    except Exception as e:
        print_bactkrace()
        notify("build scripts may be broken! Ask help from a build farm guru")
    notify("Pipeline suceeded. Congrats") 
```

Enter fullscreen mode Exit fullscreen mode

注意:这与`tsrc`本身无关，但是我认为也告诉你关于 CI 脚本是个好主意:)

## 使用 tsrc 作为库

因为我们有这么好的错误层次结构，并且每种类型的错误都包含容易访问的信息作为类的属性，所以`tsrc`函数的调用者在试图处理错误时有很大的自由度。

他们可以进行非常细粒度的错误处理，就像这样:

```
try:
    push_action = tsrc.PushAction()
    push_action.accept_merge_request()
except tsrc.GitLabError as error:
    if error.status_code == 405:
        # GitLab denied the merge request 
```

Enter fullscreen mode Exit fullscreen mode

他们只能捕捉到`tsrc.Error`个错误(因为其他类型表示一个 bug)，并重新引发他们自己的类型:

```
try:
    push_action = tsrc.PushAction()
    push_action.accept_merge_request()
except tsrc.Error as error:
    raise FooError() 
```

Enter fullscreen mode Exit fullscreen mode

或者他们可以完全忽略错误，让代码的调用者来处理错误。

## 测试错误处理

由于异常的类型、错误消息和属性非常重要，我们应该对它们进行测试。

用`pytest`我们可以这样写代码:

```
import pytest

def test_reading_config_file():
    with pytest.raises(InvalidConfigError) as e:
        tsrc.config.read_config("nosuchfile")
    assert e.value.path == "nosuchfile" 
```

Enter fullscreen mode Exit fullscreen mode

注意，`pytest`返回一个包装了原始异常的`ExceptionInfo`实例，所以我们必须使用`.value`属性。 <sup id="fnref3">[3](#fn3)</sup>

我们也可以像这样检查各种命令的返回代码:

```
 def test_sync_with_errors(tsrc_cli):
    # Arrange a workspace where a branch has diverged
    ...

    # Call the sync script:
    with pytest.raises(SystemExit) as e:
        main_sync()

    # Ensure it has failed
    assert e.value.code != 0 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

一旦应用了我在这里描述的技术，Python 中良好的错误处理是非常容易的。

它们看起来都很简单，但是我花了很长时间才发现它们，所以我希望它们对你有用。

干杯！

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。

* * *

1.  完整的列表在[文档](https://docs.pytest.org/en/latest/usage.html) [↩](#fnref1) 中

2.  这种反模式如此常见，以至于它有一个名字:“蓝精灵命名约定” [↩](#fnref2)

3.  更多信息见 [pytest 文档](https://docs.pytest.org/en/latest/assert.html#assertions-about-expected-exceptions) [↩](#fnref3)