# Safari 对 pre 标签进行了文字换行，产生了不适当的代码换行。

> 原文：<https://dev.to/ben/safari-is-word-wrapping-the-pre-tag-creating-inappropriate-code-wrapping-a9g>

这是现在在 [dev.to](https://dev.to/) 直播的，我还没能找到答案。

下面是一段普通的代码:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    Authentication with firebase
    <script src="https://www.gstatic.com/firebasejs/4.8.0/firebase.js"></script>
    <script src="https://www.gstatic.com/firebasejs/4.6.2/firebase-auth.js"></script>
    <style>
        #app{
        width:50%;
        margin: 100px auto;
        }
        .hide{
        display: none;
        }
    </style>
</head>
<body>
    <div id="app">
        <input type="email" id="txtEmail" placeholder="Enter email" /> <br/>
        <input type="password" id="txtPassword" placeholder="Enter password" /><br/>
        <button id="btnLogin" >Log in</button>
        <button id="btnSignUp" >Sign Up</button>
        <button id="btnLogOut" class="hide" >Log Out</button>
        <button id="withFB">Log In with FaceBook</button>
        <button id="withGithub">Log In with GitHub</button>
    </div>  
    <div id="results"></div>
    <script type="text/javascript" src="script.js"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

在 Chrome 上，它像预期的那样在窄屏幕上换行，侧向滚动显示代码。在 safari 上，`pre`的行为与`pre`不同，它正在创建包装。这会降低代码的可读性，这是一个错误。

(没有特别的原因，代码片段复制自[这篇文章](https://dev.to/aurelkurtula/introduction-to-authentication-with-firebase-bkd)

有人能玩这个帮忙吗？非常感谢。❤️