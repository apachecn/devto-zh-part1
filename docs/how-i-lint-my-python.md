# 我如何剥制我的蟒蛇皮

> 原文：<https://dev.to/dmerejkowsky/how-i-lint-my-python>

*最初发表于[我的博客](https://dmerej.info/blog/post/how-i-lint/)。*

这是一篇描述我如何 lint 我的 Python 代码的短文。您会发现这不仅仅是在 IDE 中安装一些插件，而是一点点脚本代码。

# 什么是林挺？

林挺是运行一个程序来分析代码中潜在错误的过程。

您给它您的源的路径，它输出一个消息列表。

# IDE 里的林挺怎么了？

没什么！只是我更喜欢在需要的时候自由地运行 linters，当你进行持续集成时，脚本是有用的。

# 我用的棉绒

我使用了其中的几个，因为它们都很好地相互补充:

*   `pycodestyle`为检查样式 <sup id="fnref1">[1](#fn1)</sup>
*   `pyflakes`用于快速静态分析。
*   寻找过于复杂且需要重构的代码
*   `pylint`对于其他一切。

# 寻找源头

你需要做的第一件事是获得你想要运行 linters 的文件列表。

这并不容易，因为每一个 linter 都有自己的语法，而且有些代码你*知道*你不想让 linter 在上面运行。

为此，我用`path.py`
写了一点 Python 代码

```
# in ci/utils.py 
import path

def collect_sources(ignore_func):
    top_path = path.Path(".")
    for py_path in top_path.walkfiles("*.py"):
        py_path = py_path.normpath()  # get rid of the leading '.'
        if not ignore_func(py_path):
            yield py_path 
```

Enter fullscreen mode Exit fullscreen mode

`collect_sources`函数将回调作为参数，允许忽略一些文件。

# 运行 pycodestyle

实际上，您可能不需要使用`collect_sources`函数来运行`pycodestyle`。就跑:

```
$  pycodestyle . 
```

Enter fullscreen mode Exit fullscreen mode

从项目的根开始。

它会找到项目中的每个`.py`文件，并检查每个文件的样式。

如果需要排除目录，可以在`setup.cfg`或`tox.ini`配置文件中添加一个`[pycodestyle]`段:

```
[pycodestyle]
exclude=<some directory> 
```

Enter fullscreen mode Exit fullscreen mode

# 运行 pyflakes

```
# in ci/run-pyflakes 
import subprocess

from .utils import collect_sources

def ignore(p):
    """ Ignore hidden and test files """
    parts = p.splitall()
    if any(x.startswith(".") for x in parts):
        return True
    if 'test' in parts:
        return True
    return False

def run_pyflakes():
    cmd = ["pyflakes"]
    cmd.extend(collect_sources(ignore_func=ignore)
    return subprocess.call(cmd)

if __name__ == "__main__":
    rc = run_pyflakes()
    sys.exit(rc) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我必须确保`pyflakes`不会在测试文件上运行，因为它有时会被`pytest`装置的魔力弄糊涂。

# 跑步麦凯

`mccabe`自带一个`main()`函数，但是只能在一个文件上运行。

所以我不得不利用之前写的`collect_sources`重写主函数。当我在做的时候，我稍微调整了一下输出。

```
# In ci/run-mccabe.py 
import ast
import mccabe

def process(py_source, max_complexity):
    code = py_source.text()
    tree = compile(code, py_source, "exec", ast.PyCF_ONLY_AST)
    visitor = mccabe.PathGraphingAstVisitor()
    visitor.preorder(tree, visitor)
    for graph in visitor.graphs.values():
        if graph.complexity() > max_complexity:
            text = "{}:{}:{} {} {}"
            return text.format(py_source, graph.lineno, graph.column, graph.entity,
                               graph.complexity())

def main():
    max_complexity = int(sys.argv[1])
    ok = True
    for py_source in collect_sources():
        error = process(py_source, max_complexity)
        if error:
            ok = False
            print(error)
    if not ok:
        sys.exit(1)

if __name__ == "__main__":
        main() 
```

Enter fullscreen mode Exit fullscreen mode

请注意复杂性阈值是如何作为命令行参数直接传递的。

它将允许您微调该参数。对我来说，10 是一个很好的值，但是根据您的代码库，您可能需要降低或增加它。

```
#  before
$  python -m mccabe foo/bar.py -m 10
4:0: 'complex_fun' 12 #  after
$  ./ci/lint.sh
$  bad.py:4:0 complex_fun 12 
```

Enter fullscreen mode Exit fullscreen mode

# 运行 pylint

我已经写了一篇关于皮林特的文章。简而言之，您应该仔细编辑您的`.pylintrc`文件，并确保正确收集您的 Python 包。

# 把所有的东西放在一起

只是一个简单的 bash 脚本:

```
#!/bin/bash -xe

pycodestyle .
python bin/run-pyflakes.py
python bin/run-mccabe.py 10
pylint mymodule 
```

Enter fullscreen mode Exit fullscreen mode

你可能会奇怪我为什么不用`flake8`或者`prospector`之类的工具。

嗯，`flake8`不运行`pylint`。

另一方面，它很好，但是强迫你使用所有其他 linters 的特定版本，并且不容易配置。另外，我是在写完剧本后*才发现它的存在:)*

此外，我已经停止尝试使用工具，如`tox`或`invoke`。我不需要测试几个 Python 版本，(这是使用`tox`的主要原因)，在`invoke`中指定命令的额外复杂性不值得。

最后，即使在 Windows 上，我也主要是在`git-bash`中运行命令，所以我不介意用 bash 编写脚本。

# 从 vim/neovim 运行棉绒

就用:

```
set makeprg=ci/lint.sh 
```

Enter fullscreen mode Exit fullscreen mode

然后运行`:make`。

所有的问题都会出现在快速修复窗口中。

正如我在开始所说的，我不喜欢我的 linters 在我编辑的时候运行，所以我同意 linters 同步运行，但是如果你需要的话，可能有一个插件可以让你这样做。

干杯！

* * *

1.  这个工具以前叫`pep8`。它因圭多亲自创办的一期《T2》而更名。 [↩](#fnref1)