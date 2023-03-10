# 定制 Lorem Ipsum 生成器

> 原文：<https://dev.to/maxwell_dev/making-a-custom-lorem-ipsum-generator>

我的第一个官方应用想法以一种奇怪的方式出现。我需要一个 web 项目的占位符文本，我想，“外面有很多奇怪的 Lorem Ipsum makers。有没有我最喜欢的电子游戏？”那个游戏是王牌律师特许经营权。不，没有。暗示个人愤怒。

快进到今天，王牌律师 Ipsum 作为一个简单的节点应用程序部署在 Heroku 上，现在可供所有人使用。

它没有太多复杂的代码，因为不出所料，吐出随机文本并不困难。它也不是完美的，因为我仍然在建立我的中级 JavaScript 技能。但是我想我会分解我制作这个定制 Lorem Ipsum 生成器的步骤，因为这是一个简单而有趣的挑战。希望看到这篇文章的人也能把他们最喜欢的特许经营权的占位符文本公之于众！

### 制作一个所有文本的数组

第一步很明显:我需要引用。很多引用。就我而言，我很幸运，找到了当时所有发布的王牌律师游戏的 [WikiQuote 页面](https://en.wikiquote.org/wiki/Ace_Attorney)。这个页面正是我所需要的——我只需要复制 HTML，删除标签，将它们放入 JS 数组，清除标点符号，就完成了。我的最终结果是这样的:

```
const lines = [ 
    "Random sentences go here, one at a time!",
    "Here's another one! Just keep adding them on.",
    "Look, here's another one.",
    "And yet another. Along with dozens of others."

    // ...lots of other lines here
]; 
```

Enter fullscreen mode Exit fullscreen mode

线越多越好，我的至少有 1300。很明显这是多余的，100 行可能是更安全的最小值。你也许可以少用一些，因为人们可能不会在意一些重复的台词。

### 得到一条随机线

随着时间的推移，我发现把一个编码问题分解成更小的步骤，然后一步一步地编码，我会做得更好。因此，我从创建 Lorem Ipsum 段落的最终目标开始倒退。为此我需要写一段话。为此，我需要一个随机的文本。

我从从数组中获取一行开始。ES6 的功能很简单，适合一行:

```
const random_quote = () => lines[Math.floor(Math.random() * lines.length)] 
```

Enter fullscreen mode Exit fullscreen mode

### 随意做一段

令人惊讶的是，现在使用`random_quote()`给了我一个随机引用！现在我需要这样做几次来写一个段落。

这个函数只是稍微难一点。唯一的区别是设置行数。我想要一些长短段落混合的变化。所以这个函数得到 3-6 个随机的引号，并把它们放在一起，就像这样:

```
const random_paragraph = () => {
  let
    num = Math.floor(Math.random() * (6 - 3 + 1) + 3),
    lines = ''
  ;

  for (var i = 0; i < num; i++) { lines += (random_quote() + ' '); }

  return lines;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 制作大量随机文本

现在`random_paragraph()`制作一个占位符段落。最后一项任务是一次生产很多。

问题是我想让用户决定有多少段落，所以这个函数为此接受一个参数。另外，这是应用程序使用的最终功能，所以我导出了它。

```
exports = function(num) {
  let ipsum = [];

  while ( ipsum.length < num ) { ipsum.push(random_paragraph()); }

  return ipsum;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，它可以根据用户的要求生成任意数量的 Lorem Ipsum 段落！下面是它在应用程序中的用法，使用 Express 进行路由。当我写这篇文章时，当前版本使用`require()`而不是`import`，但是我已经更新了它，因为它更符合 ES6。

```
import {express} from 'express';
import {ipsum} from './_javascript/ipsum';

let router = express.Router();

router.get("/", function(request, response) {

  response.render("index", {
    title: "Ace Attorney Ipsum",
    ipsum: ipsum(3)
  });
});

router.post("/", function(request, response) {

  let paragraphs = request.body.paragraphs

  response.render("feed", {
    title: "Take That!",
    ipsum: ipsum(paragraphs)
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我使用这个`ipsum`函数两次。第一个示例在主页上创建了三个占位符段落。第二个创建了用户从主页上要求的任何数量。输出是一个没有`<p>`标签的数组，所以模板在遍历`ipsum`变量时会添加这些标签。

### 保持模块化的方法

功能完成后，我决定不将这三个功能以可维护性的名义合并成一个可导出的功能。对我来说，三个简单的功能总是比一个单一的、狭窄的功能更容易理解。此外，如果我想在其他地方使用这些配件，它们都是独立的，便于携带。从长远来看，代码更具可读性和灵活性。

尽管我很喜欢用这种方法编写代码，但我仍然鼓励其他人寻找不同的方法！编码中我最喜欢的部分之一是同一个问题有近乎无限的解决方案。Lorem Ipsum generators 是其中一个很棒的项目，它很简单，但允许创造性。

因此，选择你自己最喜欢的视频游戏或电视专营权，找到一些报价，并给它一个尝试！我相信你的粉丝们也会喜欢的。