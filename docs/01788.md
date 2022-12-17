# XHR 不会在 IE 中的“卸载前”事件时发送到服务器

> 原文：<https://dev.to/goshishah/xmlhttprequestxhr-not-send-to-server-on-beforeunload-event-d1l>

这是我在任何博客/网站上的第一篇文章，我不是一个好作家。我是初级 ASP.NET 软件工程师，最近我在 React 工作，负责前端开发。

昨天在窗口关闭时用 JavaScript 写了一个函数。函数的职责是向服务器发送 xhr 调用。

## 这里是我的源代码。

```
// binding beforunload event

 componentDidMount() {
      if (window.addEventListener) {
        window.addEventListener('beforeunload', this.onUnload, false);
      } else if (window.attachEvent) {
        window.attachEvent('onbeforeunload', this.onUnload);
      }
    }

// call api function 'unlock'
onUnload() {
      unlock();
    }

// api 
export function unlock(Id) {
  return axios
    .get(UNLOCK(Id))
    .then((data) => data);
} 
```

Enter fullscreen mode Exit fullscreen mode

它在 chrome 上运行良好，但在 IE 上就不行了。我花了很多时间来调试这个问题，但是没有成功。之后，我的一个同事建议我向服务器发送一个同步调用，因为异步浏览器没有等待响应。在和他争论了很长时间后，我实施了，而且奏效了。

## 将异步改为同步后

```
export function unlock(Id) {
  // use jQuery ajax call becaus axios does not provide {async: false} option
  window.jQuery.ajax({
    type: 'GET',
    url: UNLOCK(Id),
    async: false,
    success(data) {
      //code 
    },
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

# 讨论

我的理解是当窗口关闭时调用解锁功能。函数应该在浏览器选项卡关闭后向服务器发送 xhr 请求。在我的情况下，我不需要 xhr 调用的响应。

## 问题:为什么解锁功能没有将调用发送到服务器？