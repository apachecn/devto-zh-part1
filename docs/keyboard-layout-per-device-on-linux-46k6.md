# Linux 上每个设备的键盘布局

> 原文：<https://dev.to/lepovirta/keyboard-layout-per-device-on-linux-46k6>

我做过一些结对编程，其中有两组键盘和鼠标连接到一台电脑上。我喜欢使用不同于我同事使用的键盘布局。幸运的是，有办法在 Linux 上为每个键盘设置单独的布局。

对于该设置，您需要`xinput`和`setxkbmap`命令。首先，使用`xinput -list`命令列出你所有的输入设备。您应该得到一个类似这样的列表:

```
⎡ Virtual core pointer                          id=2    [master pointer  (3)]
⎜   ↳ Virtual core XTEST pointer                id=4    [slave  pointer  (2)]
⎜   ↳ ETPS/2 Elantech Touchpad                  id=12   [slave  pointer  (2)]
⎜   ↳ Logitech USB Receiver                     id=14   [slave  pointer  (2)]
⎜   ↳ Logitech USB Receiver                     id=15   [slave  pointer  (2)]
⎣ Virtual core keyboard                         id=3    [master keyboard (2)]
    ↳ Virtual core XTEST keyboard               id=5    [slave  keyboard (3)]
    ↳ Power Button                              id=6    [slave  keyboard (3)]
    ↳ Video Bus                                 id=7    [slave  keyboard (3)]
    ↳ Sleep Button                              id=8    [slave  keyboard (3)]
    ↳ USB2.0 0.3M UVC WebCam                    id=9    [slave  keyboard (3)]
    ↳ Asus Laptop extra buttons                 id=10   [slave  keyboard (3)]
    ↳ AT Translated Set 2 keyboard              id=11   [slave  keyboard (3)]
    ↳ ACPI Virtual Keyboard Device              id=13   [slave  keyboard (3)] 
```

Enter fullscreen mode Exit fullscreen mode

接下来，查找您希望更改布局的键盘。在我的例子中，我只列出了一个键盘设备:AT Translated Set 2 keyboard。列表还告诉我们设备被分配到的 ID，在我的例子中是 11。您需要使用选项`-device`将 ID 传递给命令`setxkbmap`。例如，如果我想将我的键盘布局切换到芬兰布局，命令将是:

```
setxkbmap -device 11 us 
```

Enter fullscreen mode Exit fullscreen mode