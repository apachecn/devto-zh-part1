# 将 SSH 密钥复制到服务器

> 原文：<https://dev.to/scottrobertson/copy-ssh-keys-to-server-2164>

当您希望不用输入密码就能登录到服务器时，您需要做的就是用 SSH 登录。然而我不知道为什么，但是几年前我花了几个小时找到了一个简单的方法。

首先，您需要使用以下代码生成您的 SSH 密钥:

```
ssh-keygen 
```

Enter fullscreen mode Exit fullscreen mode

从这一点上来说，我通常只是点击回车，并使用默认值。你可以设置一个密码，但是这会很烦人。

现在您需要将 SSH 密钥复制到您希望登录的服务器上。我已经找到了很多方法来做到这一点，但没有一个像下面这样简单

```
ssh-copy-id username@server.com 
```

Enter fullscreen mode Exit fullscreen mode

然后会最后一次询问您的密码。完成后，您只需使用:
即可登录服务器

```
ssh username@server.com 
```

Enter fullscreen mode Exit fullscreen mode