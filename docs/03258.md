# 获取 Laravel

> 原文：<https://dev.to/jerguslejko/fetching-laravel-c98>

**什么？**昨晚，不知羞耻的是，我花了大约 3 个小时才弄明白这个问题。我在一个有“公司”概念的 Laravel 网站上工作。`companies.index`视图简单地列出了数据库中的所有公司(*无分页*)。我想添加一个搜索功能，当用户在查询中键入内容时，该功能会更新 HTML 表。

# 一期

当我使用花哨的`fetch()` API 发送 AJAX 请求时，Laravel 会在没有任何特殊原因的情况下用`404`响应。我没有查询 API 路由，也没有使用`api`中间件组。我只是想使用我已经有的相同的*基于会话的认证*。

# 解

原来，`fetch()` API 在默认情况下不会将 cookies 附加到请求中。Laravel 正在接收一个无法识别的请求(无 cookies =无会话标识符)，因此无法识别登录的用户。为了随请求一起发送 cookies，您需要在 fetch 的配置对象中设置`credentials: 'same-origin'`。这个电话是这样的:

```
fetch(url, {
    credentials: 'same-origin',
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，请求将包含必要的 cookies，Laravel 认证系统将能够识别用户。🎉