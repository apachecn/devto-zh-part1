# 如何分析 Python/Django 应用程序

> 原文：<https://dev.to/cosimo/how-to-profile-python-django-applications-4gm3>

使用[https://github.com/char0n/django-profiler](https://github.com/char0n/django-profiler/)T3 可用的`django-profiler`模块

```
from profiling import profile 
```

还有后来:

```
@profile(stats=True)
def view_to_be_profiled(self, request):
    ... 
```

有几个设置可以用来查看 SQL 查询并调整记录器名称:

```
PROFILING_SQL_QUERIES = True
PROFILING_LOGGER_NAME = 'profiler' 
```

只是给自己提个醒。仅此而已。