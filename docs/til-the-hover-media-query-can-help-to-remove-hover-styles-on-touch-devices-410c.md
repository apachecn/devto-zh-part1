# TIL:悬停媒体查询可以帮助移除触摸设备上的悬停样式(但可能包括误报)

> 原文：<https://dev.to/stefanjudis/til-the-hover-media-query-can-help-to-remove-hover-styles-on-touch-devices-410c>

请务必阅读到文章的结尾...这是经过编辑的，并不像我最初想的那么简单。

今天我在推特上关注了一个有趣的话题。Chris Coyier 询问了 CSS 中`@supports`规则的用例。一个回复分享了一个禁用可聚焦元素的“停滞悬停状态”的方法。

*补充说明:它没有使用`@supports`而是使用媒体查询来检测用户代理是否有悬停支持机制。但是嘿...🤷‍♂️*

如果你经常使用移动设备浏览网页，你可能知道我在说什么。当您单击某个按钮时，该按钮会保持模拟悬停状态，直到另一个元素获得焦点。浏览器以这种方式实现它，为用户提供了一种达到悬停状态的方法。

你真的不应该依赖悬停来构建用户界面。不幸的是，网络是一个混乱的地方。必须有一种方法为触摸设备用户提供与鼠标用户相同的功能。

如果你想摆脱触摸设备上的悬停状态，你可以使用 [`hover` CSS 媒体功能](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/hover#Example)。

```
@media (hover: hover) { 
  a:hover {
    background: red;
    color: white;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

就我个人而言，悬停风格并没有太多的困扰，但我可以想象，它惹恼了一些人。使用此媒体查询，您可以摆脱粘性悬停状态。🎉

如果你想在手机上试试，我设置了一个快速密码笔(【www.my-links.online/hover-states/】T2)。

## 但是等等！！！

帕特里克·h·劳克有一些非常有价值的评论。

> 一般来说，不要试图“检测”触摸设备。设计使其适用于触摸/鼠标/手写笔/键盘。如果必须的话，使用 whatinput.js 或类似工具来检查/猜测最新事件的来源

检测触摸设备变得非常非常困难。像微软 Surface 这样的设备可以有一套输入机制。或者，如果你在平板电脑上使用鼠标会怎么样。在实施媒体查询之前，请务必阅读 Patrick 推荐的资源。

*   [互动媒体特征及其潜力(针对不正确的假设)](https://dev.opera.com/articles/media-features/)
*   [检测触摸:是“为什么”，而不是“如何”](https://hacks.mozilla.org/2013/04/detecting-touch-its-the-why-not-the-how/)