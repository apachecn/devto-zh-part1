# JavaScript 的神秘“这”——什么，什么时候，为什么

> 原文：<https://dev.to/ptasker/javascripts-cryptic-this--what-when-and-why-216>

<small>*照片由 Tarun Ram 在 Unsplash* 上拍摄</small>

在 MDN 开始组织他们的 JavaScript 文档之前，寻找 JavaScript 问题的答案经常会使您陷入堆栈溢出。

好吧，这些天 MDN 已经基本上摒弃了这种做法，除非你正在寻找 JavaScript 的`this`关键字用法的答案。

[文档很棒](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)，真的很棒，但是它并没有充满有用的、真实的例子。所以我想我应该把一些关于这个如此神奇的关键字的提示和技巧放在一起。

### 老学究 JS

> "在我那个时代，我们必须警惕我们的目标！"

好的，所以是的，如果你在你的开发控制台中运行`console.log(this)`，你通常会*看到默认情况下的`this = Window{}`。超级有用...😏*

 *当您在函数
中检查`this`的值时，事情变得更加有趣

```
function mahFunc(){
    console.log(this);
}

mahFunc();
// Window{} 
```

Enter fullscreen mode Exit fullscreen mode

您应该仍然可以看到窗口对象。好了，这里没什么新东西。

但是如果加上`'use strict'`呢？

```
function mahFunc(){
    'use strict'
    console.log(this);
}
// undefined 
```

Enter fullscreen mode Exit fullscreen mode

嗯。

现在好了，但是如果你在窗口 global 上调用`mahFunc()`(因为它是一个全局函数)呢？

```
function mahFunc(){
    'use strict'
    console.log(this);
}

window.mahFunc();
// Window 
```

Enter fullscreen mode Exit fullscreen mode

啊？

严格模式是一种有趣的方式，但是它通常会使错误更加明显，并清理您的 JavaScript。

MDN 文档中没有提到的是，像 Webpack/Browserify 这样的绑定器/加载器/连接器可能默认启用了严格模式。你可能最终会用一些古怪的加载器在你不知道的情况下启用它，因为你所有的脚本都捆绑在一起。

所以，如果你看到你的`this`电话回复了一些有趣的事情，请留心。

## 叫我 plz

好的，所以`this`在全球范围内很奇怪，但是现在谁不使用对象和 ES2015 类呢？如果你想在你的函数中为`this`使用不同的值*(as-in not`undefined`或`Window`)，你可以用 [`.call()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) 和 [`.apply()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 传递一个上下文。我总是用‘yadda-yadda . prototype . call()’来记住这些。* 

```
function mahFunc(){
    console.log(this);
}

const config = {

    stepOne(){
        //do step one
    },

    stepTwo(){
        //do step 2
    }
}

mahFunc.call(config);

//{stepOne: ƒ, stepTwo: ƒ} 
```

Enter fullscreen mode Exit fullscreen mode

给你。`this`引用在参数中传递给`.call()`的对象。酷吧？

这样你就可以为一个函数指定一个上下文。它非常方便，也是很多框架和捆绑包在内部使用的——看看你的 Webpack 捆绑包吧！

我不会一一列举`this`所有可能的情况/用途，有很多，而且 [MDN doc](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this) 真的很好。

记住`this`很重要🙄。

帖子 [JavaScript 的神秘“这个”——什么，什么时候，为什么](http://petetasker.com/javascripts-cryptic-this-what-when-and-why/)首先出现在[上🔥数据库关键🔥](http://petetasker.com)。*