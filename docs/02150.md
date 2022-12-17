# pkg-config 问题使我在编译 porg 时出错

> 原文：<https://dev.to/acomagu/pkg-config-matters-tripped-me-up-in-compiling-porg-9n8>

在编写《T2》的过程中，我与失败作了斗争，所以我会留下备忘录。

过去它真的很流畅，没有问题，但现在它在我更新的 Ubuntu 17.10 电脑上失败了。

```
$ tar xf porg-0.10.tar.gz
$ ./configure

[...]

configure: error: Package requirements (gtkmm-3.0 >= 3.4.0) were not met:

No package 'gtkmm-3.0' found

Consider adjusting the PKG_CONFIG_PATH environment variable if you
installed software in a non-standard prefix.

Alternatively, you may set the environment variables GTKMM_CFLAGS
and GTKMM_LIBS to avoid the need to call pkg-config.
See the pkg-config man page for more details. 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们来安装`gtkmm-3.0`。

```
$ sudo apt install libgtkmm-3.0-dev
$ ./configure

...

No package 'gtkmm-3.0' found

... 
```

Enter fullscreen mode Exit fullscreen mode

错误信息无变化。

```
$ pkg-config gtkmm-3.0 --libs --cflags
Package gtkmm-3.0 was not found in the pkg-config search path.
Perhaps you should add the directory containing `gtkmm-3.0.pc'
to the PKG_CONFIG_PATH environment variable
No package 'gtkmm-3.0' found 
```

Enter fullscreen mode Exit fullscreen mode

嗯（表示踌躇等）...由于某种原因，`gtkmm-3.0.pc`无法从 pkg-config 中识别。

PKG 配置路径在哪里？

```
$ echo $PKG_CONFIG_PATH

$ 
```

Enter fullscreen mode Exit fullscreen mode

哦，还没定。

[Ubuntu 手册页:pkg-config -返回关于已安装库的元信息](http://manpages.ubuntu.com/manpages/trusty/man1/pkg-config.1.html)

和上面的手册一样，即使没有设置环境变量，pkg-config 也会看到`/usr/lib/pkgconfig`、`/usr/local/lib/pkgconfig`、`/usr/share/pkgconfig`、`/usr/local/share/pkgconfig`。

好的。那么，`gtkmm-3.0`在哪里呢？

```
$ dpkg -L libgtkmm-3.0 | grep '\.pc'
/usr/lib/x86_64-linux-gnu/pkgconfig/gdkmm-3.0.pc
/usr/lib/x86_64-linux-gnu/pkgconfig/gtkmm-3.0.pc 
```

Enter fullscreen mode Exit fullscreen mode

超出引用路径。什么是`/usr/lib/x86_64-linux-gnu`？

好像叫“多拱元组”。

[multi arch/Tuples-Debian Wiki](https://wiki.debian.org/Multiarch/Tuples)

它显示 ABI 和 CPU 指令类型。

我的架构肯定是 x86_64-linux，所以只是把它添加到$ PKG _ 配置 _ 路径。

```
$ export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/lib/x86_64-linux-gnu
$ pkg-config gtkmm-3.0 --libs --cflags
Package xproto was not found in the pkg-config search path.
Perhaps you should add the directory containing `xproto.pc'
to the PKG_CONFIG_PATH environment variable
Package 'xproto', required by 'xau', not found 
```

Enter fullscreen mode Exit fullscreen mode

嗯。现在需要`xproto`。

[Ubuntu–包 X11 proto-core-dev/trusty-updates/all 的文件列表](https://packages.ubuntu.com/en/trusty-updates/all/x11proto-core-dev/filelist)

它似乎已经安装在`/usr/share/pkgconfig`处，所以让我们将它也添加到`$PKG_CONFIG_PATH`中。

```
$ export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/share/pkgconfig
$ pkg-config gtkmm-3.0 --libs --cflags
-pthread -I/usr/include/gtkmm-3.0 -I/usr/lib/x86_64-linux-gnu/gtkmm-3.0/include -I/usr/include/atkmm-1.6 -I/usr/include/gtk-3.0/unix-print -I/usr/inc
lude/gdkmm-3.0 -I/usr/lib/x86_64-linux-gnu/gdkmm-3.0/include -I/usr/include/giomm-2.4 -I/usr/lib/x86_64-linux-gnu/giomm-2.4/include -I/usr/include/pa
... 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错。

而且`./configure; make; make install`也成功了。