# i3wm 中的动态屏幕定义

> 原文：<https://dev.to/mrtango/dynamic-screen-definitions-in-i3wm-328a>

下面我们将为 i3 生成一个定制的屏幕定义配置文件，并使用 update-conf.py 从 config.d 目录生成 i3 配置。

## 安装要求

为了从 config.d 生成一个配置文件，我们使用 Python 包 *update-conf.py* ，所以让我们安装它。

```
sudo pip install update-conf.py 
```

Enter fullscreen mode Exit fullscreen mode

## 分隔 i3 配置文件

现在，让我们创建 config.d 目录，并将配置文件放入该目录。

```
mkdir ~/.i3/config.d
cp ~/.i3/config ~/.i3/config.d/99-main 
```

Enter fullscreen mode Exit fullscreen mode

对于屏幕定义，我们需要一个单独的配置文件如下:

```
set $mainscreen DP-1
set $sidescreen eDP-1 
```

Enter fullscreen mode Exit fullscreen mode

## [T1】创建 gen-screen-config.sh 脚本](#creating-the-genscreenconfigsh-script)

如果我们使用不同的显示器设置或将显示器插入计算机的不同端口，此文件将会改变。为了更新这个文件，我们将使用下面的脚本。

```
#!/bin/bash

xrandr --listactivemonitors | awk '$1 ~ /^0/ {print "set $mainscreen " $4}'
xrandr --listactivemonitors | awk '$1 ~ /^1/ {print "set $sidescreen " $4}' 
```

Enter fullscreen mode Exit fullscreen mode

## 生成 i3 的屏幕配置文件

可以这样使用脚本:

```
./gen-screens-conf.sh > ~/.i3/config.d/10-screens 
```

Enter fullscreen mode Exit fullscreen mode

## 从 config.d 文件生成 i3 配置

为了从 config.d 目录生成 i3 配置文件，我们使用 update-conf.py:

```
update-conf.py -f /home/maik/.i3/config 
```

Enter fullscreen mode Exit fullscreen mode

这将把 config.d 目录中的所有文件连接到 i3 配置文件中。

## 优化流程

为了更容易在设置之间切换，我们可以将调用集成到 i3 重启绑定中。

为了不弄乱 i3 配置，并确保在重新生成 i3 配置时生成屏幕配置，我们创建了另一个名为*refresh-screen settings . sh*的脚本。

```
#!/bin/bash

~/.i3/scripts/gen-screens-conf.sh > ~/.i3/config.d/10-screens
update-conf.py -f ~/.i3/config 
```

Enter fullscreen mode Exit fullscreen mode

有了这个脚本，我们可以在重启绑定中使用它，如下所示:

```
bindsym $mod+Shift+r exec /home/john/.i3/scripts/refresh-screensettings.sh; restart 
```

Enter fullscreen mode Exit fullscreen mode

现在，每次我们改变设置，我们只需要重新启动 i3。确保在重新启动 i3 之前已经设置了主屏幕。你可以用 ARandR 程序做例子。