# 如何修复无效凭证？脸书 API 弃用后，facebook 的宝石破碎。

> 原文：<https://dev.to/n1ckbren/how-to-fix-invalid-credential-omniauth-facebook-gem-broken-after-facebook-api-deprecation>

转到宝石文件:
更改

> gem 'omniauth-facebook '

到

> gem 'omniauth-facebook '，' ~> 4.0.0 '

在用于更新 omniauth-facebook 的终端捆绑包中(**‘捆绑包更新’可以破坏你的应用**，因为它更新了你所有的宝石)。

> 捆

将更改推送到生产服务器。

1.  Gemfile
2.  2.Gemfile.lock

**重启生产服务器**
我的情况是:

我从 2.0.1 更新到 4.0.0

一切正常，但是您需要**重启生产服务器**(停止并启动 unicorn 或其他)。

[来源](https://mozpi.com/articles/how-to-fix-invalid-credential-omniauth-facebook-gem-broken-after-facebook-api-deprecation-id-83)