# SanDisk 的 Connect Drive 应用程序和 Android Marshmallow 上的屏幕覆盖错误

> 原文：<https://dev.to/victoroalvarez/sandisk-s-connect-drive-app-and-screen-overlay-error-on-android-marshmallow-4799>

昨天，我安装并试图运行 SanDisk 的 Connect Drive 应用程序，这样我就可以从我的 Android 智能手机上访问我的无线闪存盘。然而，该应用程序拒绝运行，并发出通知，解释必须关闭“[屏幕覆盖设置](https://www.howtogeek.com/271519/how-to-fix-the-screen-overlay-detected-error-on-android/)，否则无法设置访问权限。这个问题一直存在，直到在启动“[最近的应用程序菜单](https://www.tomsguide.com/us/close-android-apps,news-21281.html)”(带有方形按钮)后，我注意到出现了一个“ES 文件浏览器”窗口，表明这个应用程序正在运行。但是，我没有打开这个 app。我当时意识到，也许这个应用程序通过主动绘制其他应用程序导致了问题。当我卸载 ES 文件浏览器，连接驱动器让我设置权限，我的无线闪存盘工作完美。