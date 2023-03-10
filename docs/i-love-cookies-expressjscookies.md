# 我喜欢饼干(ExpressJS+cookies)

> 原文：<https://dev.to/sroy8091/i-love-cookies-expressjscookies>

## 安装 Node.js

虽然我的网络应用是一个三屏幕的想法，但一些功能对我们来说是理所当然的，比如登录、注册、仪表板和搜索。所以我用任何人都可以建立的最基本的登录和注册系统开始了我的网络应用。

首先，我在笔记本上安装了 node.js 和 npm。要查看安装说明，请参阅本[指南](https://nodejs.org/en/download/package-manager/)。

## 对饼干的热爱

是的，大多数开发人员都喜欢 cookies，我也是。我用一个包 [cookie 解析器](https://www.npmjs.com/package/cookie-parser)实现了登录和注册部分，这个包用于在你的浏览器中存储 cookie。当用户登录时，Hasura 返回一个授权令牌、用户 id 和角色。

```
{  "hasura_id":  70,  "hasura_roles":  [  "user"  ],  "auth_token":  "9zsbtjosat6xqcwsj32nrokxobcrz8sb"  } 
```

在 cookie-parser 的帮助下，我将 hasura_id 和 auth_token 存储在 cookie 中，并在需要时使用它们在数据库中进行查询。

用于设置 cookies

```
res.cookie("userId", json['hasura_id']);
res.cookie("userName", username);
res.cookie("Authorization", json['auth_token']); 
```

为了找回那些

```
const user = req.cookies.Authorization; 
```

我就是这样检查我的代码是否工作的
[![cookies set](img/60914be32a39fc800fcacca1929ac284.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lVTPPaZp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bmkar0eg5241lwf03yzo.png)

注销后，cookies 被删除，hasura 上的会话也结束，因此授权令牌变得无效。

```
res.clearCookie('Authorization');
res.clearCookie('userName');
res.clearCookie('userId'); 
```

[![cookies reset](img/fac7c30eef9dcbeff56d0b14502b62a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RhPCXq8i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0z1e3b9h14apb6s3zyj8.png)

这是在 ExpressJS 中使用 cookie-parser 的用户帐户活动的基本实现，我将它用于我的 web 应用程序 [SnipCode](http://app.snipcode.hasura.me) 。

*这里是关于这一系列 snipcode 开发的所有帖子的索引*

[第一部分:App Idea](https://dev.to/sroy8091/app-idea-for-hasura-internship)
[第二部分:App 原型](https://dev.to/sroy8091/app-prototype)
[第三部分:本地开发](https://dev.to/sroy8091/hasura-local-development)
[第四部分:G for Git](https://dev.to/sroy8091/g-for-git)
[第五部分:数据建模](https://dev.to/sroy8091/its-modeling-time)
[第六部分:Data&Auth API](https://dev.to/sroy8091/postman-a-major-tool-for-developers)
[第七部分:基本功能](https://dev.to/sroy8091/i-love-cookies-expressjscookies)
[第八部分:App 屏幕 1](https://dev.to/sroy8091/i-built-it-with-codemirror)
[第九部分](https://dev.to/sroy8091/app-screen-2)

| 喜欢我的帖子吗？ |
| --- |
| [![](img/f4f06ea384c5e485d3474b718bbe6f95.png)T2】](https://ko-fi.com/A0A5WBC1) |