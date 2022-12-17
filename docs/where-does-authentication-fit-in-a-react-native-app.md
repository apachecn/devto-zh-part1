# React 原生应用中的身份验证在哪里？

> 原文：<https://dev.to/goshacmd/where-does-authentication-fit-in-a-react-native-app>

*本帖最初发表于[goshakk . name](https://goshakkk.name/auth-in-react-native-apps/)T3】*

在 web 应用程序中，你可以使用 cookies 或者在本地存储中存储一个令牌…

但是如何让一个用户登录你的 React 原生应用呢？

但是首先…让我们缩小一点，重复一下开发人员通常所说的“从面向用户的应用程序的角度来说，身份验证:

*   用户登录的方式；
*   “保存”用户会话的方法；
*   一种让服务器知道用户是谁的方法。

大多数情况下，身份验证是使用*令牌*来完成的。

然而，令牌认证的概念并不局限于 React 本地应用程序；本地移动和桌面应用程序，以及许多 web 应用程序，都使用令牌。

简而言之，令牌身份验证意味着:

我们不是在每个请求中向服务器传递用户名和密码，而是在每个请求中传递一些随机的字符串，称为*令牌*。我们通过用用户名和密码向服务器发出“登录请求”来获得这个令牌。

### 用户登录的一种方式

用户必须向我们证明他们是他们所说的人，通常是通过输入他们的用户名和密码。不过，实现这一点的 UI 并不是重点，因为它会因应用程序而异。

这个阶段最确定的是:我们会用输入的用户名和密码要求服务器给我们一个*令牌*。

### 一种保存用户会话的方式

如果每次你打开 Twitter 应用程序，你都必须重新输入你的证书，生活就没有意思了。

网络应用很简单:有 cookies，然后还有`localStorage`。

假设我们有一个想要保存的令牌。但是，你在 React 本地应用程序中做什么呢？

两个选项:

**1。使用与 React Native 捆绑的 [`AsyncStorage`](https://facebook.github.io/react-native/docs/asyncstorage.html) 。**这样用:

```
// to set
AsyncStorage.setItem('@app:session', token);

// to get
AsyncStorage.getItem('@app:session').then(token => {
  // use token
}); 
```

Enter fullscreen mode Exit fullscreen mode

它很容易使用，但它有安全隐患:所有东西都以纯文本形式存储，而不是加密。因此，在根/越狱设备上，其他应用程序可能会访问您的数据。此外，通过物理访问，可以提取应用程序数据，包括令牌。

**2。iOS 附带了钥匙串，这是一种安全储存凭证数据的方式。在 Android 上，还有一种安全存储数据的方法。**

因此，您可以使用 [`react-native-keychain`](https://github.com/oblador/react-native-keychain) React 本地库来利用这些选项。

```
// to set
Keychain.setGenericPassword('session', token);

// to get
Keychain.getGenericPassword.then(creds => creds.password).then(token => {
  // use token
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 一种让服务器知道用户是谁的方式

例如，这通常是通过使用头传递令牌来实现的。

就像在浏览器环境中一样，你可以在 React Native 中使用`fetch`。而[用`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch#Supplying_request_options) 发送报头也是蛮容易的:

```
// assuming `token` is already fetched somehow

const headers = {
  Authentication: `Bearer ${token}`,
};

fetch(path, { method: 'GET', headers: headers }).then(...); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

如果你喜欢这里的内容，请订阅我的博客，确保你不会错过我下一篇关于 React Native 和 React 的文章。