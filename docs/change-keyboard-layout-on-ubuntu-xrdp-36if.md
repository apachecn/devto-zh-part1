# 在 Ubuntu + Xrdp 上更改键盘布局

> 原文：<https://dev.to/adamkdean/change-keyboard-layout-on-ubuntu-xrdp-36if>

如果你使用 Ubuntu 和 Xrdp，你会发现键盘非常不准确，你应该换一个。要做到这一点，你需要有打开`System Settings`窗口的能力，这在 RDP 上空是做不到的。在那里，进入`Text Entry`，点击底部的小加号。现在选择适合您的键盘布局，并测试它的工作情况。

现在，最后，要将这些设置应用于 Xrdp，请运行以下命令:

```
sudo xrdp-genkeymap /etc/xrdp/km-0409.ini 
```

Enter fullscreen mode Exit fullscreen mode

注销，用 Xrdp 重新登录，现在应该可以工作了。