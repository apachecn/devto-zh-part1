# 自然盒子布局模型

> 原文：<https://dev.to/adamkdean/natural-box-layout-model-40jh>

不幸的是，浏览器并不总是实现我们想要的默认 CSS 规则。几乎肯定应该加到每个浏览器上的一条规则是，对任何东西都应用边框大小。

```
/* apply a natural box layout model to all elements */
* { -moz-box-sizing: border-box; -webkit-box-sizing: border-box; box-sizing: border-box; } 
```

Enter fullscreen mode Exit fullscreen mode

如果我没记错的话，感谢保罗·爱尔兰人。