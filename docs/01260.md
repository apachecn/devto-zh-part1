# 如何在 Powershell 通知中包含单击事件？

> 原文：<https://dev.to/it_command/how-do-i-include-click-event-in-powershell-notification-1a47>

## 这是我得到的

-可以创建通知的 powershell 脚本。这个工作脚本可以在[这里找到](http://www.itcommand.tech/Powershell.txt)
——一个批处理脚本来调用表示脚本

## 这是我需要的

-让我的 powershell 脚本在点击
时也启动一个程序-就是这样
我知道这是可能的。我不知道怎么做。注意我用的是 Windows 10。
以下是我目前掌握的最新版本:

```
 [void]         

 $objNotifyIcon = New-Object System.Windows.Forms.NotifyIcon 
 $objNotifyIcon.Icon = "icon.ico"
 $objNotifyIcon.BalloonTipIcon = "None"
 $objNotifyIcon.BalloonTipText = "Body  text or description" 
 $objNotifyIcon.BalloonTipTitle = "Title" 
 $objNotifyIcon.Visible = $True 
 register-objectevent $objNotifyIcon BalloonTipClicked BalloonClicked_event - Action Start-Process -FilePath "C:\users\Public\ITCMD\Toggler\Toggle.bat"
 $objNotifyIcon.ShowBalloonTip(10000) 
```

请注意，我现在不允许我启动一个程序，也把图标留在系统^托盘中，不重新启动就不能删除。(大问题)。