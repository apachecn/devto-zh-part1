# 使用 pcal 创建横向格式的月历

> 原文：<https://dev.to/wincentbalin/using-pcal-to-create-monthly-calendars-in-landscape-format-2ccg>

你想要一本月历，用 DIN A4 横向格式打印，就像[这本](https://wincent.balin.at/files/june.pdf)？

如果你愿意，有一个实用程序可以创建这样的日历，叫做`pcal`。如果您的首选操作系统是 Windows 的化身，您可以从 http://pcal.sourceforge.net/的  下载可执行文件，并使用下面提供的选项在命令行上运行它们。如果您选择的操作系统是 Linux，大多数(如果不是全部)主要发行版都会提供相应的包(在 Ubuntu 中，只需运行`sudo apt-get install pcal`)。

要创建上面的日历，我只需运行这些命令:

```
pcal -o june.ps -P a4 -M -F 1 -E
ps2pdf june.ps 
```

Enter fullscreen mode Exit fullscreen mode

第一个命令运行`pcal`以产生上述 Postscript 格式的日历，它使用了许多选项，下面将对此进行解释:

*   `-o june.ps`该选项让程序将日历写入文件`june.ps`。
*   `-P a4`指定 DIN A4 的纸张尺寸。
*   将月相的象形图添加到日历中。
*   `-F 1`将星期一设置为一周的第一天；通常用于欧洲大陆。
*   进一步欧化日历，设定日期惯例为欧洲的`day/month`(而不是美国的`month/day`)。

照原样运行，上面的命令产生当前月份的日历。要生成指定月份的日历，请在`pcal`命令行中添加 *`month year`* (例如，2013 年 7 月的`07 2013`)。

只有在将生成的日历复制到可能没有 Postscript 兼容打印机的系统时，才需要第二个命令。此命令将日历从 Postscript 转换为可移植文档格式，更广为人知的是其缩写 PDF。程序`ps2pdf`是 ghostscript 包的一部分，处理 Postscript 文件。

当然,`pcal`有更多的选项，但是如果您对本文开头的日历满意，就使用上面的命令。