# 简单的“类名”和小型库

> 原文：<https://dev.to/greduan/simple-classnames-and-small-libraries>

我想分享一下我对像 [`classnames`](https://github.com/JedWatson/classnames) 这样的小库的想法，它们是非常常用的帮手，但经常会被放弃，以换取一个本土的替代方案。

这里有一个很小的`classnames`实现，只有最基本的特性:

```
export default (...classes) => {
  const result = []
  classes.forEach(a => {
    if (typeof a === 'string') {
      result.push(a);
    } else if (Object.prototype.toString.call(a) === '[object Object]') {
      Object.keys(a).forEach(b => a[b] && result.push(b))
    }
  })
  return result.join('  ')
} 
```

Enter fullscreen mode Exit fullscreen mode

这个管用。我测试过了。(著名的遗言。)

为什么我被提示写这个？只是作为一个练习。还因为我清楚地记得对这个库的代码有多长有一个不好的印象，即使我现在检查它，实际上它非常小。

但是写这篇文章可以让我更好地理解，通过把简单的工作交给外部库，我放弃了多少对代码及其行为的控制，这些工作也许可以像上面一样在一分钟内完成。

如果有些东西不工作，我必须检查源代码，但首先我必须仔细检查我使用的是哪个版本，并检查它的源代码，而不是新版本或旧版本，然后我必须阅读它。它在我的`node_modules`中，所以要给它添加一个`console.log`，我必须编辑`node_modules`文件。或者至少我是这么做的。

相比之下，我可以直接去读`utils/classnames.js`文件，很简单，把`debugger`语句或`console.log`或其他什么放进去。简单。

* * *

顺便说一句，除了上面这种奇特的函数，你实际上可以遵循类似下面的模式:

```
const trimClassName = (class) => class.replace(/\s{2,}/g, '  ');

const Foo = () => (
  <div
    className={trimClassName(`
      xyz ${someTruth() ? 'truth' : 'notruth'}  ${someFalsety() ? 'false' : ''} `)}
  >
    Hello world
  </div> ); 
```

Enter fullscreen mode Exit fullscreen mode

所以我完全跳过了一个复杂的函数，我只是在一个使用内置 JS 字符串模板的字符串上，搜索并替换了两个或更多的空格，用一个空格替换它们。

或者另一种方法:

```
const cx = (...classes) => classes.filter(a => !!a).join('  ')

const Foo = () => (
  <div
    className={cx(
      'xyz',
      someTruth() ? 'truth' : 'notruth',
      someFalsety() && 'false'
    )}
  >
    Hello world
  </div> ); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

现在说了这么多。我意识到自主开发的选项并不总是性能最好的选项，但是我更喜欢将微优化留给引擎，我喜欢从事宏优化。

让我知道你的想法，通常我交谈的人更喜欢使用库，因为“有人已经写了，没有必要自己写”，但在某些地方我不同意这一点。