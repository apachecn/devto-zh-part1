# 在 JavaScript 中克隆对象

> 原文：<https://dev.to/adamkdean/cloning-objects-in-javascript-1k41>

今天只是一个简短的帖子。

在 JavaScript 中克隆对象和中断引用的一个好方法是序列化和反序列化对象。将它转换成 JSON 字符串并转换回对象的过程会切断所有引用。不幸的是，它也破坏了某些类型，比如`Date`，可能还有`RegEx`。

```
var clone = JSON.parse(JSON.stringify(original)); 
```

Enter fullscreen mode Exit fullscreen mode

在保留类型的同时克隆对象的一个更好的方法(我发现)是使用 [`jQuery.extend`](http://api.jquery.com/jQuery.extend/) 。

```
// Shallow copy
var newObject = jQuery.extend({}, oldObject);

// Deep copy
var newObject = jQuery.extend(true, {}, oldObject); 
```

Enter fullscreen mode Exit fullscreen mode

还有其他更快的方法，但是当你一秒钟可以运行 [120，000 个深层拷贝](http://jsperf.com/cloning-an-object/2)时，那多出来的微秒真的值得吗？