# markdown 应该如何保存和渲染？

> 原文：<https://dev.to/michael/how-should-markdown-be-saved-and-rendered-51f>

我试图找出将 markdown 保存到数据库中并进行渲染的最佳方法。

raw markdown 应该作为字符串保存到数据库中，然后在前端呈现之前返回并转换为 HTML 吗？还是应该将 markdown 转换成 HTML，然后作为字符串保存到数据库中？然后它可以作为原始 HTML 被传递到前端。

我的设置:

*   MongoDB for db
*   后端节点
*   Ember.js for frontend

很想知道那里使用了什么方法。