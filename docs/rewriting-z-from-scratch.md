# 从头开始重写 z

> 原文：<https://dev.to/dmerejkowsky/rewriting-z-from-scratch>

z 是一个工具，当你使用你的终端时，它会记住你正在访问的所有目录，然后让你可以快速跳转到这些目录。

让我们试着从头开始重写这个功能，也许我们可以通过这种方式学到一些东西。

# 动机

大约一年前我开始使用`z`。

除了一件事，它工作得很好。

假设我有两个匹配`spam`、`/path/to/spam-egss`和`/other path/to/eggs-with-spam`的目录。

`z`将根据每个目录的访问频率和最近的访问时间为其计算分数。

所以，理论上:

> 具有低等级但最近被访问的目录将会**快速**具有比很久以前被频繁访问的目录更高的等级。(摘自`z`的自述，重点是我的)

但是实际上，如果你从`spam-eggs`开始工作到`eggs-with-spam`，你会几次得到`z spam`的错误答案(“快速”并不意味着“立即”)

此外，*因为*算法使用访问日期，您不能(容易地)预测它将选择哪个目录。

# 第一步:选择数据库格式

`z`使用的数据库看起来像这样

```
/other/path/to/eggs-with-spam|24|1495372880
/path/to/spam-eggs|4|1495372491
... 
```

Enter fullscreen mode Exit fullscreen mode

有路径，它被访问的次数和最后一次访问的时间戳。

我们将使用`json`，由于我们想要一个不依赖于时间的算法，我们将只存储访问次数:

```
{
  "/other/path/to/eggs-with-spam": 24,
  "/path/to/spam-eggs": 4,
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

为什么是`json`？因为它在任何语言中都很容易阅读和书写(包括漂亮的字体)，同时仍然可以被人类编辑。

如果我们需要，仍然有可能添加新数据。

# 第二步:决定如何处理不存在的目录

假设你在`/tmp/foo`工作，没有其他目录叫`foo`。
你还创建了目录`/tmp/foo/src`和`/tmp/foo/include`。

有了`z`，一旦将`/tmp/foo`、`/tmp/foo/src/`和`/tmp/foo/include`三条路径存储在数据库中，它们将永远留在那里。

这意味着，如果您删除了`/tmp/foo`，`z foo`仍然会尝试进入不存在的目录。但是，如果您稍后重新创建`/tmp/foo`，那么`z foo`将再次工作。

在我们的重写中，我们将用另一种方式处理这种情况:

*   第一，如果我们不能`cd`到一个目录，我们将立即从数据库中删除它
*   第二，我们将使显式“清除”数据库成为可能:在我们的例子中，这意味着在一个步骤中删除所有三个路径。为此，我们将查看数据库中的每个路径，并删除那些不再存在的路径。

# 第三步:编写命令行工具

我决定将这个工具命名为`cwd-history`，并使用类似于`git`的命令行语法，对于不同的动作，有几个可能的“动词”:

*   `cwd-history list`:以正确的顺序显示路径
*   `cwd-history add PATH`:添加数据库的路径
*   `cwd-history remove PATH`:从数据库中删除路径
*   `cwd-history edit`:直接编辑 json 文件(可能会很方便)
*   `cwd-history clean`:从数据库中删除不存在的路径

想看的话代码在 github 上[。](https://github.com/dmerejkowsky/dotfiles/blob/master/bin/cwd-history)

一些注意事项:

```
def get_db_path():
    zsh_share_path = os.path.expanduser("~/.local/share/zsh")
    os.makedirs(zsh_share_path, exist_ok=True) 
```

Enter fullscreen mode Exit fullscreen mode

*   我们尊重 XDG 标准(实际上我们也应该检查`XDG_DATA_HOME`环境变量，但这比污染`$HOME`要好)。

*   我们使用`exist_ok` <sup id="fnref1">[1](#fn1)</sup> 作为`os.makedirs`的参数，这样如果目录已经存在，命令就不会失败。

```
def add_to_db(path):
  path = os.path.realpath(path) 
```

Enter fullscreen mode Exit fullscreen mode

*   我们使用`os.path.realpath`来确保所有的符号链接都被解析。这意味着我们的数据库中不会有重复的路径。

```
def clean_db():
    cleaned = 0
    entries = read_db()
    for path in list(entries.keys()):
        if not os.path.exists(path):
            cleaned += 1
            del entries[path]
    if cleaned:
        print("Cleaned", cleaned, "entries")
        write_db(entries)
    else:
        print("Nothing to do") 
```

Enter fullscreen mode Exit fullscreen mode

*   我们试图在一行中提供尽可能多的信息，显示被清理的目录数量，或者什么也没做。列出所有被删除的路径会太冗长，如果我们不显示任何内容，我们将永远无法确定该命令是否真的有效。

```
import operator

def list_db():
    entries = read_db()
    sorted_entries = sorted(entries.items(),
                            key=operator.itemgetter(1))
print("\n".join(x[0] for x in sorted_entries)) 
```

Enter fullscreen mode Exit fullscreen mode

*   我们使用`operator.itemgetter`作为`lambda x: x[1]`的快捷方式
*   因此，我们根据路径被访问的次数对它们进行排序。
*   我们只显示路径，每个路径由一行分隔。这将使`cwd-history list`的输出非常容易解析。(例如，我们可以添加一个`--verbose`选项来显示完整的数据库数据)

# 第四步:钩入 zsh

每次`zsh`改变当前工作目录时，我们都要调用`cwd-history add $(cwd)`。

这是通过编写一个函数并将其添加到一个特殊的数组:
来实现的

```
function register_cwd() {
  cwd-history add "$(pwd)"
}
typeset -gaU chpwd_functions
chpwd_functions+=register_cwd 
```

Enter fullscreen mode Exit fullscreen mode

*   注意`chpwd_functions`是一个 zsh 数组，所以我们必须使用`typeset`。我们用`-g`来调用它，因为`chpwd_functions`是一个*全局值*和`-U`来确保列表中不包含重复项。不太清楚`-a`是干什么用的，抱歉。

# 第五步:过滤结果

我们将让用户通过连接到`fzf`来选择，而不是试图猜测最佳结果。

我已经在[的上一篇文章](https://dmerej.info/blog/post/fzf-for-the-win/)中谈到过`fzf`。其要点是，您可以将任何命令作为输入传递给`fzf`，并让用户从列表中交互式地选择一个结果。

实现是这样的:

```
 cwd_list=$(cwd-history list)
  ret="$(echo $cwd_list| fzf --no-sort --tac --query=${1})"
  cd "${ret}"
  if [[ $? -ne 0 ]]; then cwd-history remove "${ret}"
  fi 
```

Enter fullscreen mode Exit fullscreen mode

注意事项:

*   我们使用`fzf`和`--query`，这样你可以输入`z foo`，或者只输入`z`，并且是在`fzf`窗口中输入`foo`模式之后

*   由于最有可能的答案在`cwd-history list`命令的*底部*，我们使用`fzf`和`--tac`选项 <sup id="fnref2">[2](#fn2)</sup> 。我们还需要告诉`fzf`到*而不是*预先对输入进行排序。

*   如前所述，如果我们不能`cd`进入数据库，我们就从数据库中删除路径。(顺便说一下，这也包括我们没有权限访问文件夹的情况)

# 第六步:从外壳到 neovim，反之亦然

最后一件事。因为我在 neovim 中完成了大部分编辑工作，所以我一直在寻找在我的 shell 和编辑器中实现类似行为的方法。

因此，让我们看看如何将访问过的目录信息从一个工具转移到另一个工具。

## 从 neovim 到 shell

这是一种丑陋的黑客行为。

首先，我添加了一个 auto 命令，将 neovim 的电流直接写入一个硬编码文件`/tmp/` :

```
" Write cwd when leaving
function! WriteCWD()
  call writefile([getcwd()], "/tmp/nvim-cwd")
endfunction

autocmd VimLeave * silent call WriteCWD() 
```

Enter fullscreen mode Exit fullscreen mode

然后，我将对`neovim`的调用封装在一个函数中，该函数读取文件的内容，然后调用`cd`，但前提是`neovim`成功退出。

```
# Change working dir when Vim exits
function neovim_wrapper() {
  nvim $*
  if [[ $? -eq 0 ]]; then cd "$(cat /tmp/nvim-cwd 2>/dev/null || echo .)"
  fi
} 
```

Enter fullscreen mode Exit fullscreen mode

## 从外壳到内奥维姆

要走另一条路，我就从 [fzf.vim](https://github.com/junegunn/fzf.vim) 插件中调用`fzf#run()`以`cwd-history list`为源、`:tcd`为宿: <sup id="fnref3">[3](#fn3)</sup>

```
function! ListWorkingDirs()
  call fzf#run({
 \ 'source': "cwd-history list",
 \ 'sink': "tcd"
 \})
endfunction

command! -nargs=0 ListWorkingDirs :call ListWorkingDirs()
nnoremap <leader>l :ListWorkingDirs<CR> 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

现在你知道了。

`z`是 458 行`zsh`代码。

我的重新实现是 Python 的 75 行，`zsh`的 6 行，`vimscript`的 8 行。

它在 shell 和编辑器之间共享数据库，永远不会出错，并且数据库保持干净，可以手工编辑。

我觉得还不错:)

PS1:你也可以用几行代码直接用`fzf`使用`z`，如 [fzf wiki](https://github.com/junegunn/fzf/wiki/examples#integration-with-z) 所示

PS2:如果你好奇的话，我的博客上有这篇文章的后续。

* * *

1.  这里自 Python 3.2 [↩](#fnref1)

2.  这是反向的，你明白了吗？ [↩](#fnref2)

3.  `tcd`是 neovim 独有的功能。我已经在我关于 vim，cwd 和 neo vimT3】↩的[帖子中提到过](https://dmerej.info/blog/post/vim-cwd-and-neovim/)