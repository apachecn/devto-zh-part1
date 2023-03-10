# Shell 脚本很重要

> 原文：<https://dev.to/thiht/shell-scripts-matter>

[![Bash Logo](img/c3cdd8eb895df1f8316e2f78a510ba12.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5zmS9pUp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/fe6QlZv.png)

贝壳是一种奇怪的动物。尽管它违背了软件工程中的每一个当前趋势(强类型，编译检查胜过运行时检查，...)，shell 脚本一直存在，并且仍然是每个开发人员生活中的重要组成部分。

关于 shell 脚本的奇怪之处在于，当谈到 shell 脚本时，即使是良好实践的强烈拥护者也会欣然忘记他们所知道的一切。

> 版本控制？何必呢，这是一次性代码。
> 
> 代码质量？那只是个 shell 脚本，反正都是垃圾。
> 
> 测试？那鸿没有合适的工具来做这件事。

错了，错了，错了。Shell 脚本有价值。你为真实代码所做的一切都应该为非平凡的 shell 脚本所做，即使是一次性脚本。这包括版本控制、代码审查、持续集成、静态代码分析和测试。

这里总结了在编写 shell 脚本时可以做和应该做的所有事情。

**注意:**本文将使用 Bash 作为参考 shell。大部分内容可以移植到其他 POSIX 兼容的 shells 中。

## 保持你的脚本在版本控制中

将 shell 脚本置于版本控制之下有多种好处:

*   它构成了一个图书馆。Shell 脚本可能很难编写。如果某处有困难的参考，当你的同事需要时，他们会感谢你。你应该尽快在某个地方建立一个“shell-scripts”库。
*   可以对它们进行适当的审查。使用 shell 脚本很容易出错，而且可能会非常有害。对于 shell 脚本来说，代码审查应该是强制性的，就像对于任何其他代码一样。
*   它们是可以改进的。我不会给你解释什么是版本控制。但是有了版本化的 shell 脚本，定期改进它们就很容易了。

请从现在开始，在运行之前，**版本化所有的 shell 脚本**。**在生产中执行脚本之前，让某人优先审查你的脚本**。这不是浪费你同事的时间，而是为团队节省时间。

## 使用 ShellCheck 提高你的脚本质量

虽然您可以使用命令`bash -n`检查脚本的语法有效性，但是还有很多强大的工具。

[ShellCheck](https://www.shellcheck.net/) 是一个 shell 脚本的静态代码分析工具。这真的是一个很棒的工具，在你使用它的时候，它会帮助你提高你的技能。**所以一定要用它**。你可以[在你的机器](https://github.com/koalaman/shellcheck#installing)上全局安装它，在你的[持续集成](https://github.com/koalaman/shellcheck#travis-ci-setup)中使用它，它甚至[与大多数主流编辑器](https://github.com/koalaman/shellcheck#in-your-editor)完美集成。使用 ShellCheck 真的没有坏处，它可以把你从自我中拯救出来。

如果 Steam 在 2015 年使用 ShellCheck，[这条生产线将永远无法投产](https://linux.slashdot.org/story/15/01/16/1429201/steam-for-linux-bug-wipes-out-all-of-a-users-files) :

```
rm -rf "$STEAMROOT/"* 
```

Enter fullscreen mode Exit fullscreen mode

该代码违反了 ShellCheck 的 [SC2115 规则](https://github.com/koalaman/shellcheck/wiki/SC2115)。

## 使用 Bash 非官方严格模式

非官方的严格模式来自 Aaron Maxwell 的文章“[使用非官方的 Bash 严格模式(除非你喜欢调试)](http://redsymbol.net/articles/unofficial-bash-strict-mode/)”。他建议每个 Bash 脚本都以下面几行开始:

```
#!/bin/bash
set -euo pipefail
IFS=$'\n\t' 
```

Enter fullscreen mode Exit fullscreen mode

*   如果任何命令返回非零状态码，`set -e`将退出脚本。即使没有发生错误，要防止选件触发返回非零状态代码的命令，有两种解决方案:

*   使用`|| true`模式:

```
command_returning_non_zero || true 
```

Enter fullscreen mode Exit fullscreen mode

*   暂时禁用该选项:

```
set +e
command_returning_non_zero
set -e 
```

Enter fullscreen mode Exit fullscreen mode

*   `set -u`将防止使用未定义的变量。在未定义位置参数的情况下(`$1`、`$2`)，...)，您可以用[参数扩展](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)构造给它们一个默认值:

```
my_arg=${1:-"default"} 
```

Enter fullscreen mode Exit fullscreen mode

*   `set -o pipefail`将在第一个非零状态代码时强制管道失败。

*   使迭代和分裂不那么令人惊讶，尤其是在循环的情况下。这个变量的缺省值通常是`IFS=$' \n\t'`,但是空格作为分隔符经常会产生混淆的结果。

阅读[原文](http://redsymbol.net/articles/unofficial-bash-strict-mode/)了解更多详情以及使用严格模式时常见挑战的[解决方案！](http://redsymbol.net/articles/unofficial-bash-strict-mode/#issues-and-solutions)

非官方的严格模式比我们以前看到的更具侵入性，可能很难处理，但从长远来看这是值得的。花点时间试试吧。

## 做一些清理工作！

当脚本被中断时，无论是因为用户的操作还是因为发生了不好的事情，大多数 shell 脚本都不会清理它们的混乱。在最坏的情况下，他们可能不会重新启动他们不得不暂时禁用的服务。考虑到用`trap`命令执行一些清理和错误捕捉是多么容易，这是一个遗憾。

在“[Exit Traps 如何让 Bash 脚本更加健壮可靠](http://redsymbol.net/articles/bash-exit-traps/)”一文中，Aaron Maxwell 给出了一些很好的建议。

总是在您的 shell 脚本中添加以下内容:

```
cleanup() {
    # ...
}
trap cleanup EXIT 
```

Enter fullscreen mode Exit fullscreen mode

脚本一退出，`trap`命令就会执行`cleanup`函数。在这个函数中，你可以删除临时文件，重启服务，或者任何与你的脚本相关的东西。

## 用 shUnit2 测试你的脚本

shUnit2 是一个针对 shell 脚本的单元测试框架。灵感来源于 JUnit。它可以在标准的软件仓库中找到，所以你可以在基于 Ubuntu 的发行版上用`apt-get install shunit2`安装它。

shUnit2 由一个 shell 脚本组成，您可以在您的测试文件中`source`。要使用它，有多种方法。为了不弄乱主脚本，我更喜欢在单独的文件中编写测试。这意味着我将拥有一个`script.sh`文件和一个`test_script.sh`文件。

下面是一个提供两个数相加函数的脚本示例。

`add.sh`必须具有以下结构:

```
add() {
    local a=$1
    local b=$2
    echo $(( a + b ))
}

if [[ "${BASH_SOURCE[0]}" = "$0" ]]; then
    # Main code of the script
    add $1 $2
fi 
```

Enter fullscreen mode Exit fullscreen mode

`[[ "${BASH_SOURCE[0]}" = "$0" ]]`测试是用来只在直接执行脚本的时候执行主代码，而不是`source` d。

`test_add.sh`会是这样的:

```
. ./add.sh

test_add() {
    actual=$(add 5 8)
    expected=13
    assertEquals "$expected" "$actual"
}

. shunit2 
```

Enter fullscreen mode Exit fullscreen mode

首先，测试文件`source`是主文件`add.sh`(在 Bash 中，`.`是`source`的别名)。它声明的函数在测试脚本中是可用的。

实际的测试是以`test`开头的简单函数。最后，全球安装的`shunit2`是`source` d，表演它的[魔术](http://ssb.stsci.edu/testing/shunit2/shunit2.html#quickstart)。

然后可以执行测试文件:

```
$ bash test_add.sh
test_add

Ran 1 test.

OK 
```

Enter fullscreen mode Exit fullscreen mode

shUnit2 能做什么的细节在它的文档中有解释。

除了 shUnit2 还有其他选择，比如蝙蝠或者 t2，但是我还没有机会使用它们。但是它们的用法应该相对相似。这一节的重点是**测试 shell 脚本是可行的，并且应该完成**，无论您最终选择什么解决方案。

## 记录你的脚本正在做什么

在过去，我犯了一个错误，没有记录任何事情。我喜欢运行一个脚本，看着它神奇地工作，没有任何难看的东西出现在控制台上。我错了，因为当事情不按预期运行时，就不可能知道发生了什么。运行一个脚本不应该感觉像魔术，它必须有点冗长，可以理解。为此，请**在您的脚本**中尽可能多地记录。

为此，我通常会在脚本中添加以下几行:

```
readonly LOG_FILE="/tmp/$(basename "$0").log"
info()    { echo "[INFO] $*" | tee -a "$LOG_FILE" >&2 ; }
warning() { echo "[WARNING] $*" | tee -a "$LOG_FILE" >&2 ; }
error()   { echo "[ERROR] $*" | tee -a "$LOG_FILE" >&2 ; }
fatal()   { echo "[FATAL] $*" | tee -a "$LOG_FILE" >&2 ; exit 1 ; } 
```

Enter fullscreen mode Exit fullscreen mode

这个微小的日志框架允许轻松跟踪脚本执行过程中发生的任何事情。日志记录变得像写`info "Executing this and that..."`一样简单。然后很容易在日志文件上找到特定的东西。您可以根据需要随意改进这些函数，包括日期、调用函数名(带`$FUNCNAME`)等。

我不使用内置的 [`logger`](http://man7.org/linux/man-pages/man1/logger.1.html) ，因为它需要特殊权限才能写入`/var/log`并且我不喜欢它的用法。在`/tmp`中写入日志文件通常就足够了。对于`cron`剧本，你也许应该调查一下`logger`。

根据需要使用您调用的命令的`-v`或`--verbose`来提高日志记录的质量。

## 学会调试你的脚本

除了记录日志之外，调试 shell 脚本最简单的方法是用`bash -x`运行它。另一种方法是在脚本内部使用`set -x`。这个选项将使 Bash 在执行命令之前打印每个命令，用它们的实际值替换变量。与非官方的严格模式一起使用，这种方法有助于了解脚本中的情况，降低破坏环境的风险。

同样值得一提的是，Bash 有一些调试器，例如 [bashdb](http://bashdb.sourceforge.net/bashdb.html) 。bashdb 的工作方式和 gdb 一样，可以用来添加断点，切换到分步执行，显示变量的值等。您可以通过视频“[使用 bashdb 调试您的 Shell 脚本](https://www.youtube.com/watch?v=jbOQJDSTksA):

[https://www.youtube.com/embed/jbOQJDSTksA](https://www.youtube.com/embed/jbOQJDSTksA)

T5”了解如何使用 BashDB

## 记录你的脚本

任何 shell 脚本都应该有一个`--help`选项。这好像不容易？是的，多亏了下面的魔法:

```
#/ Usage: add <first number> <second number>
#/ Compute the sum of two numbers
usage() {
    grep '^#/' "$0" | cut -c4-
    exit 0
}
expr "$*" : ".*--help" > /dev/null && usage 
```

Enter fullscreen mode Exit fullscreen mode

`usage`函数将打印以`#/`注释开始的每一行，不带前缀。

`expr`命令将检查所有参数串联后的字符串是否包含`--help`。如果是的话，它会叫`usage`。

这肯定不是解析参数的最干净的方式，但是这种快速的方法确保您将添加一个`--help`标志。

出于良好实践的考虑，[这篇 StackOverflow 文章](https://stackoverflow.com/a/14203146/1544176)解释了如何使用`while` / `case` / `shift`构造来正确解析脚本的参数。

为了从你的评论中生成 HTML 文档，你也可以检查 [shocco.sh](http://rtomayko.github.io/shocco/) ，它启发了[上面的技巧](https://github.com/rtomayko/shocco/blob/e4660e563559d5bd9acbca42b61115e72b54667f/shocco.sh#L54-L57)。

## 随机建议

以下是我从艰难的道路中学到的随机良好实践的列表。我会边走边解释每个建议背后的基本原理。

### 使用 Bash 进行脚本编写

默认使用 Bash，必要时使用 sh。试着忘记 Ksh、Zsh 或 Fish，除非有真正好的理由使用它们。这种选择不仅可以确保你的脚本几乎在任何地方都能工作，还可以促进整个团队对脚本的理解。你不为自己写制作脚本。

### 使用 Bashisms

如果你用 Bash，不要半用。使用[参数扩展](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)。使用[本地](http://tldp.org/LDP/abs/html/localvar.html)和[只读](https://ss64.com/bash/readonly.html)变量。使用[改进的条件表达式](https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html)。

### 报出你的变量

即使你知道引用是不必要的，总是引用变量也是一个好习惯。唯一的例外是当您特别希望发生扩展时。更多关于[分词](https://github.com/koalaman/shellcheck/wiki/SC2086)。

### 命名你的参数

这不言而喻，但明确命名参数(`$1`，`$2`，...)使代码自文档化并有助于可读性。Bash 的参数扩展是为位置参数命名和分配默认值的绝佳选择:

```
my_arg=${1:-default} 
```

Enter fullscreen mode Exit fullscreen mode

### 使用子外壳来控制你的全局范围

一个例子胜过千言万语:

```
var=1
echo $var
(
    echo $var
    var=5
    echo $var
)
echo $var 
```

Enter fullscreen mode Exit fullscreen mode

将打印:

```
1
1
5
1 
```

Enter fullscreen mode Exit fullscreen mode

我的主要用途是当我需要临时修改`$IFS`(例如迭代简单的类似 CSV 的文件)并随后将其重置为初始值时。

### 使用模板

这个脚本模板总结了本文中分享的每个片段。我相信这是任何剧本的良好基础。

```
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'

#/ Usage:
#/ Description:
#/ Examples:
#/ Options:
#/   --help: Display this help message
usage() { grep '^#/' "$0" | cut -c4- ; exit 0 ; }
expr "$*" : ".*--help" > /dev/null && usage

readonly LOG_FILE="/tmp/$(basename "$0").log"
info()    { echo "[INFO] $*" | tee -a "$LOG_FILE" >&2 ; }
warning() { echo "[WARNING] $*" | tee -a "$LOG_FILE" >&2 ; }
error()   { echo "[ERROR] $*" | tee -a "$LOG_FILE" >&2 ; }
fatal()   { echo "[FATAL] $*" | tee -a "$LOG_FILE" >&2 ; exit 1 ; }

cleanup() {
    # Remove temporary files
    # Restart services
    # ...
}

if [[ "${BASH_SOURCE[0]}" = "$0" ]]; then trap cleanup EXIT
    # Script goes here
    # ...
fi 
```

Enter fullscreen mode Exit fullscreen mode

### 消息灵通

Shell 脚本现在已经不怎么流行了。这是一个很好的理由去阅读关于这个话题的东西，这样很容易跟上。以下是一些有趣的资源:

*   我打赌你们中几乎没有人读过它，但它是一个很好的信息来源！我保证这并不难理解。
*   [@UnixTooltip](https://twitter.com/UnixToolTip) 在 Twitter 上，不断给出提示和窍门。
*   [stack overflow 上投票最多的 Bash 问题](https://stackoverflow.com/questions/tagged/bash?sort=votes&pageSize=15)
*   关于 StackOverflow 的 Bash 参与文档有一些有趣的例子。
*   ShellCheck 的 wiki 是一个很好的资源，可以帮助你了解什么不该做，以及为什么不做。
*   [黑客新闻](https://news.ycombinator.com/news)和[/r/编程](https://www.reddit.com/r/programming/)，偶尔会有这方面的文章冒出来。

* * *

我希望这篇文章揭示了 shell 脚本的可能性。工具在那里。你知道惯例。现在就看你自己了，让你和你的团队享受编写脚本的乐趣吧！