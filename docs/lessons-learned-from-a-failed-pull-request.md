# 从失败的拉取请求中吸取的教训

> 原文：<https://dev.to/dmerejkowsky/lessons-learned-from-a-failed-pull-request>

*首发于[我的博客](https://dmerej.info/blog/post/lessons-learned-from-a-failed-pull-request/)。*

# 简介:netrc 文件

*如果你已经对`~/.netrc`文件了如指掌，可以直接跳到下一部分*

但是对于那些不知道的人，让我们仔细看看文件名，看看我们是否能找到任何线索。

1.  以`net`开头，所以大概是网络方面的东西。
2.  以`rc`结尾，通常代表“运行时配置”。
3.  路径以波浪号开始，这是 UNIX 用户对主目录的称呼。所以我们可能没有看到应该在 Windows 上使用的东西。
4.  它以一个点开始，这意味着它应该是“隐藏的”。 <sup id="fnref1">[1](#fn1)</sup>
5.  它是主目录中的配置文件，但不在`~/.config/`中，这意味着它不遵循 [XDG 基本目录规范](https://standards.freedesktop.org/basedir-spec/basedir-spec-latest.html)，因此可能有点旧。

我不确定有多少应用程序依赖于`~/.netrc`文件。我所知道的是，许多命令行 FTP 客户端(我知道这并不完全是顶级技术)使用这个文件。

在我最新的 macOS 机器上，有一个`ftp`二进制文件，我仍然可以使用它:

```
$  ftp
ftp>  open mirrors.kernel.org
Trying 2620:3:c000:b::1994:3:14...
Connected to mirrors.pdx.kernel.org.
220 Welcome to mirrors.kernel.org.
Name (mirrors.kernel.org:dmerej): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files. ftp> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，即使我只需要匿名访问，我也必须提供用户名和密码。

`~/.netrc`文件允许我不必为每个连接输入用户名和密码。

我只需要在`~/.netrc`文件中添加一行 <sup id="fnref2">[2](#fn2)</sup> 就像这样:

```
machine mirrors.kernel.com login dmerej password p4ssw0rd 
```

Enter fullscreen mode Exit fullscreen mode

# 臭虫

那么，这和 Python 有什么关系呢？

嗯，在 Python 标准库中，(简称 stdlib)有一个专门解析`~/.netrc`格式的模块。解析本身并不简单。(见 GNU 文档中血淋淋的细节)

下面是使用方法:

```
import netrc

nrc = netrc.netrc()
login, account, password = nrc.authenticators("example.com") 
```

Enter fullscreen mode Exit fullscreen mode

(`account`是一个“附加帐户密码”，但我从未使用过它)

无论如何，几个月前我正在准备新的机器，以便在工作中进行持续集成。

(我们在 Linux、macOS 和 Windows 上做跨平台的 C++代码)

我决定将 Python 用于构建脚本，并且需要在节点上存储一些访问令牌。

由于令牌授予的功能非常有限，所以我不介意在节点上以明文形式保存它们，所以我决定使用`netrc`模块。

但是在 Windows 上，当直接从`cmd.exe`运行脚本时，代码崩溃:

```
Could not find .netrc: $HOME is not set 
```

Enter fullscreen mode Exit fullscreen mode

这并不奇怪:`~/.netrc`是 UNIX 的东西，所以它尝试使用`$HOME`环境变量是有道理的，在 UNIX 上，这个变量几乎总是被设置的。

# 《修罗》

我碰巧知道<sup id="fnref3">T3 3T5 在 Python stdlib 中，有一个函数叫做`os.path.expanduser`非常好用，即使在 Windows 上，甚至在`HOME`没有设置的时候。</sup>

所以我赶紧写了个补丁:

```
 def __init__(self, file=None):
         default_netrc = file is None
         if file is None:
-            try:
-                file = os.path.join(os.environ['HOME'], ".netrc")
-            except KeyError:
-                raise OSError("Could not find .netrc: $HOME is not set") from None +            file = os.path.join(os.path.expanduser("~"), ".netrc")
         self.hosts = {}
         self.macros = {}
         with open(file) as fp: 
```

Enter fullscreen mode Exit fullscreen mode

我打开了我的第一个 Python 的[拉请求](https://github.com/python/cpython/pull/123)

# 试图使拉取请求获得批准

该过程花费了相当长的时间，拉取请求仍然不被接受。

对我来说，一开始很明显这个补丁是一个进步。我重用了现有的代码，并且修复了一个崩溃！

但结果是我不得不:

*   写一些测试
*   修补文档
*   修补发行说明

让我们明确一点，我不是在抱怨这个，也不是在责怪 Python 的维护者。

我一直非常关心测试，我喜欢 Python 文档是如何如此准确，以及更改日志是如何详细和精确，我意识到维护人员的这种请求是维护 stdlib 用户如此喜爱的质量水平所必需的。

但是试图让拉取请求被接受让我有了新的认识:

有时候，执行并不重要。

# 实现不要紧

原来 Python stdlib 的*实现*有些特殊。

那是因为这个代码是如何*使用*的。

人们对它有所期待，主要是因为使用它的代码几乎总是*向后兼容的*。

我写的补丁改变了 stdlib 的行为，原因不明。

如果`~/.netrc`文件是 UNIX 的东西，那么无论如何都必须设置`$HOME`变量才能让其余的工具工作。(【想到 T2】)。

另外，`netrc()`构造函数接受 path 作为参数，所以“修复”可以写成这样:

```
netrc_path = os.path.expanduser("~/.netrc")
nrc = netrc.netcr(file=netrc_path) 
```

Enter fullscreen mode Exit fullscreen mode

最后但同样重要的是，补丁的后果很难描述，甚至会导致破损。

考虑这个代码，假设`~/.netrc`不存在*不存在*并且`$HOME`是*不存在*的集合。

```
 try:
    nrc = netrc.netrc()
except OSError as error:
    # do something with error.args 
```

Enter fullscreen mode Exit fullscreen mode

在旧版本中，`error`将是一个带有一个参数的`OSError`(“$ HOME is not set”消息)，但是在新版本中，代码将改为引发一个`FileNotFound`，它是`OSError`的子类，但是带有两个参数(`errno`和`filename`)

这可能会导致非常讨厌的错误被引入。

没错，这确实是一个极限情况，但是事实上行为的*变化是如此难以描述，这是一个很好的暗示，也许根本不值得。*

# 更进一步

我不知道接下来会发生什么。仍然有人请求进行更改，但是我不再确定拉请求是一个好主意。如果你对此有什么看法，我很乐意听听！

此外，我邀请您观看 PyCon 2017 的[幕后请求](https://www.youtube.com/watch?v=ptbCIvve6-k)演示，这是一个类似的主题。

## 更新(2017-11-25)

事实证明，Python 维护者之一 Berker Peksag 非常关心我的 pull 请求，这个错误将在下一个 Python 版本中修复。这是一个很好的大团圆结局。

*PS:感谢你读到这里:)*

我很想听听你有什么要说的，所以请随时在下面留下评论，或者阅读我的[反馈页面](https://dmerej.info/blog/pages/feedback/)，了解更多与我联系的方式。

* * *

1.  罗布·派克写了一个关于这个话题的精彩故事。 [↩](#fnref1)

2.  是的，密码是明文的，但那是另一个话题。 [↩](#fnref2)

3.  我知道它是因为[我写的另一篇文章](https://dmerej.info/blog/post/why-i-dont-like-ruby/)[↩](#fnref3)