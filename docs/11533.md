# 在提交消息中使用感叹号！

> 原文：<https://dev.to/adamkdean/using-exclamation-marks-in-commit-messages-37ll>

如果您曾经对您最近完成的一组工作特别兴奋，并试图通过在提交消息中添加感叹号来表达您的兴奋，那么您可能会遇到这个特定的错误:

```
git commit -m "I did some most excellent work!"
sh.exe": !": event not found 
```

Enter fullscreen mode Exit fullscreen mode

你有两种解决方法。一个最好的方法是使用单引号:

```
git commit -m 'I did some most excellent work!'
[branch 123abc] I did some most excellent work!
1 file changed, 2 insertions(+) 
```

Enter fullscreen mode Exit fullscreen mode

另一种方法是，如果需要双引号，可以在末尾加上单引号:

```
git commit -m "I did some most excellent work"'!'
[branch 123abc] I did some most excellent work!
1 file changed, 2 insertions(+) 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，简单地逃避角色是行不通的，你只会以`\!`告终。