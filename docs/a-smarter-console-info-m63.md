# 更智能的控制台。信息

> 原文：<https://dev.to/ipasqualito/a-smarter-console-info-m63>

这里有一小段代码，您可以在您的项目中使用它来获得稍微智能一些的控制台消息。对象和数组在登录到控制台时的处理方式与字符串不同。Firebug 给你一个对象的字符串版本，而 console.dir 给你一个可扩展的对象。两者都给了你 Chrome 中的可扩展对象。要解决这个问题，您可以使用这个小片段，它将始终给您正确的版本。

```
 var debug = function(message) {
    "object" === typeof console && ("object" === typeof message || "array" === typeof message ? console.dir(message) : console.info(message));
}; 
```

Enter fullscreen mode Exit fullscreen mode

帖子[更智能的控制台信息](https://www.recoveryarea.nl/javascript/a-smarter-console-info/)首先出现在[恢复区](https://www.recoveryarea.nl)。