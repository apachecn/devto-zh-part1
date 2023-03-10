# Conda &处理系统中的冲突 Python

> 原文：<https://dev.to/bgalvao/conda--dealing-with-conflicting-pythons-in-your-system-62n>

(免责声明:这里说的是一个 UNIX(Linux 上的 me)系统)

# 问题

我学习了一门使用[机器人操作系统](http://www.ros.org/about-ros/) (ROS)的机器人课程。然而，我总是保持一个`miniconda3`安装来处理我的数据科学冒险和各自的依赖。为了澄清，它安装了`conda`，一个通用的包管理器——虽然实际上我只用它来安装 python 库——和 **python 3.6** 。

发生的事情是 ROS 使用了系统 python——也就是 **python 2.7** 。如果您将“python 路径”默认设置为`miniconda3`的路径，就会出现问题，当您接受`miniconda3`安装程序将下面一行添加到您的`.bashrc`文件时，这个过程就会自动完成。

```
export PATH="/home/user/miniconda3/bin:$PATH" 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，每次你打开一个 shell，你的系统会将 python 解释器设置为`miniconda3`的解释器，也就是 3.6 版本。所以每次我从 ROS 运行一些东西时，它都会崩溃运行在 2.7 版本中编写的 python 脚本，也就是由于两个版本之间的`print`命令的语法差异。

# 解决方案-满足符号链

在阅读了 conda 关于主题的[文档后，我决定测试一下自己实施他们建议的解决方案的能力，即:](https://conda.io/docs/user-guide/troubleshooting.html#programs-fail-due-to-invoking-conda-python-instead-of-system-python)

*   从`PATH`上取下`miniconda3`
*   创建到`miniconda3`的三个组件的符号链接
    *   `conda`
    *   `activate`
    *   `deactivate`
*   将这些符号链接添加到`PATH`

要从`PATH`中删除`miniconda3`，只需在`.bashrc`文件中注释掉或删除前面提到的那一行，例如使用类似`nano`的文本编辑器。

现在已经完成了，让我们创建[符号链接](https://www.howtogeek.com/287014/how-to-create-and-use-symbolic-links-aka-symlinks-on-linux/)。在本文中，符号链接就像是指向其他可执行文件的指针，并将作为命令使用。我为此专门创建了一个文件夹(`.symlinks`)。

```
mkdir .symlinks
cd .symlinks 
```

Enter fullscreen mode Exit fullscreen mode

现在我们在`.symlinks`文件夹中，让我们实际创建符号链接。

```
ln -s /home/user/miniconda3/bin/conda conda
ln -s /home/user/miniconda3/bin/activate activate
ln -s /home/user/miniconda3/bin/deactivate deactivate 
```

Enter fullscreen mode Exit fullscreen mode

要验证它们是否被创建，运行`.symlinks`文件夹中的`ls -l`(列表文件)，您将能够看到指针。

```
lrwxrwxrwx 1 user user 34 Nov  9 14:08 activate -> /home/user/miniconda3/bin/activate
lrwxrwxrwx 1 user user 31 Nov  9 14:10 conda -> /home/user/miniconda3/bin/conda
lrwxrwxrwx 1 user user 36 Nov  9 14:08 deactivate -> /home/user/miniconda3/bin/deactivate 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将`.symlinks`文件夹添加到路径中。但是我们希望在每次打开新的 shell 时，这些符号链接命令自动可用。答案是将它们“添加”到每次打开 shell 时运行的脚本中:文件`.bashrc`。因此，将这一行添加到上述文件中。

```
export PATH="/home/user/.symlinks:$PATH" 
```

Enter fullscreen mode Exit fullscreen mode

为了使更改生效，您可以关闭并重新打开终端，但是要像专业人员一样不做那个动作，运行`source .bashrc`。

现在，如果你运行`python --version`，你将得到系统 python。

```
user@ryzen:~$ python --version
Python 2.7.12 
```

Enter fullscreen mode Exit fullscreen mode

如果你想从根 [conda 环境](https://conda.io/docs/user-guide/tasks/manage-environments.html)使用 python 3.6，做

```
user@ryzen:~$ source activate root
(root) user@ryzen:~$ python --version
Python 3.6.3 :: Anaconda, Inc. 
```

Enter fullscreen mode Exit fullscreen mode

从`python --version`位可以看出，我们现在使用的是 3.6 解释器。如果我想激活我安装了 **d** ata **s** 科学库 <sup>[1](#myfootnote1)</sup> 的环境，我会运行`source activate ds`。

现在新的解释器被激活了，您可以运行为该解释器编写的脚本。不管怎样，如果你需要在当前 shell 上返回到系统解释器(2.7)，那么运行

```
(root) user@ryzen:~$ source deactivate
user@ryzen:~$ python --version
Python 2.7.12 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们回到了 2.7。所以你可以毫无问题地运行在这个版本上的软件，并且随时切换回你的 3.6 版本。ðŸ˜

祝你过得愉快。ðŸ˜

 : *我认为* `conda` *不再允许安装包到 root，并强迫用户创建他们自己的环境，以激励良好的实践。不是我查的，只是个人经验。*