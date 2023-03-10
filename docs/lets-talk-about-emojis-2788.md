# 我们来谈谈表情符号

> 原文：<https://dev.to/fbnlsr/lets-talk-about-emojis-2788>

表情符号无处不在。从推特到脸书聊天，它们已经成为牛津 2015 年[年度词汇](http://time.com/4114886/oxford-word-of-the-year-2015-emoji/)，甚至出现在一部[恐怖电影](https://www.rottentomatoes.com/m/the_emoji_movie)中。但是外面的短信和即时通讯呢？在代码注释甚至 git 提交消息中使用表情符号怎么样？让我们来看看如何充分利用这些有趣的小图片。

与人们可能认为的相反，表情符号已经存在了相当长一段时间。第一个表情符号可以追溯到 1999 年，由 Shigetaka Kurita 创造，他是 NTT Docomo 的一名日本电信规划师。最初只在日本使用，这些小图片花了十年时间才被添加到 Unicode 字符空间。因此，2010 年 10 月，Unicode 标准 6.0 发布了，随之发布的还有 722 个表情符号。但是它们并不在自己的专用块中，而是分布在 Unicode 表中。在谷歌和苹果工作的多名工程师花了数年时间才说服 Unicode 技术委员会添加它们。现在表情符号是每个人生活的一部分。

甚至还有一些关于这些小图片的怪癖和有趣的小事实。例如:表情符号可以因平台而异。因此，苹果产品上的日历表情符号总是显示 7 月 17 日(这一天代表了 2002 年 iCal 的发布)。这导致人们“错误地”宣布 7 月 17 日为世界表情符号日。

表情符号在不同的平台上也有不同的表现方式，可以有稍微不同的解释。以`astonished face`表情符号为例。第一个是苹果的解读，第二个是三星的。

[![emoji1](img/4890091c1ddbf997e35c9e7cdfb0ee4a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kc8pXGBD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2aeutf1h2l10l53j1cp9.png)

苹果对这种感觉的接受感觉比三星更驯服，你不觉得吗？

其他时候，情况可能正好相反。在这个例子中，三星对`pouting face`的解释没有 Twitter 的那么“愤怒”。

[![emoji2](img/20ed13f095f5cebbbae35ec120841a2a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5H1IyKNG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iskgv0i4p6qz3j6swm7s.png)

历史已经讲得够多了，让我们开始写代码吧。

## git 提交消息中的表情符号

Github 在 2012 年的一篇[博客中推广了他们生态系统中的表情符号支持，这要归功于他们现在著名的“`:`”快捷方式。现在，假设你想使用`fox face`表情符号🦊在 Github 的某个地方(提交消息、问题或要点)，你可以简单地使用`:fox_face:`来代替，它会自动被 Github 解释。](https://github.com/blog/1289-emoji-autocomplete)

使用快捷方式是避免表情符号不被解释的一个优雅的解决方案。您不必冒险破坏某些东西，即使它们没有(或很差地)呈现出来，消息仍然是可读的。

表情符号还可以增加提交信息的清晰度。比较这两个序列:

```
- Fix editing user not being saved to the database
- Cleanup code
- Add the ability to edit a user
- Fix bad function callback on API request 
```

Enter fullscreen mode Exit fullscreen mode

```
- 🐛 Fix editing user not being saved to the database
- 📝 Cleanup code
- ✨ Add the ability to edit a user
- 🐛 Fix bad function callback on API request 
```

Enter fullscreen mode Exit fullscreen mode

您可以立即看到哪里修复了错误，哪里添加了新功能。

在一个不支持表情符号的平台上，这将被读作:

```
- :bug: Fix editing user not being saved to the database
- :memo: Cleanup code
- :sparkles: Add the ability to edit a user
- :bug: Fix bad function callback on API request 
```

Enter fullscreen mode Exit fullscreen mode

肯定没有那么有趣，但仍然非常可读。

整个科技行业都在使用这些快捷方式，而[远远超越了](https://www.webpagefx.com/tools/emoji-cheat-sheet/)简单的表情符号。当然用起来很好🐛谈论修复一个 bug，但尝试在 Slack 或 Redmine 中使用`:trollface:`。嘣，你成了街区里新的酷小子。但是不要太频繁地使用它，你不想成为*那个*的家伙。

我的建议是:在 git commits 中不要犹豫使用表情符号，但是最好使用短代码。我也建议不要过度使用它，坚持用几个动作的列表来表示主要的动作(错误修复、新特性、样式、代码清理等等)..).

如果你不确定要开始或者想要给你的团队建议一个指导方针，我强烈推荐卡洛斯单面山的 [Gitmoji](https://gitmoji.carloscuesta.me/) 。它甚至附带了一个漂亮的 CLI(简称为 [`gitmoji-cli`](https://github.com/carloscuesta/gitmoji-cli) )，可以帮助你通过一个交互界面编写提交消息。Gitmoji 甚至用在了 Atom 的[贡献指南](https://github.com/atom/atom/blob/master/CONTRIBUTING.md#git-commit-messages)中。

## 表情符号以/为代码

从技术上来说，你可以在计算机代码中使用表情符号，但是你应该非常小心。表情符号在 Javascript 中被解释为字符串，但它们的长度可以变化。

```
"🐼".length         // returns 2
"🇨🇦".length         // returns 4 
```

Enter fullscreen mode Exit fullscreen mode

别忘了表情符号是可以连接的(有点像 Fira Code 给你那些性感的连字)。这就是你现在如何获得肤色修改器(称为`EMOJI MODIFIER FITZPATRICK TYPE-1`、`-2`、`-3`、`-4`、`-5`和`-6`)。我不是开玩笑)。或者更好，如果你结合以下表情符号:👨,👩，以及👧你有一个完整的家庭👨‍👩‍👧！让我们用 Javascript 运行一下。

```
"👨‍👩‍👧".length         // returns 5 
```

Enter fullscreen mode Exit fullscreen mode

为什么是 5？因为你不仅得到了组成那个符号的每个表情符号的长度，而且它还使用了两个`ZWJ`(零宽度 Joiner)字符作为“胶水”。你甚至可以看到它的运行:例如，在 VS 代码中复制/粘贴表情符号，你需要用五个“箭头键”来浏览它。

**我的建议:**不要在代码逻辑中使用表情符号。简单明了。但是您仍然可以在视图中使用它们。网络浏览器拥有惊人的表情符号显示能力，并且知道如何退回到一种字体，让*显示你的“嘭嘭”图标。但是在这些视图中使用表情符号短代码解释器时要小心，尤其是当你碰巧在你的网站上显示代码块的时候。它可能欺骗你，将`h:m:s`解释为`hⓂ️️s`，从而使代码块无用。*

## 代码注释中的表情符号

那么代码注释呢？表情符号无处不在！据我所知，你不容易因为评论中的表情符号而打破任何东西。现代代码编辑器(Atom、VS Code、Sublime、Intellij...)有惊人的表情符号支持。他们甚至可以非常有用，使一些东西脱颖而出。

```
/**
 * WARNING: Do NOT change this file.
 */ 
```

Enter fullscreen mode Exit fullscreen mode

对比:

```
/*
 * 🛑 WARNING: Do NOT change this file.
 */ 
```

Enter fullscreen mode Exit fullscreen mode

## 最后的想法

表情符号是一把双刃剑。它们让我们以一种快速而有趣的方式表达复杂的感情。它们是我们在 IRC 辉煌时期大量使用的表情符号的延伸。它们可以用作装饰，给原本平淡无奇的句子增添感情。它们也可以被用作标记，让一些东西脱颖而出，甚至在单独使用时作为一个完整的交流工具。

然而，由于它们不是跨平台统一设计和解释的，它们可能是误解的来源。交流依赖于其传播方式的稳定性。如果一个符号在发送者和接收者之间改变了，信息就不一样了。作为角色，他们也需要放在上下文中。这就是为什么他们中的一些人必须改变。例如`:gun:`表情符号🔫以前是真枪，现在是水枪。

当谈到代码时，我完全赞成使用表情符号。不在代码本身，正如我所说的，而是在注释和提交消息中。它们嵌入它们所附加的信息，因为它们通常主要用作指针。在短代码的帮助下，你可以使用它们而不用担心会弄坏什么东西。

如果你想了解更多的表情符号，你应该看看莫妮卡·丁库莱斯库的作品，尤其是她的演讲。

我还推荐安吉拉·古兹曼关于苹果表情符号制作的帖子。安吉拉写道，她和她的导师雷蒙德在 2008 年实习期间设计了 500 多个表情符号。这改变了她的生活，她的作品现在掌握在数百万人手中。

所以，继续使用表情符号吧，你会提高可读性，摆脱单调乏味的充满代码的屏幕。😄