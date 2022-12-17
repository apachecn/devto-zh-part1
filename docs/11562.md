# 使用未知参数调用回调

> 原文：<https://dev.to/adamkdean/invoke-callbacks-with-unknown-arguments-51fo>

在处理一些插装代码时，我需要将一个函数和未知数量的参数传递给另一个函数，然后该函数将计时该函数的执行。使用`fn.prototype.apply`，这变得非常容易。

假设我们想调用一个函数，但执行一些其他代码，我们会这样做:

```
function invokeCallback(callback) {
  var params = Array.prototype.slice.call(arguments, 1);
  return callback.apply(null, params);
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们三种不同的功能:

```
function action() {
    console.log('action');
}

function greet(msg) {
    console.log(msg);
}

function nameage(name, age) {
    var msg = 'My name is ' + name + ' and my age is ' + age;
    console.log(msg);
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是我们如何动态调用它们，而不需要 invokeCallback 了解它们的任何信息:

```
invokeCallback(action);
invokeCallback(greet, 'Hello!');
invokeCallback(nameage, 'Bob', '20'); 
```

Enter fullscreen mode Exit fullscreen mode

太神奇了。