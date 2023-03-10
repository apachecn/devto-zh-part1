# 今天我最喜欢的 shell 命令——在几秒钟内预先设置文本行

> 原文：<https://dev.to/pauljacobson/my-favourite-shell-command-for-today--prepend-lines-of-text-in-seconds-4dja>

本周，我正在为一个客户做一个潜在客户生成项目，有趣的是，这个项目引导我使用一个简单、省时的 shell 命令，这个命令使我能够在文本行前加上一个简单的引号。它比听起来更令人印象深刻，因为它比我编写和运行它节省了更多的时间。

我用来寻找潜在客户的方法之一是运行布尔搜索，结合多个搜索参数。搜索词很长，看起来有点像这样:`"Managing director" OR "sales director" OR "sales manager" OR "procurement manager" OR ...`

我的起点通常是一个搜索参数列表，我需要将它转换成一个布尔搜索查询。例如:

```
Managing director

sales director

sales manager

procurement manager 
```

只是名单要长得多。我首先将我的列表添加到 [VS 代码](https://code.visualstudio.com/)中，因为以纯文本方式处理这类事情极大地简化了这个过程。我喜欢 VS 代码的一点是，你可以在你选择的文本的每一行的末尾添加一个光标。这可能来自崇高的文字，这是一个可怕的功能。

这很方便的原因是因为我想把每一个搜索词用引号括起来，这样我就可以用布尔运算符把它们串在一起，比如`AND`、`OR`和`NOT`。

我发现自己面临的挑战是，如何将光标移动到每个 like 的*开头*处，以添加开始引号？因为每一行都有不同的长度，所以我不能将光标从每一行的末尾移到开头。我也[不想重写让我在每行末尾添加光标的特性](https://marketplace.visualstudio.com/items?itemName=kaiwood.insert-cursor-at-beginning-of-each-line-selected)。

我认为一定有某种 shell 命令或脚本可以运行，来解析文本文件并在每一行的开头添加一个左引号。原来[还有](https://stackoverflow.com/questions/2099471/add-a-prefix-string-to-beginning-of-each-line)(当然)。像许多 shell 命令一样，它看似简单，并且完全按照机器上所说的去做(如果有一个机器的话)。

我看了三个选项(如果你想要不同的东西，可能更多):

```
sed -e 's/^/prefix/' file

# If you want to edit the file in-place
sed -i -e 's/^/prefix/' file

# If you want to create a new file
sed -e 's/^/prefix/' file > file.new 
```

我选择了选项 3，只是用我的左引号替换了单词`prefix`,然后运行命令。我将命令输出到一个新文件中，因为我不想破坏原来的列表。它立即运行，当我打开新文件时，每行的开头都有引号。

从那时起，在每一行的末尾添加光标来为我的搜索添加布尔操作符就变得相对简单了。

当我们几乎所有的东西都有图形用户界面时，使用 shell 命令可能看起来很奇怪，但是我对 shell 脚本了解得越多，它就越让我吃惊。这一行代码看起来无关痛痒，但它在几秒钟内为我做了一些本来要花很长时间才能完成的事情。