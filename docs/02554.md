# 隐藏板-“通过 http 复制/粘贴”用于非敏感文本

> 原文：<https://dev.to/sarmis/hidden-boards---copypaste-via-http-for-non-sensitive-text-c1i>

大家好，我刚刚建立了[http://hiddenboards.com](http://hiddenboards.com)它的目的是使私人(但不是太敏感的)文本从一台电脑传输到另一台电脑，无需安装任何东西或经历漫长的注册过程。(将序列号复制/粘贴到新机器上)

你可以创建一个公告板，公告板的内容只对那些拥有(随机)公告板 ID 的人(以及作为管理员的我)开放。

在将数据发布到 firebase 之前，我将添加一层(简单的)加密。

我将感谢任何意见。

该堆栈是 firebase、vue.js 和 bulma.io(目前运行在自由层上，因此应该限制在大约 100 个同时连接)