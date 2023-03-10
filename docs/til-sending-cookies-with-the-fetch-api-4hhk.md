# TIL:用获取 API 发送 cookies

> 原文：<https://dev.to/jasonmerino/til-sending-cookies-with-the-fetch-api-4hhk>

今天，我不顾一切地试图为我正在做的一个副业项目创建一个登录表单，但我无法在 [Express](http://expressjs.com/) 中从一个页面加载到另一个页面加载保持这个会话。我是通过 ajax 调用进行身份验证的，所以很自然地，我试图用 req.session.save()强制会话持久化，但这也不起作用。

当我注意到 req.cookies 在发出 auth 请求时作为一个空对象通过时，我简直要发疯了，这在我的情况下不是一个好主意，因为我的头发是自己掉下来的。经过一点研究，我发现因为我使用了新的 window.fetch API(并且我使用了[同构提取 polyfill](https://www.npmjs.com/package/isomorphic-fetch) )并且我忽略了凭证标志，所以我的 cookies 没有随请求一起发送。

这是我之前的记录。

```
fetch("https://url/to/my/api/endpoint", {
  method: "post",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    email,
    password,
  }),
}) 
```

这是我之后吃的。

```
fetch("https://url/to/my/api/endpoint", {
  method: "post",
  credentials: "same-origin", // here's the magical line that fixed everything
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    email,
    password,
  }),
}) 
```

原来凭证标志默认为‘省略’,这基本上意味着不要担心用这个请求发送 cookies。有三个不同的选项，如果你想了解更多，我会在 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Request/credentials) 上阅读，因为这是我发现的最容易理解的解释凭证标志一切的页面。