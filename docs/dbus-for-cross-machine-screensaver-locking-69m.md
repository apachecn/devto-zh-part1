# 用于跨机器屏保锁定的 DBUS

> 原文：<https://dev.to/michaelmior/dbus-for-cross-machine-screensaver-locking-69m>

我通常定期使用两台计算机工作。当我把我的笔记本电脑带进办公室时，那就是三个了。我有一个习惯，当我出门的时候，我会锁上我的电脑，但是当我同时有三台机器在运行的时候，这是一件痛苦的事情。我决定，我希望能够锁定一台机器，并让其余的机器也这样做。我发现使用 DBUS 可以做到这一点，因为每当锁定状态改变时，gnome-screensaver 都会发出一个信号。我转向我可信赖的 Python，开始不停地攻击。

事不宜迟，下面是代码:

```
#!/usr/bin/python

import dbus
from dbus.mainloop.glib import DBusGMainLoop
import gobject
import os

# Hosts which should be locked/unlocked
hosts = ['starks', 'mmior-laptop']
# Set to True to turn on displays of other
# hosts after unlocking
wake = False

def toggle_lock(x):
  if x == 0:
    for host in hosts:
      os.system('ssh ' + host + ' export DISPLAY=:0; gnome-screensaver-command -d')
      if wake:
        os.system('ssh ' + host + ' export DISPLAY=:0; xset dpms force on; gnome-screensaver-command -d')
  if x == 1:
    os.system('xset s activate')
    for host in hosts:
      os.system('ssh ' + host + ' export DISPLAY=:0; xset dpms force off; gnome-screensaver-command -l')

DBusGMainLoop(set_as_default=True)

# Connect to the session bus and
# install our signal handler
bus = dbus.SessionBus()
bus.add_signal_receiver(toggle_lock,
  'ActiveChanged',
  'org.gnome.ScreenSaver',
  path='/org/gnome/ScreenSaver')

# Start the loop and wait for the signal
gobject.MainLoop().run()

# Clean up by removing the signal handler
bus.remove_signal_receiver(toggle_lock,
  'ActiveChanged',
  'org.gnome.ScreenSaver',
  path='/org/gnome/ScreenSaver') 
```

Enter fullscreen mode Exit fullscreen mode

这需要作为后台守护程序运行，以监视信号。要使用该脚本，只需用您希望锁定/解锁的主机名列表替换 hosts 数组。wake 变量控制解锁时是否应打开其他机器的屏幕。当然，您可以添加任何您希望在锁定/解锁时执行的操作。

欲了解更多信息，请查看 [Python DBUS 教程](http://dbus.freedesktop.org/doc/dbus-python/doc/tutorial.html)或查看如何使用 [`dbus-monitor`](http://linux.die.net/man/1/dbus-monitor) 来观察总线上的任何信号。

如果你想看看你安装的各种程序的 DBUS 接口，可以查看 Debian 包`qt4-dev-tools`。它可能有点大，因为它需要安装大量的 Qt 库，但是包含的`qdbusviewer`工具对于浏览信号和测试消息发送非常有用。

如果你遇到任何问题或者想出一个很酷的用例，请在评论中告诉我！