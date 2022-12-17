# Linux 电源继续

> 原文：<https://dev.to/tcratius/linux-power-continued-51b>

在所有的 Linux 发行版中，你会遇到的第一件事就是这种检查你正在下载的 Linux 文件是否是你想要下载的“合法的”ISO 文件的方法。

今天我将教你使用 Windows 电脑检查 sha256sum 的基础知识；尤其是 Windows 7。要下载的操作系统是 Linux Mint 18.2“Sonya Cinnamon 发布！

为什么是这种分布？第一:它非常受欢迎，因此在世界各地的不同计算机系统上使用，所以如果你有麻烦，你应该能够找到你的问题的答案。第二:我尝试了 Antergos，但是它出现了“建立数据库连接错误”,我下载的种子文件与他们的文件检查系统 MD5 sum 不匹配。作为一个好人，我发布了这个 bug，以后还会回来查看。:)

如果你想试试，这里有 Linux 肉桂。

[http://blog.linuxmint.com/?p=3289](http://blog.linuxmint.com/?p=3289)

好了，现在，你的 ISO 文件将会下载到你的“下载”文件夹中。让这个过程更容易。按开始，然后键入“powershell”。如果你和我一样，我把它钉在我的任务栏上以便于访问。

根据您的系统，您会得到类似这样的结果。

C:\Users\YourAccountNamehere

键入 cd \,这将带您回到 C:\

然后，您需要为将要下载的文件创建一个文件夹

mkdir linux\

如果您希望检查是否创建了该文件夹，可以通过键入“dir”然后“enter”来完成。

现在，您需要从这里下载 sha256sum.txt 来验证您的下载。

[https://linuxmint.com/verify.php](https://linuxmint.com/verify.php)

所以现在我们想从这里得到 sha256sum.exe 的文件；

[http://www . labtest project . com/using _ windows/step _ by _ step _ using _ sha 256 sum _ on _ windows _ XP . html](http://www.labtestproject.com/using_windows/step_by_step_using_sha256sum_on_windows_xp.html)

好了，你现在可以拿走所有这些文件了:

*   sha256sum.exe sha 256 sum . txt
*   Linux 作为一个大

并将它们放入 C:\linux 文件夹中

再次打开“powershell”并键入 cd \ enter

键入 sha 256 sum Linux mint . iso > Linux . txt

* *请注意，您必须将上面的 linuxmint.iso 替换为您下载的 iso 的完整文件名

现在打开 sha256sum.txt 和 linux.txt 文件，比较两个签名。您所做的是测试 linuxmint.iso 并将签名发送到 linux.txt 文件。如果这与 sha256sum.txt 中的匹配，那么你就有了一个好的 ISO，可以刻录到 CD 或 USB 上