# 模块不是全局的

> 原文：<https://dev.to/designfrontier/modules-not-globals-33fd>

通常新的 JS 开发人员会问他们什么时候应该把一些东西变成一个独立的模块。什么时候函数应该变成它自己的文件？关于组件化架构，我们可以谈论很多事情。但是有一件事应该是一个巨大的闪光信号，表明你需要一个模块。

那就是全局变量。每当你触摸窗户。或者全球。或者一些其他共享变量范围。

下一次你试图在全球范围内分享东西的时候...抓一个模块，你可以要求分成很多模块。通常这些包装器非常轻，它们会让你的代码更加整洁。