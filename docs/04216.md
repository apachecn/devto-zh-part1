# 用 Sparrow 工具包测试 Bash 脚本

> 原文：<https://dev.to/melezhik/bash-scripts-testing-with-sparrow-toolkit-b84>

Sparrow 是一个脚本系统，旨在跨 Linux 环境快速开发和分发脚本。

在这里，我将展示如何使用 nifty Sparrow 框架毫不费力地为 Bash/Shell 脚本编写测试。

# 安装麻雀

Sparrow 是用 Perl 编写的，因此作为 CPAN 模块安装:

```
$ cpanm Sparrow 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，你都设置好了。

# 给出一个 Bash/Shell 脚本

假设你有一个名为`script.bash`的脚本来做一些有用的事情。

```
$ cat /path/to/my/script.bash 
```

Enter fullscreen mode Exit fullscreen mode

```
echo Hello World!
exit 0 
```

Enter fullscreen mode Exit fullscreen mode

```
$ bash /path/to/my/script.bash 
```

Enter fullscreen mode Exit fullscreen mode

```
Hello World! 
```

Enter fullscreen mode Exit fullscreen mode

按照麻雀测试的方法(*黑盒测试*？)我们应该关心的是脚本以 0 退出代码退出并产生一些输出。在这种情况下，我们希望输出包含< Hello World！>串串。

# 创建测试(麻雀)场景

好了，让我们为我们的脚本创建一个测试场景。

该场景将由两个不同的文件组成:

*   场景文件(或故事)——它只是一个运行`script.bash`的包装器

*   检查文件-包含验证`script.bash`输出的检查规则

下面是代码:

```
$ cat story.bash 
```

Enter fullscreen mode Exit fullscreen mode

```
bash /path/to/my/script.bash 
```

Enter fullscreen mode Exit fullscreen mode

```
$ cat story.check 
```

Enter fullscreen mode Exit fullscreen mode

```
Hello World 
```

Enter fullscreen mode Exit fullscreen mode

# 运行测试场景

Sparrow 附带了`strun` -一个执行 Sparrow 场景的工具，只需从我们已经创建了一对文件`story.bash`和`story.check` :
的目录中运行`strun`

```
$ strun 
```

Enter fullscreen mode Exit fullscreen mode

```
2017-10-09 14:59:06 :  [path] /
Hello World!
ok  scenario succeeded
ok  text has 'Hello World!'
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

# 报告测试失败

当 Sparrow 测试场景失败时，这意味着两种情况之一:

*   下划线 Bash 脚本返回了非零退出代码
*   生成的输出未通过检查规则

让我们来看看这两种情况:

一个非零退出代码:

```
$ cat /path/to/my/script.bash 
```

Enter fullscreen mode Exit fullscreen mode

```
echo Hello World!
exit 1 
```

Enter fullscreen mode Exit fullscreen mode

```
$ strun 
```

Enter fullscreen mode Exit fullscreen mode

```
2017-10-09 15:03:34 :  [path] /
Hello World!
not ok  scenario succeeded
STATUS  FAILED (1) 
```

Enter fullscreen mode Exit fullscreen mode

检查规则测试失败:

```
$ cat /path/to/my/script.bash 
```

Enter fullscreen mode Exit fullscreen mode

```
echo How are you?
exit 0 
```

Enter fullscreen mode Exit fullscreen mode

```
$ strun 
```

Enter fullscreen mode Exit fullscreen mode

```
2017-10-09 15:05:40 :  [path] /
How are you?
ok  scenario succeeded
not ok  text has 'Hello World!'
STATUS  FAILED (2) 
```

Enter fullscreen mode Exit fullscreen mode

# 更复杂的测试

可以使用 Sparrow 创建更复杂的场景。很难在这里面面俱到，我将向您简要介绍一些很酷的 Sparrow 特性，然后我们结束如何*发布*您的测试并与团队的其他成员分享。

## 使用正则表达式

假设你想检查一些正则表达式的脚本输出，这很简单:

```
$ cat story.check 
```

Enter fullscreen mode Exit fullscreen mode

```
regex: hello World\W 
```

Enter fullscreen mode Exit fullscreen mode

## 检查线条顺序

如果你的脚本生成了一系列的行，比如:

```
Header
Line1
Line2
Line3
Footer 
```

Enter fullscreen mode Exit fullscreen mode

可以使用所谓的“块检查”来检查这一点:

```
$ cat story.check 
```

Enter fullscreen mode Exit fullscreen mode

```
begin:
Header
Line1
Line2
Line3
Footer
end: 
```

Enter fullscreen mode Exit fullscreen mode

## 检查范围内

如果您需要设置行搜索的上下文，比方说您的脚本生成了一些条目或块，并且您想要检查条目:
中的内容(数字)*该怎么办*

```
<data>
1
2
3
A
B
</data> 
```

Enter fullscreen mode Exit fullscreen mode

检查规则如下:

```
$ cat story.check 
```

Enter fullscreen mode Exit fullscreen mode

```
between: <data> <\/data>
regexp: \d+ 
```

Enter fullscreen mode Exit fullscreen mode

## 测试套件

你也可以运行不止一个测试，并在每个项目或套件的基础上组织一个测试代码:

```
$ cat hook.bash 
```

Enter fullscreen mode Exit fullscreen mode

```
run_story test1
run_story test2 
```

Enter fullscreen mode Exit fullscreen mode

```
.
â”œâ”€â”€ hook.bash
â””â”€â”€ modules
    â”œâ”€â”€ test1
    â”‚Â Â  â”œâ”€â”€ story.bash
    â”‚Â Â  â””â”€â”€ story.check
    â””â”€â”€ test2
        â”œâ”€â”€ story.bash
        â””â”€â”€ story.check

3 directories, 5 files 
```

Enter fullscreen mode Exit fullscreen mode

在[文档](https://github.com/melezhik/outthentic)页面上还有很多其他的例子。我鼓励你读这一篇！

# 发布测试

Sparrow 的一个杀手锏就是一旦你完成了你的工作，你可以发布你的测试并让其他人来运行它们:

## 上传测试

只需用测试套件在根目录下创建一个 Sparrow 元文件，然后给它一个 run `sparrow plg upload`命令

```
$ cat sparrow.json

{

   "name" : "my-test-suite",
   "version" : "0.0.1",
   "description" : "Some tests for my Bash scripts"
} 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，您的测试套件将作为 Sparrow 插件打包并上传到名为 [SparrowHub](https://sparrowhub.org) 的中央 Sparrow 存储库中。预料到可能的问题，我也说:“是的。私下发布 Sparrow 插件是可能的”，但这可能是我下一篇文章的主题。

在 [Sparrow](https://github.com/melezhik/sparrow) 文档页面上有更多关于发布和下载 Sparrow 脚本的信息。

最后，您的团队成员可以下载并运行测试套件，就像下面两个命令一样简单:

```
 $ sparrow plg install my-test-suite
$ sparrow plg run my-test-suite 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

Sparrow 是包含许多电池的 Linux 脚本平台。它使得毫不费力地开发 Bash/Shell 脚本的测试场景成为可能，并鼓励在团队中分发测试脚本。

想法，问题，评论非常感谢。

问候

作者