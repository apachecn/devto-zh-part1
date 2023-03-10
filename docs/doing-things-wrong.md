# 做错事

> 原文：<https://dev.to/philnash/doing-things-wrong>

上周我写了一篇博文，有些地方是错误的。这不是撤回那篇博文，我只是想分享我得到的反馈，我改变的事情和我学到的一些教训。

## 前后矛盾糟透了

我收到的第一个反馈来自于[在/r/javascript](https://www.reddit.com/r/javascript/comments/5szzfd/es2015_destructuring_promises_and_beer/) 上发布的文章。评论现在被删除了，所以我不能相信作者，但他们指出了一些事情:

*   我的两个例子之间的代码风格是不同的
*   两个分号不见了
*   最好将`.then`放在下一行并缩进来格式化承诺链

我知道这两个片段的风格不同，因为其中一个是一年前为圣诞节博客的 [12 Devs 写的，我选择保留原始代码。但是，在一个项目中混合不同的代码风格是不可原谅的，那么为什么一篇博客文章会有所不同呢？](http://12devsofxmas.co.uk/2016/01/day-9-service-worker-santas-little-performance-helper/)

一旦有人向我指出，格式问题也很明显。所以，我回到帖子上，改进了[承诺格式](https://github.com/philnash/philna.sh/commit/9bd9af741934c5903d300c70e483d22f6de767cc)，并修正了[分号和代码样式](https://github.com/philnash/philna.sh/commit/707e0f9fe7724be999c3b381d34636042bc196ae)。

## 过有为欠兑现

第二条反馈来自推特，首先是马斯·菲耶罗的直接消息，然后是 T2 的詹姆斯·迪戈伊亚的提及。我对自己的承诺过于偏执，写了一两个太多的承诺。我只需要返回最终数据，而不是解决另一个承诺。

```
 const punkAPIPromise = fetch(punkAPIUrl)
     .then(res => res.json())
-    .then(data => Promise.resolve(data[0])); +    .then(data => data[0]); 
```

Enter fullscreen mode Exit fullscreen mode

多亏了马斯和詹姆斯，我也解决了这个问题。

## 无障碍事宜

最后要指出的一点，可能也是最重要的一点，不是博文的内容，而是我的网站的总体可访问性。夏洛特·斯宾塞指出我的网站没有通过代码样本和网站链接的色彩对比测试。他们提到的测试是关于颜色对比的[网页内容可访问性指南](https://www.w3.org/TR/UNDERSTANDING-WCAG20/visual-audio-contrast-contrast.html)，该指南建议常规正文文本的对比度至少为 4.5:1。为了解决这个问题，我学到了一些有趣的东西:

*   由[汗学院](https://www.khanacademy.org/)开发的 tota11y ，是一个非常棒的工具
    *   我是从夏洛特分享给我的链接中得知的
    *   它建议使用与现有颜色相似的颜色来通过测试
    *   它也有其他插件，可以可视化诸如乱序的标题用法、糟糕的链接文本、缺失的 alt 属性等等，帮助你确保你的网站尽可能的易访问
*   语法突出主题在可及的对比度水平上确实很糟糕
    *   我使用的是一个版本的[日晒黑暗](http://ethanschoonover.com/solarized)在许多地方失败了
    *   我找不到任何语法突出主题，对开箱即用的所有元素都有体面的对比
    *   在你的编辑器中使用语法高亮是一回事，你可以使用任何你觉得舒服的对比度，但是在网上任何人都可以阅读它

最终，我选择使用 tota11y 的建议来修正曝光黑暗中对比度太低的四种颜色。我还修改了链接文本的颜色。

我对此感到有点尴尬，因为，正如我在一条推文中所说的，我认为我曾经很擅长这类事情。以至于大约 10 年前，我写了一篇关于网页可访问性中[肤色的博文。这表明，在 web 开发中，我们不仅每天都在学习新的东西，保留旧的东西也同样重要。](http://www.unintentionallyblank.co.uk/2007/09/27/web-accessibility-colour/)

## 永远在进步

我要感谢马斯、詹姆斯、夏洛特和现在神秘的 Redditor 给了我反馈，帮助我改进了我的博客文章和 T2 的网站。如果你在这个网站上看到任何可以改进的地方，我欢迎你的反馈。我认识到我应该关注我的代码的格式和一致性，不要承诺太多，要更加关注可访问性问题。

总的来说，我认识到为了不断改进——我的代码、我的网站、我自己——有时我需要做错事。

* * *

*[做错事](https://philna.sh/blog/2017/02/13/doing-things-wrong/)原载于[philna . sh](https://philna.sh)2017 . 2 . 13。*