# 好的评论解释为什么，而不是什么，还有 3 条写好评论的规则

> 原文：<https://dev.to/andreasklinger/comments-explain-why-not-what-and-2-more-rules-on-writing-good-comments>

评论…我知道没错…真是🔥要讨论的主题。我希望服务器可以处理我们的兴奋…

但玩笑归玩笑。如果你每天都与代码打交道，那么好的注释对你在任何大中型代码库中的生产力都是必不可少的。呃。多扫兴啊…

让我们言归正传:对于一个好的代码库来说，注释并不是额外的东西。它们是代码库。

这里有一些小技巧，可以让你写出更好的评论…

# #1 你的评论应该解释为什么而不是什么

> “不需要评论。好的代码应该自己解释它是做什么的。”

如果你这样想，你就误解了评论应该做什么。

注释不应该解释代码做什么。代码本身应该。评论应该解释它为什么存在，为什么以这种方式存在

#### 为什么 vs 什么:

*   代码应该解释*什么*
*   注释应该解释*为什么*这个代码存在 ***或者*** 为什么它必须以一种不明显的方式做一些事情。

坏:

```
class Newsletter
  # removes stuff from newsletter
  def remove(stuff)
  …
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

好:

```
class Newsletter
  # Note(andreasklinger): In case we run into XYZ we need to remove the user to avoid
  #   problems w/ ABC and DEFG's API. Read more here: https://.....
  def remove(stuff)
  …
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

# #2 留下你的名字

在 [Product Hunt](https://www.producthunt.com) 我们有一个习惯，总是在评论旁边留下一个名字。

例:

```
Note(andreasklinger): This is a comment. 
```

Enter fullscreen mode Exit fullscreen mode

> 但是你可以用 git 责备它。

人们会移动代码，人们会编辑你的代码行(有人删除了尾随空格吗？).一旦代码被频繁地移动或编辑，没人会费心去找作者。

尤其是在较小的团队中，仅仅是名字就能帮助你理解某些东西为什么存在，或者你是否应该尝试修复/改进它。

# #3 要彻底

“下一个开发者接手”几乎是一个神话。

通常“下一个开发人员”只是“匆匆路过”,有她自己想完成的事情，而你的代码妨碍了她。

想象下一个进入你的代码的人没有任何上下文，并且只有非常有限的时间去修改它。

当你写代码的时候，它对你来说是显而易见的——再也不会那么明显了——对任何人来说都是如此，包括你自己。

*   解释时不要假设人们已经知道你的系统是如何工作的
*   解释你必须考虑的外部系统的怪癖
*   解释你必须考虑的内部系统的怪癖
*   解释系统的哪些部分是遗留的
*   解释何时可以删除(例如遗留)代码(尽可能留下“X 完成后”或日期)
*   解释你需要做的事情，以及为什么它们有效
*   解释不明确的内部相互依赖关系(例如依赖于结构或 api 的其他系统)
*   需要的时候写一个更长的段落是可以的
*   如果可以的话，不要使用外部文档。如果它不在同一个文件中，人们不会更新
*   要求“留下一个 BFN”(留下一个大大的 f***注释)——也期望你的同事在任何需要的时候给任何代码留下注释

# #4 避免杂乱

但是当显式变得多余时，你要避免显式。

写下你的评论。当你写下评论时，再读一遍，看看你能删除或简化什么。

> 如果不能简单的解释，说明你理解的不够好。
> 
> *   [人名]阿尔伯特·爱因斯坦(犹太裔理论物理学家)

#### 希望有所帮助！

对于一个好的代码库来说，注释并不是额外的。它们是代码库。代码使复杂的系统易于机器管理，而注释使它们易于人类管理。

欢迎随时在推特上联系我！

[@andreasklinger](https://www.twitter.com/andreasklinger)