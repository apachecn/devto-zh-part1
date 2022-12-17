# 您应该知道的是 PHP 中的环境变量

> 原文：<https://dev.to/phpprofi/---------php-2gm5>

目前，配置中使用的环境变量是应用程序设置的主要方法，例如数据库凭据、API 密钥、秘密密钥和任何内容，具体取决于您在何处部署 T1 现在，这些设置通过周围的环境进入代码，而不是直接写在配置文件中，或者更糟糕的是，直接写在代码中。

[![You can't leak what you don't store](img/8acbbb04a5eb733702b9976af639ed72.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MyF3GfAc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://jolicode.com/media/original/2017/password.png)

让我们进一步看一看:

*   这是怎么运作的？
*   这真是个好主意吗？
*   如何在 PHP 中处理它们？
*   最后，我要指出一些应该避免的建议和常见错误——我们在现实世界中遇到的陷阱！

http://phpprofi.ru/blogs/post/72 的читать