# 在 Mercurial 中安全地保存密码

> 原文：<https://dev.to/shiena/mercurial-50oj>

要在 Mercurial 中保存密码，可以使用以下方法。

```
[auth]
example.com.prefix = https://example.com
example.com.user = xxxxxxx
example.com.password = xxxxxxx 
```

但是，因为要写生密码，所以不能说是安全的。
因此使用[Mercurial-Keyring](https://bitbucket.org/Mekk/mercurial_keyring) 扩展。
如下所示，如果将该扩展设为有效并删除密码设定，则仅第一次会被要求输入密码并保存到密码数据数据库中。
从下次开始使用密码数据数据库，所以可以安全地保管密码。

```
[extensions]
mercurial_keyring = /path/to/mercurial_keyring.py

[auth]
example.com.prefix = https://example.com
example.com.user = xxxxxxx 
```

Windows 版的 TortoiseHG 附带了该扩展，只需以下设置即可启用。

```
[extensions]
mercurial_keyring = 
```

其他密码的保存地址等详细内容记载在[tortoise Hg v 1.0.0 documentation/8.5.mercurial-key ring](http://tortoisehg.bitbucket.org/manual/2.8-ja/extensions.html#mercurial-keyring)中，请作为参考。