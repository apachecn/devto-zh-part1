# 显示 id 为“hello”的 div 的第 n 个子级

> 原文：<https://dev.to/vinhlee95/reveal-the-nth-child-of-a-div-with-id-hello-2epg>

大家好，
我被如何用纯 JavaScript 显示 id 为“hello”的 div 的第 n 个子元素所困扰。我其实试过:

```
var elements = document.getElementById(‘hello’);
var child = elements.children[n-1];
child.style.display = ‘block’; 
```

Enter fullscreen mode Exit fullscreen mode

但是，好像不对。很想听听你如何解决这个问题！谢谢大家！