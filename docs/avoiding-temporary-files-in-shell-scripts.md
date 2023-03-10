# 避免 Shell 脚本中的临时文件

> 原文：<https://dev.to/philgibbs/avoiding-temporary-files-in-shell-scripts>

这是摘自 [Openmake 软件](https://www.openmakesoftware.com/production-quality-shell-script/%20)的一篇较长的文章。如果您想了解与 shell 脚本中的性能和安全性有关的其他技巧和诀窍，请访问该网站！

## 简介

以争论开始总是很有趣的，所以让我把我的立场说清楚:临时文件是 shell 脚本世界中的一股邪恶力量，应该尽可能避免。如果你想为这一立场辩护，只需考虑以下几点:

*   如果脚本以 root 用户身份运行(例如，从部署过程中),那么使用临时文件会带来很大的安全风险。
*   每当脚本退出时，都需要删除临时文件。这意味着你必须捕获用户出口，比如 CTRL-C，但是如果脚本被 SIGKILL 杀死了呢？
*   临时文件会填满文件系统。更糟糕的是，如果它们是通过重定向应用程序的标准输出而创建的，那么应用程序可能不会意识到由于文件系统已满而导致的输出失败。

让我们详细看看这些问题:

## 问题 1:如果 shell 脚本意外退出，临时文件会怎样？

如果 shell 脚本是交互式的，并且用户使用 CTRL-C 暂停它，就会发生这个问题。如果没有被捕获，那么脚本将会退出，临时文件将会被搁置。

唯一的解决方案是编写一个“trap”函数来捕获用户生成的信号，比如 CTRL-C，并删除临时文件。这里有一个例子:

```
function Tidyup
{
    rm –f $tempfile
    exit 1
}

trap Tidyup 1 2 3 15

```

Enter fullscreen mode Exit fullscreen mode

但是，如果脚本由于任何其他原因而退出，则永远不会调用整理脚本。脚本中的 kill-9 将彻底杀死它，并保留临时文件。

## 问题 2:如果文件系统写满了会怎么样？

好的，所以当文件系统达到 100%满时，你正忙于写你的临时文件。现在发生了什么？你的剧本会失败，不是吗？也许吧，但也许不是你想的那样。一些第三方应用程序在写入标准输出时不检查错误返回。老实说，如果你写了 C 代码，你会检查从`printf`(或`write`或其他什么)返回的代码，看看它是否写对了吗？可能不会——在屏幕上写东西会出错吗？大概不是很多，但是如果你重定向了 standard out，那么输出就不会出现在屏幕上——而是出现在一个文件中。你会惊讶有多少商业应用程序成为这种情况的牺牲品。

最终结果是，像这样的命令

```
third_party_command > /tmp/tempfile

```

Enter fullscreen mode Exit fullscreen mode

即使`/tmp`已满，也可能*而非*返回失败条件。你无法知道命令失败了，但是`/tmp/tempfile`没有包含来自`third_party_command`的完整输出。接下来会发生什么取决于你的脚本做什么，但它很可能是次优的。我们稍后将讨论一些解决方法。

## 问题三:谨防重定向攻击。

大多数临时文件是在`/tmp`中创建的，文件名中包含一个$$序列。shell 用当前进程 id 替换$$序列，从而创建一个惟一的文件名。

但是，如果脚本以 root 用户身份运行，并且您像这样隐式创建文件:

```
echo "top secret" > /tmp/mytempfile$$

```

Enter fullscreen mode Exit fullscreen mode

那么“绝密”很可能不会保密很久。一个名为 Dave 的无耻用户可以创建这样一个脚本:

```
mknod /tmp/mytempfile12345 p
cat /tmp/mytempfile12345 > /home/dave/secretdata

```

Enter fullscreen mode Exit fullscreen mode

这将一直阻塞，直到你的根脚本最终以 PID 12345 执行——当它把“绝密”写到它认为*是它将要创建的文件中。然而，该文件是一个管道，Dave 肆无忌惮的脚本只是抓取内容并将其写入 Dave 目录中名为`secretdata`的文件。当然，这可能会破坏根脚本(当它试图读取`mytempfile$$`时会挂起)，但是到了这个阶段，Dave 已经带走了秘密数据。*

## 避开临时文件

避免临时文件可能很困难，但不一定不可能。许多 UNIX 命令将读取标准输入(或将输出发送到标准输出)以及命名文件。使用管道将这样的命令连接在一起通常会得到想要的结果，而不需要求助于临时文件。

如果您有两个独立的命令，并且想要合并和处理它们的输出，该怎么办呢？让我们假设我们将为程序`process_file`构建某种形式的控制文件。控制文件由一些标题行、控制文件的实际主体(我们的脚本将生成)和一些结尾行组成，以完成整个工作。

构建这种文件的一种常见方式是:

```
echo “some header info” >Â  /tmp/tempfile.$$
process_bodyÂ  Â  Â  Â  Â  Â  >> /tmp/tempfile.$$
echo “some tailer info” >> /tmp/tempfile.$$
process_file /tmp/tempfile.$$
rm –f /tmp/tempfile.$$

```

Enter fullscreen mode Exit fullscreen mode

然而，这种代码容易受到上述所有问题的影响。

如果我们将代码重写为:

```
{
echo "some header info"
process_body
echo "some tailer info"
} | process_file

```

Enter fullscreen mode Exit fullscreen mode

然后，这将所有相关命令放入一个列表中，并执行列表标准输出到`process_file`程序的单次重定向。这避免了从所需输入文件的各种组件构建临时文件的需要。

如果`process_file`是一个不能从标准输入中获取输入的应用程序，该怎么办？那么我们肯定得使用临时文件了吧？

嗯，你仍然可以避免使用临时文件，但是这需要更多的努力。下面是代码的样子。我们将逐行检查它。

```
mknod /tmp/mypipe.$$ p # 1
if [ $? –ne 0 ]
then
    echo “Failed to create pipe” >&2
    exit 1
fi
chmod 600 /tmp/mypipe.$$ # 2
process_file /tmp/mypipe.$$ & # 3
(
   echo "some header info"
   process_body
   echo "some tailer info"
) > /tmp/mypipe.$$ # 4
wait $! # 5
rm –f /tmp/mypipe.$$ # 6

```

Enter fullscreen mode Exit fullscreen mode

首先，我们创建一个命名管道(#1)。命名管道与任何其他管道完全相同，只是我们可以显式地创建它，并且它出现在文件系统中。(换句话说你可以用`ls`看到它)。严格来说，这是一个临时文件。但是，它的长度为零，因此不会填满文件系统。此外，如果由于任何原因(包括文件已经存在)而无法创建，则会返回一个错误。因此重定向攻击是无用的。当然，它是由一个不可追踪的杀手留下的，但我们不可能拥有一切。

我们更改了访问模式(#2)，以便只有运行该脚本的用户可以读取或写入它。另一种方法是在调用`mknod`之前，通过调用`umask 066`首先创建具有正确权限的管道。

我们设置我们的`process_file`程序在后台运行，从这个命名管道(#3)中读取它的输入。现在，由于管道上没有任何东西，程序的 read 调用将被阻塞。因此`process_file`将挂起等待输入。然而，我们已经设置了`process_file`运行背景(用`&`操作符),所以脚本将继续运行。

我们像以前一样为`process_file`构造控制文件，只是这次我们将它重定向到我们的命名管道(#4)。此时，`process_file`将解除阻止并开始读取数据，就像它来自传统文件一样。

等待调用(#5)将阻塞，直到指定的子进程退出。`$!`是一个 shell 序列，表示最后一个后台进程的进程 ID。由于这是`process_file`的 PID，我们的脚本将一直等到`process_file`完成，就像它在前台被调用一样。

最后，我们删除命名管道(#6)。

## 我别无选择，只能创建一个临时文件。我能怎么做呢？

如果您别无选择，只能使用临时文件，请使用以下技巧:

*   使用`umask`在脚本顶部设置文件权限屏蔽。`umask 077`将从新创建的文件中删除组和全局读取权限。这样，即使您的脚本在没有清理的情况下中止，也只有调用的用户可以读取留下的文件内容。
*   不要使用单个重定向操作符隐式创建文件。相反，首先确保该文件名不存在(使用`-f`测试操作)，然后适当地设置您的`umask`,以便只有该文件的所有者可以访问它。更好的是，设置一个函数来生成一个唯一的文件名，创建它并设置访问权限。
*   如果您要创建多个临时文件，请创建一个临时目录并将文件放在那里。这样，您的`trap`函数可以在退出时简单地删除目录和其中的所有文件——您不需要跟踪您创建了什么临时文件，因为它们都在同一个临时目录中。
*   对临时文件执行健全性检查，以确保它已被成功写入。请记住，检查`$?`可能是不够的，因为应用程序可能不会检查从写入标准输出返回的错误。请尝试下面的技术:

    ```
    process_file | tee $output_file > /dev/null
    if [ $? != 0 ]
    then
    …
    fi

    ```

    因为这将使`tee`负责写入文件系统，并且它*应该*正确地标记错误。注意`$?`操作员将检查`tee`的退出状态，而不是`process_file`。如果你想知道`process_file`是否正常工作，使用一个列表将`process_file`的执行与退出检查组合起来(见下文)
*   创建整理功能，以便在用户中止脚本时删除临时文件。在脚本的受控退出(正常或错误)时调用相同的函数。

将所有这些放在一起，给我们一个这样的脚本:

```
#!/bin/ksh
function ExitWithError
{
        echo "$*">&2
        rm -f $output_file
        exit 1
}

function Tidyup
{
        ExitWithError "Abort"
}

umask 077
output_file=/tmp/tfile$$
rm -f $output_file
trap Tidyup 1 2 3 15

{
        process_file
        [[ $? != 0 ]] && ExitWithError "process_file failed"
} | tee $output_file > /dev/null
[[ $? != 0 ]] && ExitWithError "Error writing to $output_file"
# process output_file here
# .
# .
# normal exit
rm -f $output_file
exit 0

```

Enter fullscreen mode Exit fullscreen mode

该脚本确保它创建的临时文件在退出时(正常成功退出或出错时)被删除，确保临时文件在被读取进行处理之前被正确写入，并正确设置权限掩码，以便如果用 SIGKILL (-9)终止该脚本，该临时文件只能由调用该脚本的用户读取。

## 结论

临时文件可能会产生严重的安全问题，并导致脚本出现意外故障。大多数 shells 使得避免它们变得相对容易——您应该这样做！如果你真的别无选择，那么确保权限设置正确，检查它们是否被正确写入，并在成功退出和出错时删除它们。