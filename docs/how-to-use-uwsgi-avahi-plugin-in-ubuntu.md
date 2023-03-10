# 如何在 Ubuntu 中使用 uWSGI Avahi 插件

> 原文：<https://dev.to/pauloxnet/how-to-use-uwsgi-avahi-plugin-in-ubuntu>

*一步一步如何使用用于 GNU/Linux 的 uWSGI Avahi 插件(在 Ubuntu 17.04 上测试)。*

> Avahi uWSGI 插件([github.com/20tab/uwsgi-avahi](https://github.com/20tab/uwsgi-avahi))允许您的实例在您的 mDNS 系统中注册服务。

```
$  sudo apt-get install uwsgi uwsgi-src libavahi-client-dev
$  cd ~
$  PYTHON=python3.6 uwsgi --build-plugin https://github.com/20tab/uwsgi-avahi
$  sudo mv avahi_plugin.so /usr/lib/uwsgi/plugins/avahi_plugin.so
$  sudo chmod 644 /usr/lib/uwsgi/plugins/avahi_plugin.so 
```

Enter fullscreen mode Exit fullscreen mode

配置您的`.ini`文件，如:

```
[uwsgi]
...
project_name = paulox
socket = 127.0.0.1:0
subscribe-to = 127.0.0.1:5005:%(project_name).local
plugins = avahi
avahi-register = %(project_name).local 
```

Enter fullscreen mode Exit fullscreen mode

启动你的皇帝后，你可以在你的日志上看到一些类似的消息:

```
...
Avahi Server Version: avahi 0.6.32
[uwsgi-avahi] registered record paulox.local CNAME ubuntu.local
... 
```

Enter fullscreen mode Exit fullscreen mode

> 最初发表于:[www.paulox.net](http://www.paulox.net/2017/07/21/how-to-use-uwsgi-avahi-plugin-in-ubuntu/)