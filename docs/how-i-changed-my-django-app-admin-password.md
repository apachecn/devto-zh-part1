# 我如何更改我的 Django 应用管理密码

> 原文：<https://dev.to/kiddeveloper/how-i-changed-my-django-app-admin-password>

我知道大多数人都曾为我们的管理面板设置了一个荒谬的硬密码，将它们保存在某个地方，然后由于遗忘或其他原因丢失了密码。

这是我在学习 Django 教程时遇到的，我只想记录下我是如何解决这个问题的。

1)使用
访问您的 python shell

```
python manage.py shell 
```

Enter fullscreen mode Exit fullscreen mode

这个小代码片段将启动您的 python shell 开发。

2)接下来我们要做的是获取用户列表，这可以通过以下方式完成:

答:从 Django auth:
导入用户

```
from django.contrib.auth.models import User 
```

Enter fullscreen mode Exit fullscreen mode

b:将用户对象赋给一个变量并打印:

```
users = User.objects.all()
print users 
```

Enter fullscreen mode Exit fullscreen mode

这将打印出应用程序中的用户总数，您可以选择哪个是您的管理员。不过一般来说，admin 用户通常总是第一个创建的用户。

3)选择管理员用户的“id”:

```
user = users[0] 
```

Enter fullscreen mode Exit fullscreen mode

4)为用户设置新密码:

```
user.set_password('the password') 
```

Enter fullscreen mode Exit fullscreen mode

5)保存新密码:

```
user.save() 
```

Enter fullscreen mode Exit fullscreen mode