# 可重用组件和 Vue 路由器[VueJS]

> 原文：<https://dev.to/patrickodacre/reusable-components-and-vue-router-492h>

> 订阅我的 YouTube 频道获取更多关于使用 VueJS 和 AdonisJS 的教程——一个用于 NodeJS 的 MVC 框架！

将道具从父组件传递到子组件是一种常见的设置，理解这如何使子组件可重用并不困难。

那么，如果我们想要跨多个路由重用一个组件，并让路由更改驱动组件属性和行为呢？

## 看视频+叉笔

[https://www.youtube.com/embed/UIu-7qOdki4](https://www.youtube.com/embed/UIu-7qOdki4)

点击这里查看代码笔:

[https://codepen.io/patrickodacre/embed/jwXJOE?height=600&default-tab=result&embed-version=2](https://codepen.io/patrickodacre/embed/jwXJOE?height=600&default-tab=result&embed-version=2)

## 其他一些注释:

*   确保你的路线定义对象允许道具`props: true`
*   请确保您的组件设置为接收这些道具

```
 const routes = [
  { path: '/', 
    name: 'home', 
    components: {
        content: Home
    }
  },
  { path: "/one", 
    name: 'one', 
    components: {
        header: Foo, 
        content: contentOne
    }, 
    props: {header: true, content: false} },
  { path: "/two", 
    name: 'two', 
    components: {
        header: Foo, 
        content: contentTwo
    }, 
    props: {header: true, content: false} },
  { path: "/three", 
    name: 'three', 
    components: {
        header: Foo, 
        content: contentThree
    }, 
    props: {header: true, content: false} },
  { path: "/four", 
    name: 'four', 
    components: {
        header: Foo,
        content: contentFour
    }, 
    props: {header: true, content: false} },
  { path: "/five", 
    name: 'five', 
    components: {
        header: Foo, 
        content: contentFive
    }, 
    props: {header: true, content: false} },
] 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我想使用 Foo 作为我的页眉组件，但是我希望我的页眉在每个页面上略有不同。

我希望 Foo 显示不同的标题、不同的文本，甚至不同的可调用方法。

幸运的是，Vue 路由器支持传递路由参数作为道具。这个特性使得为给定的上下文定制任何组件变得非常容易。

## 道具:类型，必选，默认

props 的伟大之处在于它可以很容易地根据需要标记它们，创建默认值和设置类型提示。

在路线之间使用道具时，可以设置所有值、一些值或不设置任何值。

这是一个非常灵活的特性，只要稍加练习就很容易阅读和理解。

## 尽可能使用默认值，或者...

确保在你的模板中使用 v-if / v-show 来避免错误。

默认值可以帮助您避免缺少属性的问题，但是如果没有设置默认值，并且不需要属性，您必须小心避免模板中出现未定义的错误。

你可能意识不到有一个问题，直到你尝试导航到一个你没有设置某个道具的路线。你允许的道具越多，就越容易忽略这个潜在的错误。

## 发现这个有用吗？

一定要订阅 YouTube 频道。我还有更多关于 VueJS、AdonisJS 等的教程。