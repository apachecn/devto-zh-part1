# 了解 Vue 路由器导航卫士[VueJS]

> 原文：<https://dev.to/patrickodacre/learn-vue-router-navigation-guards-vuejs-24hk>

> 订阅我的 YouTube 频道获取更多关于使用 VueJS 和 AdonisJS 的教程——一个用于 NodeJS 的 MVC 框架！

## 看视频+叉笔

[https://www.youtube.com/embed/mts37TpiScM](https://www.youtube.com/embed/mts37TpiScM)

点击这里查看代码笔:

[https://codepen.io/patrickodacre/embed/owjVNK?height=600&default-tab=result&embed-version=2](https://codepen.io/patrickodacre/embed/owjVNK?height=600&default-tab=result&embed-version=2)

导航卫士是一个非常有用的工具；他们可以帮助你完成一些非常复杂的 UI 工作。

## 卫兵

查看此处的[文档](https://router.vuejs.org/en/advanced/navigation-guards.html)，您会看到 Vue 路由器提供了一系列保护措施:

### 全球卫士

*   beforeeach()
*   afterEach()
*   之前解决()

在“路由器”对象上找到。

### 按路线守卫

*   beforeEnter()

在每个单独的`route`定义对象上找到。

### 组件内防护

*   beforeRouteEnter()
*   beforeRouteUpdate()
*   beforeRouteLeave()

放置在单个组件的`option`对象上。

当你从一条路线走到另一条路线时，每个守卫都允许你在特定的点上插入一些工作。

要真正了解在给定情况下使用哪种方法，了解导航到给定路线时事件的顺序会有所帮助:

## 全导航解析流程

*   导航触发
*   调用停用组件中的离开保护装置
*   呼叫全球`beforeEach`警卫
*   调用重用组件中的 beforeRouteUpdate 保护(2.2+)
*   在路线配置中输入前调用
*   解析异步路由组件
*   在激活的组件中调用 beforeRouteEnter
*   调用全局 beforeResolve guards (2.5+)
*   导航确认。
*   Each 挂钩后调用全局。
*   DOM 更新已触发。
*   调用传递给实例化实例的 beforeRouteEnter 防护中的 next 的回调。

## 发现这个有用吗？

如果这对你有帮助，请告诉我！在下面发表评论， [**订阅**我的 YouTube 频道](http://buildtolearn.io/youtube-dt)，确保你不会错过我未来关于 VueJS 和 NodeJS 的教程。