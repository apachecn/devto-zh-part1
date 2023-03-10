# 理解标准输入和输出

> 原文：<https://dev.to/pmihaylov/understanding-standard-input-and-output-5f1b>

*这篇文章是《基础知识中你学不到的基础知识》系列文章的一部分，旨在帮助渴望深入理解编程和计算机科学的人们。*

在编程基础课程的第一堂课中，我们看到了如何在终端上获取输入和显示输出。这被称为标准输入/输出，或简称为标准 IO。

所以，在 C#中我们有控制台。在 C++中，我们有 cin 和 cout。

所有这些都与标准 IO 的主题有关。他们告诉我们，标准输入是键盘，标准输出是屏幕。在很大程度上，事实就是如此。

但是我们不知道的是标准 IO 是可以改变的。有一种方法可以从一个文件接受输入，并将输出重定向到另一个文件。不，我说的不是写代码读/写文件。我说的是通过终端使用标准 IO 来完成工作。

这有什么用呢？

还记得那些你不得不做一个控制台应用程序，然后用几个例子测试它的时候吗？正常的工作流程是编写一些代码，然后用键盘手动输入示例。然后将输出与给定的预期输出进行可视化比较。更优化的方法可能是将输入写入文件一次，然后一直复制粘贴。

当然，那会节省你的时间。但是有一个更通用的自动化方法。我们去看看。

# 程序如何知道自己的输入输出？

我们将从一点操作系统理论开始。

每当你有一个打开的文件时，操作系统会在某个地方存储一个数字，作为你可以用来读取该文件的索引。在基于 Unix 的操作系统中(所有 Linux 发行版都是这个数字)，这叫做文件描述符。在 Windows 中，它被称为句柄，但基本上是一回事。在本文中，我们将参考 Unix 术语——文件描述符。

所以当你在程序中打开一个文件时，操作系统会给你一个文件描述符来访问它。

但是每个进程都有 3 个文件描述符。即文件描述符 0 -标准输入，1 -标准输出，2 -标准错误。这些引用了调用控制台时使用的另一个文件或进程。WriteLine 或 Console.ReadLine。

让我们在我正在使用的 Linux 操作系统- Ubuntu 中做一个快速演示。

我编译了一个简单的程序，名为 parrot，它接收输入并一直打印相同的输出，直到它接收到行“end”:

[![](img/48f4f10c6d64b17f108b10c3d76c1bff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--faHp86u8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/07/parrot-program.png)

因此，我将让它运行，同时打开另一个终端。在里面，我会列出一些进程，找到我的程序。

[![](img/5b6ffb4c6d0b41abd54e06c0195d9dc8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jtcqjgXa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/07/parrot-process.png)

该输出的 TTY 列显示了运行该进程的终端。看到它如何为 parrot 进程显示 **/dev/pts/19** 了吗？记住这个名字。另外，检查进程 id (PID)。

现在，我将进入这个神奇的 Linux 目录- **/proc** 。它在文件中包含了所有关于当前正在运行的进程的数据(Linux 中的一切都是一个文件，包括进程，但那是另一个话题)。如果我打开目录 **23764** (进程 id！)，我会看到我鹦鹉进程的数据。我打开目录 **fd** ，它保存了关于该进程的所有文件描述符的信息，并向您展示了它的内容:

[![](img/50e76ae5dc473c36693f288110f84b0f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_uaAxgIE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/07/parrot-proc-dir.png)

正如你所看到的，我们有 3 个文件描述符，它们指向运行程序的终端 **/dev/pts/19** 。

因此，当您运行 parrot 程序时，您会看到一些字符串在终端窗口中打印出来。但是从操作系统的角度来看，有一个程序叫做 parrot，它把它的标准 IO 发送到设备 **/dev/pts/19** 上，这个设备恰好是我们打开的终端！

# 操纵木卫一

好了，现在你知道当你写**控制台时实际发生了什么。ReadLine** ，是时候告诉你如何改变这种行为了。如何改变这些文件描述符，使其不使用 **/dev/pts/XX** 进程，而是使用其他的进程呢？

原来，有一些简单的例程可以在终端中使用，用它们可以很容易地改变标准 IO。一种是使用 **' > '** 命令。因此，我在 parrot 程序的文件夹中，这次我将使用以下命令运行它:

**。/parrot > output.txt**

这次，当我写消息时，我在屏幕上看不到任何输出。你看到的只是我输入的内容。

[![](img/b2f3e98dc93c6775610f9d5bed5b893c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yFW6fJYQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/07/parrot-stdout-1.png)

但是如果我打开文件 **output.txt** ，你可以看到我期望的所有输出都写在里面了！

[![](img/2676592bc068c60016fb00b9eafe2e19.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ApuJZ8Ga--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/07/parrot-output.png)

现在，执行我在上一节的演示中描述的所有例程，我转到 proc 目录并检查我的进程的文件描述符。原来文件描述符 1 现在指向了我刚才提到的文件！

[![](img/ae636f9325940f33674630d353d17ac5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_HJLR-Rk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/07/parrot-stdout-redirected-1024x102.png)

这就是如何覆盖默认的标准输出，这次是控制台。ReadLine 不是写入屏幕，而是写入文件。但是标准输入呢？

类似地，有一个 **' < '** 命令接受来自文件的标准输入。

我创建了一个名为 **input.txt** 的文件，并添加了一些内容。接下来我执行:

**。/parrot < input.txt**

[![](img/bf80147c068af5cf3b8ce5cb0beb6623.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yu0vs1NC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/07/parrot-input-gedit.png)

不过，在继续之前，我修改了 parrot 程序，使其在读取完输入后不退出，因为我想检查它的进程数据。浏览第一个演示的所有内容，我观察到以下文件描述符:

[![](img/fdc6f918bbadc0cdee9b9f2e50b6c599.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kS84T-zp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/07/parrot-stdin-redirected-1024x100.png)

正如您可能猜到的，您可以通过如下方式覆盖标准输入和输出:

**。/鹦鹉< input.txt > output.txt**

哦，顺便说一下，我提到还有一个东西叫做标准误差？这是程序输出的任何错误信息的输出。例如，C#中异常的输出进入标准错误流。你可以使用**‘2>’**命令来重定向它。

# 流程呢？

到目前为止，我已经向您展示了如何将标准 IO 重定向到文件。但是一个非常有趣的尝试是使用相似的技术实现两个进程之间的通信。这可以使用管道来完成，管道在终端中由 **'|'** 符号表示。

管道的作用是将流程 A 的标准输出重定向到流程 b 的标准输入。用一种更非正式的语言来说，一个流程的输出作为另一个流程的输入。

[![](img/b8f77a21c3f87c925ce41834a52bd1df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_YvMrgwl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/07/pipe-explanation.png)

比方说，我想要从某个目录中按字母顺序排序的前 3 个文件的列表。Linux 中有一些程序可以独立完成这些任务。

**ls -1** :按行列出给定目录下的文件
**排序**——按字母顺序对所有输入行排序
**尾**——n3:从所有输入行中取出前三个条目

使用管道，您可以将这些组合在一起，得到:
**ls -1 | sort | head -n 3**

结果是:
**【ls-1】**按行输出所有文件。**【sort】**按字母顺序排序，**【head-n 3】**只取前三行。

[![](img/6388fdf56eef8e9b7318567dff6545f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MlVco-Xp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/07/pipe-example-1024x101.png)

这就是使用终端的美妙之处。当使用像 Excel 这样的 GUI 程序时，你希望它们能够做所有的事情——编辑、搜索、排序、过滤等等。但是当你使用终端时，你希望程序做一件事。正是不同小程序的组合赋予了你巨大的力量。

# 一个简单的测试平台

现在我们知道了这些功能，我们可以创建一个有用的系统来帮助我们进行开发。还记得我在编写控制台应用程序时提到的所有手工测试吗？

我们可以将输入写在某个测试文件中，并将其作为程序的标准输入，而不是一直手动编写输入。接下来，我们可以将标准输出重定向到一个文件，并将其与具有预期输出的文件进行比较。因此，我们将使用一个简单的 shell 脚本来完成这项工作，您只需运行该脚本(通过键入 bash test.sh ),它就会输出所有测试是通过还是失败:

[![](img/f95d66043e14ae08bdfb63879ad93466.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tKiFC8uS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pmihaylov.com/wp-content/uploads/2017/07/parrot-test-1-1024x256.png)

如果你以前没有遇到过 shell 脚本，不要被吓倒。它的目的是让你在实际的终端上自动输入所有的命令。因此，您可以做的是运行脚本，它会为您完成所有工作。也许我稍后会写一篇关于创建 shell 脚本的文章。

我在脚本中做的是遍历测试文件列表，并将它们用作所提供程序的标准输入。然后，我将标准输出输出到另一个文件，并与预期的文件进行比较。有关测试平台本身的详细信息，请查看此处:

[测试 IO](https://github.com/PreslavMihaylov/tests-io)

注意，这将适用于 Linux，因为它是一个 bash 脚本，但是您可以创建等效的 Windows 命令和脚本。他们在那里被称为蝙蝠文件。也许你也可以尝试在 Windows 上实现这样一个平台。如果没有，您可以安装 [MinGW](http://www.mingw.org/) ，它可以让您在 Windows 机器上模拟 Linux 命令/脚本。

# 结论

因此，我们已经走过了从键盘和屏幕读取 IO 到将它重定向到文件和其他进程的漫长道路。这就是标准输入/输出的威力。我们用它创建了一个简单而智能的测试平台，用于根据一些输入和预期输出检查您的程序。

现在试试这些东西。编译您的源文件，打开输出“exe”或您可能有的任何东西的位置，并尝试用我向您展示的一些命令来改变它的标准 IO。顺便说一下，它们也适用于 Windows。

一旦你掌握了这个概念，作为一名开发人员，你就离更高效、更有知识更近了一步。