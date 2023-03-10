# 如何在 MacBook Pro 上安装 Ubuntu

> 原文：<https://dev.to/lobo_tuerto/ubuntu-on-a-macbook-pro-4n5c>

这些指令在 2014 年年中的 MacBook Pro 上进行了测试。

应该——可能——在其他 MacBooks 上也能正常工作。

* * *

如果你在 MacBook 上安装了 Ubuntu，你很快就会发现一组很小但非常恼人的行为:

*   Alt 和 Cmd 键被交换(使用 PC 键盘作为参考)。
*   *功能键*与*多媒体键互换。*
*   无法用笔记本电脑的键盘在 CLI 上轻松*复制&粘贴*。
*   触控板的行为不稳定。
*   你几乎看不到屏幕上的任何东西，或者…
*   也许用户界面太大了，你想根据自己的喜好进行调整。

如果这听起来像是你的问题，那么继续读下去，解决方案就在前面。

_ 目前，唯一没有解决方案的问题是集成网络摄像头。**它不起作用。** _

# 键盘问题

## 交换 Cmd 和 Alt 键

如果你想让你的 Alt 键出现在每台电脑上，除了苹果电脑。立即生效，但只是暂时的:

```
echo 1 | sudo tee /sys/module/hid_apple/parameters/swap_opt_cmd 
```

使其永久化:

```
echo options hid_apple swap_opt_cmd=1 | sudo tee -a /etc/modprobe.d/hid_apple.conf
sudo update-initramfs -u -k all 
```

## 启用功能键

这将使您的功能键默认可用。要使用多媒体键，您现在需要按住 **fn** 键，同时按下多媒体键。

立即生效，但只是暂时的:

```
echo 2 | sudo tee /sys/module/hid_apple/parameters/fnmode 
```

这种变化不是永久性的。要做到这一点:

```
echo options hid_apple fnmode=2 | sudo tee -a /etc/modprobe.d/hid_apple.conf
sudo update-initramfs -u -k all 
```

## 一键粘贴

如果你将使用*简化的*笔记本电脑键盘，总是使用鼠标进行复制&粘贴将会很困难。

使用**右超**键(如果你把上面的 *Cmd* 和 *Alt* 键对调，则该键标为 *Alt* 键)粘贴怎么样？

```
sudo apt install xkbset

echo "" >> ~/.bashrc
echo "xmodmap -e \"keycode 134 = Pointer_Button2\"; xkbset m" >> ~/.bashrc 
```

它的工作原理就像当你用鼠标高亮显示一些文本，然后点击**中间的按钮**将它粘贴到其他地方。

当你在 Ubuntu 中用鼠标高亮显示文本时，它会被复制到一个特殊的缓冲区，你可以通过点击鼠标中键来粘贴。

在 CLI 中粘贴通常是用 **Shift + Ins** 来完成的，祝你好运找到插入键。

# 触控板问题

如果你的鼠标光标跳动，或者你在使用 Mac 的触控板时遇到了一般问题，那么你应该安装`libinput`:

```
sudo apt install xserver-xorg-input-libinput 
```

要自定义触控板行为，您需要修改`/usr/share/X11/xorg.conf.d/60-libinput.conf`文件。

上面的一些更改需要重新启动。

# 高 DPI 显示

## 香草 Ubuntu

要立即调整文本大小(字体大小),请尝试:

```
gsettings set org.gnome.desktop.interface text-scaling-factor 1.65 
```

我认为最好是从命令行(CLI)学习如何做这种事情，但是如果你想查看所有可用的选项，你将需要`dconf-editor`:

```
sudo apt install dconf-editor 
```

_ 您可以通过执行`dconf-editor`并浏览到我们上面使用的位置来检查您刚刚更改的值: **org - > gnome - >桌面- >界面。** _

## i3 Ubuntu

不幸的是， **i3** 没有使用`org.gnome.desktop.interface text-scaling-factor`中设置的信息。

因此，调整显示的另一种方法是改变屏幕模式(分辨率)，这种方法效果很好:

```
xrandr --output eDP-1 --mode 1680x1050 
```

我们所做的是将分辨率从 2560x1600 降低到 1680x1050。

提示~返回登录屏幕

在 i3 上，你可以用 **Shift + Super + E.** 来结束你的会话

***超级**是**视窗的**键。*

如果您无法按下该组合键，您可以使用(小心地)下一个命令:

```
kill -9 -1 
```

这将终止所有用户进程，并使您返回登录屏幕。

*   **不要**用`sudo`执行。
*   如果您有未保存的数据，请不要执行。