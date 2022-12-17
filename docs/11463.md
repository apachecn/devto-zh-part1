# 在 OSX 上安装 awscli

> 原文：<https://dev.to/adamkdean/install-awscli-on-osx-2374>

在 OSX 上安装`awscli`很容易。如果你还没有，安装 pip (python 的包管理器)。

现在，要安装`awscli`，请这样做:

```
pip install awscli 
```

Enter fullscreen mode Exit fullscreen mode

然后使用以下命令对其进行配置:

```
aws configure 
```

Enter fullscreen mode Exit fullscreen mode

现在用以下测试它:

```
aws ec2 describe-regions 
```

Enter fullscreen mode Exit fullscreen mode