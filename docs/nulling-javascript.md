# 清空 JavaScript

> 原文：<https://dev.to/kayis/nulling-javascript>

难道你不喜欢 JavaScript 中巨大的对象图吗？比如，当你从 JSON API 中获得一些不错的数据，并且不得不深入研究它时，你只希望它不会回头看？

嗯，通常是这样的，然后事情变得一团糟，你诅咒那个该死的 JavaScript 没有为你提供更好的工具，你忘记了你已经用良好的访问接口建模了那个数据结构。

像你们大多数懒汉一样，我只是喜欢我的普通旧 JavaScript 对象，如果我想让这种语言帮助我，我会使用静态类型的对象，对吗？；)

很长时间以来，解决方案(除了真正建模你的数据)是 looong 检查或第三方库。

```
 let myValue = "default";
    if (response && response.body && response.body.somethingElse) {
      myValue = response.body.somethingElse.myValue;
    }

    import _ from "lodash";

    const myValue = _.get(response, "body.somethingElse.myValue", "default"); 
```

Enter fullscreen mode Exit fullscreen mode

但是，不用再担心我的 JavaScripters 伙伴了，因为我们很快就会构思出臭名昭著的**空传播运算符**！

它是做什么的？！

它让您可以访问数据的深度，而不用担心后端开发人员再次改变结构时一切都会崩溃。

```
 const myValue = response?.body?.somethingElse?.myValue || "default"; 
```

Enter fullscreen mode Exit fullscreen mode

它甚至可以处理数组或函数

```
 const someValueFarAway = myArray?.[993];

    const calculatedIfAvailable = myFunc?.(10); 
```

Enter fullscreen mode Exit fullscreen mode

但是我们什么时候会有它？！

我不知道...

目前这个提议似乎处于第二阶段，一个巴别塔插件正在开发中。

现在的问题是，这是来自 JavaScript 神的祝福还是诅咒？

它会让代码更健壮，更少的思考吗？还是所有的无声错误会累积成我们屁股下的无声定时炸弹？

像往常一样，我只是不知道...