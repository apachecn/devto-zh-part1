# 重命名 Ubuntu droplet

> 原文：<https://dev.to/adamkdean/rename-ubuntu-droplet-3foo>

要重命名数字海洋 Ubuntu droplet，请更新`/etc/hostname`和`/etc/hosts`，然后运行:

```
service restart hostname 
```

Enter fullscreen mode Exit fullscreen mode

为此，您需要 root 访问权限或 sudo。