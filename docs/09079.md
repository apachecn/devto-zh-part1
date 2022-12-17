# 在 JavaScript 中处理时间间隔

> 原文：<https://dev.to/amit_merchant/handling-time-intervals-in-javascript>

在做一个[电子管](//electron.atom.io) app [电子管](https://github.com/amitmerchant1990/pomolectron)的时候，我需要通过 JavaScript 的 [setInterval()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval) 函数来处理不同的时间间隔。
我基本上需要在我的应用中实现三个定时器:

*   25 分钟的番茄
*   短暂休息 5 分钟
*   十分钟的长时间休息

我可以想到两种方法来实现这三种情况下的倒计时。一，通过使用三个不同的`setInterval()`来实现不同的定时器。第二，找到另一种方法为所有三个定时器使用相同的`setInterval()`。

# 解

所以，我尝试了第二种方法。我可以对所有三个定时器使用相同的`setInterval()`,方法是将它赋给一个变量，如下所示，

```
var pomodoroIntervalId;

function startTimer(duration, display) {
  timer = duration;
  pomodoroIntervalId = setInterval(function(){
    if (--timer < 0) {
        timer = duration;
    }

    minutes = parseInt(timer/60, 10);
    seconds = parseInt(timer%60, 10);

    minutes = minutes < 10 ? '0'+minutes : minutes;
    seconds = seconds < 10 ? '0'+seconds : seconds;

    display.textContent = minutes+ ":" + seconds;

    if(minutes == 0 && seconds == 0){
      notifyUser();
    }
  }, 1000);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后通过使用传递时间间隔 ID 的 [clearInterval()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/clearInterval) 方法首先清除当前时间间隔，在其他计时器上使用相同的`startTimer()`。在我们的例子中，我把它赋给了一个全局变量`pomodoroIntervalId`。这基本上清除了当前运行的时间间隔，以便下次设置另一个定时器时可以使用它。代码如下所示...

```
function resetTimer() {
  clearInterval(pomodoroIntervalId);
} 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们在不同的用例中使用相同的`setInterval()`函数的好处，这使得代码简洁流畅。我认为这是 JavasScript 的一个非常方便的特性。