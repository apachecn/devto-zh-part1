# 你在项目中运行/编写过哪些有趣的代码行？

> 原文：<https://dev.to/andy/what-are-some-funny-lines-of-code-youve-runwritten-into-a-project-4e4g>

嗯，现在是美国的假日季节，我的工作效率逐渐下降。我想说这是因为我有家人过来，我最近睡眠不足，但这只是问题的一部分。然而，为了克服我做得少的感觉，我决定做一些更小的任务，清理一些我以前写的代码。

无论如何，我在清理数据库种子文件时遇到了一个小错误——基本上是一个为开发创建虚拟数据的文件——并决定立即修复它。没有比现在更好的时机了，对吧？

为了给出一些背景，我们的标签有两列，`bg_color_hex`和`text_color_hex`。它们应该接受一个十六进制代码，我们适当地限制它为字母数字字符，并且一行中限制 6 个——嗯，这是我认为的，因为它是一个正则表达式验证器，我不想翻译它🙃。

然而，有一种恼人的边缘情况，我们可以输入一个十六进制代码，但在 6 个字符前面不要有井号/hashtag/whatever-people-call-it-now，validator 会抛出一个错误。没有理由不自动化，所以我去了。

当我把方法写出来时，我突然有了一个绝妙的灵感。在我们安静的办公室里，我对自己的极度聪明暗自发笑。

<figure>

[![me-smiling-like-the-grinch](img/0417be59a7c5ce36cf0ddfdda0e55a0b.png)T2】](https://i.giphy.com/media/B5AVgxf0OzlyE/giphy.gif)

<figcaption>More silly and less creepy though.</figcaption>

</figure>

因此，我非常高兴地编写了一个方法，如果需要的话，可以在颜色属性前面加上磅符号:

```
 def pound_it
    text_color_hex&.prepend("#") unless text_color_hex&.starts_with?("#")
    bg_color_hex&.prepend("#") unless bg_color_hex&.starts_with?("#")
  end 
```

Enter fullscreen mode Exit fullscreen mode

“Pound it”是美国俚语，指拳头碰撞。撞拳就像击掌，除了用你的拳头。理想情况下，还有他们的拳头。

<figcaption>

现在，代码可能会被重构，但美妙之处当然在于名字。每次标签被验证时都会发生这种情况，所以要知道你每次点击文章的预览或保存按钮时都是在“敲打它”。

希望这篇年终文章能激励你做类似的事情。你遇到过哪些有趣的代码？你有没有在你的代码中写任何东西，并通过你公司所有的繁文缛节？很想听听你的故事！

</figcaption>