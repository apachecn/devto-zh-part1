# 我应该如何打包我的 Django 应用程序？

> 原文：<https://dev.to/hmhrex/how-should-i-package-my-django-app>

我有一个 Django 应用程序，它提供媒体服务(以 Plex 或 Kodi 的形式),我想让它更容易发布给其他人使用。它需要三种服务:

1.  网络服务器(WSGI)
2.  数据库(MySQL)
3.  任务队列(芹菜)

本质上，我希望制作一个一键式应用程序来安装这些服务，并让用户在 Windows，Mac 和 Linux 上运行自己版本的应用程序。

我的背景是 web，所以我对软件分发游戏相当陌生。我了解到可以使用 cx_Freeze 或 pyInstaller 打包 Django 应用程序，但是要让它工作起来相当麻烦，文档也很少和/或缺乏。Django 还会一些奇特的魔法，但并不总是包装得很好。

[![So many options](img/5b17dd5be2cf580630a477894847e283.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--THKaPpCS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pehpywva2talp54e1j5c.gif)

我也考虑过使用 Docker，但是我不想让不太懂技术的用户的安装变得复杂。如果我可以“隐藏”Docker 的安装，并且用户可以通过 Windows installer 安装它和我的项目容器，那就太好了。

有哪些选择？