# 如何在 Ubuntu 中将 uWSGI 与 python3.6 配合使用

> 原文：<https://dev.to/pauloxnet/how-to-use-uwsgi-with-ptyhon36-in-ubuntu>

如何在 Ubuntu 16.10+中使用 uWSGI 和 python 3.6:

```
$  sudo apt-get install python3.6 python3.6-dev uwsgi uwsgi-src uuid-dev libcap-dev libpcre3-dev
$  cd ~
$  PYTHON=python3.6 uwsgi --build-plugin "/usr/src/uwsgi/plugins/python python36"
$  sudo mv python36_plugin.so /usr/lib/uwsgi/plugins/python36_plugin.so
$  sudo chmod 644 /usr/lib/uwsgi/plugins/python36_plugin.so
$  uwsgi --plugin python36 -s :0 # Ubuntu 16.10
... Python version: 3.6.0b2 (default, Oct 11 2016, 05:27:10)  [GCC 6.2.0 20161005] ... $  uwsgi --plugin python36 -s :0 # Ubuntu 17.04
... Python version: 3.6.1 (default, Mar 22 2017, 06:17:05)  [GCC 6.3.0 20170321] ... 
```

Enter fullscreen mode Exit fullscreen mode

> 最初发布于[www.paulox.net](http://www.paulox.net/2017/04/04/how-to-use-uwsgi-with-python3-6-in-ubuntu/)