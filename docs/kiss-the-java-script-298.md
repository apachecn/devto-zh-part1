# 亲吻 JavaScript

> 原文：<https://dev.to/nijeesh4all/kiss-the-java-script-298>

```
 " KEEP IT STUPID SIMPLE " 
```

Enter fullscreen mode Exit fullscreen mode

我曾经看到这句话，说“当我能写一些很棒的代码时，我为什么要保持简单呢，毕竟这是我的代码，我在任何地方都有注释，所以我以后理解它没有问题”

所以作为一个程序员，请想一想你在编写代码时采取的最佳实践是什么。Is 是时不时评论一下？，把它拆分成模块？，或者可能是减少重复。

每个人都以自己的方式编写代码，但是有一种方法可以适用于任何方式，那就是“保持简单愚蠢”。

你可以从同一句话中得到两种意思

1.  简单点，笨蛋
2.  保持简单的愚蠢

我会选择第二个，因为我不喜欢任何人说我愚蠢。

我会说你为什么要接吻(听起来有点别扭但是听起来很搞笑)。你可能是爱因斯坦，你可以编写只有你能读懂的代码。但是我们的思维方式真的很有趣，当你写代码的时候，你可能处于爱因斯坦模式，当你必须在几天后，可能是几个月或几年后重新编写代码时，你的思维将处于特朗普状态。因此，不去修改你写的代码，你会浪费很多时间去理解你写的东西。).

你可能认为我写的所有代码都是愚蠢的，我没有足够的智慧去写复杂的代码。让我告诉你为什么可能不是这样。对我来说，一个令人敬畏的代码是一个即使是哑巴也能理解的东西(足够聪明去阅读代码)。讽刺的是，保持事情的简单并不是那么简单。

依我看，写代码是为了你自己，而不是为了让别人理解。

谈到 JS，事情很快就会变得非常糟糕。如果你用 JS 编码过一段时间，你应该听说过叫做**回调地狱**的东西。如果你不知道这是回调地狱的定义

看过电影《盗梦空间》吗？这部电影讲述了梦里梦里的梦，每一层都有自己的范围，但会影响前一层的梦-如果有一层的话。最糟糕的情况是，如果你迷路了，你就不知道自己在哪里，从哪里来。

这是一个从[回调地狱](http://callbackhell.com/)
回调地狱的例子

```
 fs.readdir(source, function (err, files) {
  if (err) {
    console.log('Error finding files: ' + err)
  } else {
    files.forEach(function (filename, fileIndex) {
      console.log(filename)
      gm(source + filename).size(function (err, values) {
        if (err) {
          console.log('Error identifying file size: ' + err)
        } else {
          console.log(filename + ' : ' + values)
          aspect = (values.width / values.height)
          widths.forEach(function (width, widthIndex) {
            height = Math.round(width / aspect)
            console.log('resizing ' + filename + 'to ' + height + 'x' + height)
            this.resize(width, height).write(dest + 'w' + width + '_' + filename, function(err) {
              if (err) console.log('Error writing file: ' + err)
            })
          }.bind(this))
        }
      })
    })
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里的代码看起来很乱，可以通过使用承诺来避免。

使用承诺会让你的代码更容易阅读和维护。看到组织得整整齐齐的代码，你会感到更加愉快。

```
 awesome_function()
    .then(do_this)
    .then(do_this_too)
    .then(here_is_one_more)
    .error(oh_crap) 
```

Enter fullscreen mode Exit fullscreen mode

看，写有承诺的代码看起来就像同步编程。想象一下这样一个场景，你必须修改别人写的代码，如果它是用第一种方式写的，你会很生气，如果是第二种方式，即使是傻瓜也能理解。

我在这里谈到了召回地狱，在这里我应该谈得更普遍。在我看来，回调是最容易使你的代码混乱的事情，所以重构它们会给你留下一个好的整洁的代码。

快乐编码...

### 链接

1.[吻为编程](https://simpleprogrammer.com/2015/08/19/kiss-one-best-practice-to-rule-them-all/)
2。[回调地狱](http://callbackhell.com/)3
。[承诺](https://scotch.io/tutorials/javascript-promises-for-dummies)
4。[回调](http://javascriptissexy.com/understand-javascript-callback-functions-and-use-them/)
5。[承诺](https://dev.to/maxart2501/gotchas-about-asyncawait-and-promises-9di)