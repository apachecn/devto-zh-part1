# 解释了 shell 重定向

> 原文：<https://dev.to/oyagci/shell-redirections-explained-2h9>

作为一名学生，我不得不每天使用我的 shell 来完成我的工作。如果您知道如何使用，shell 在日常生活中会是一个非常强大的工具。

为了顺利完成许多任务，您必须使用 Shell 重定向。

# 一个程序如何在终端上输出其内容。

为了在终端上输出文本，程序必须将其输出写入文件。在类 UNIX 操作系统上操作文件的经典方法是向操作系统请求文件描述符(fd)。fd 是由操作系统给出的无符号整数，用于标识系统上的给定文件。

文件描述符是代表文件访问方式的数字。

默认情况下，你的程序有 3 个文件描述符，分别叫做`stdin`、`stdout`和`stderr`。

第一个 fd `stdin`是程序用来读取输入的文件。它的默认值是`0`。如果你启动一个像`cat`这样没有参数的程序，它会从`stdin`读取输入。

第二个 fd `stdout`用于为用户输出内容(比如“Hello World！”).它的默认值是`1`。

默认打开的第三个 fd 是`stderr`。除了我们主要用它来输出错误消息之外，它的工作方式和`stdout`完全一样。它的默认值是`2`。

# 外壳重定向操作符

在 shell 中，有一些操作符可以执行重定向。那些是`>`、`>>`、`<`、`<<`、`>&`、`<&`。

## `>``>>``<`

这 3 个操作符有一个主要的共同点:它们与输入/输出文件交互。

`>`和`>>`用于将程序的输出重定向到文件中。

```
$> cat my_file.txt > my_new_file.txt # The output from cat is redirect to a file caled my_new_file.txt 
```

Enter fullscreen mode Exit fullscreen mode

(如果文件不存在，则创建该文件)

`>`和`>>`的区别在于`>`截断它输出到的文件，而`>>`追加到输出文件。

```
$> echo Hello World! > greeting.txt # Creates a file with 'Hello World!' as the content

$> echo How are you? >> greeting.txt # Append 'How are you?' to greeting.txt 
```

Enter fullscreen mode Exit fullscreen mode

`<`允许您使用文件作为输入。于是`stdin`变成了文件。

```
$> base64 < greeting.txt # base64 will use stdin as input which is redirected to greeting.txt by the shell 
```

Enter fullscreen mode Exit fullscreen mode

您也可以组合多个重定向运算符:

```
$> cat < a.txt > b.txt # Reads from a.txt and outputs to b.txt 
```

Enter fullscreen mode Exit fullscreen mode

## 和`<<`

那个有点像上面的三个，除了它不与文件交互。相反，它用于从用户的键盘逐行获取输入。

所以当你做类似于:
的事情时

```
$> cat <<EOF 
```

Enter fullscreen mode Exit fullscreen mode

...并按 enter 键，shell 将期待输入。
所以你可以继续:

```
$> cat <<EOF
> Hello World!
> How are you?
> Do you know the answer to the ultimate question? 
```

Enter fullscreen mode Exit fullscreen mode

当你想停止输入时，你可以按下`Ctrl+D`或者输入在`<<`操作符右边的单词(在这里是`EOF`)然后按下回车键(必须在空行):

```
$> cat << EOF
> Hello World!
> How are you?
> Do you know the answer to the ultimate question?
> EOF
Hello World!
How are you?
Do you know the answer to the ultimate question? 
```

Enter fullscreen mode Exit fullscreen mode

输入停止后，shell 会立即运行您在`stdin`中输入内容的程序。

## `>&`和`<&`

这两者不是作用于文件，而是作用于文件描述符。

它们的左边可以被忽略或者是一个数字(目标 fd)。
左侧必须附在操作者身上。

如果我想把`stdout`重定向到`stderr`，我可以做:

```
$> cat ft_strdup.c 1>&2 
```

Enter fullscreen mode Exit fullscreen mode

或者...

```
$> cat ft_strdup.c >&2 
```

Enter fullscreen mode Exit fullscreen mode

你也可以使用`>&`和`<&`来关闭一个文件描述符(例如，如果你想忽略错误信息)

```
$> find / -name 'lost_file' 2>&- # If you don't want to see messages like 'Permision denied' 
```

Enter fullscreen mode Exit fullscreen mode

`>&`和`<&`之间的唯一区别是在没有给定左 fd 的情况下使用的默认文件描述符。
`>&`代表`stdout``<&`代表`stdin`。

此外，如果您在操作符左侧和操作符本身之间放置一个空格，shell 将认为左侧实际上是一个要传递给程序的参数，并将使用默认的左侧 fd 号。

## 最后一件事

这些操作符可以在命令行的任何地方使用

```
$> < input_file cat

$> >output_file echo hello world

$> ls 2>&- -R / 
```

Enter fullscreen mode Exit fullscreen mode

但是您应该将它们放在命令的开头或结尾，以避免混淆。

希望这篇文章对你有所帮助。:)