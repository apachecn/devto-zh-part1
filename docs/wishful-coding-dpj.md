# 一厢情愿编码

> 原文：<https://dev.to/rpalo/wishful-coding-dpj>

至少据我所知，封面图片归功于[勒内·德·鲁伊特](https://hatrabbits.com/author/rene/)。

我一直在做 2017 年降临码的[降临挑战这个降临，我注意到随着我一直在做一些更难的挑战(数字螺旋，阿米利特？)我已经开始做一些事情，这些事情似乎真的帮助我锁定了这个问题。这肯定不是什么开创性的东西，但我想我会分享它，以防它能帮助别人。](https://github.com/rpalo/advent-of-code-2017)

## 问题:注意力持续时间短-

如果这种情况看起来像人身攻击，请阻止我:

你刚刚接到一个问题/任务。您知道需要从哪里开始，但是当您开始键入代码的第一个大纲时，您接下来需要做的事情之一会突然出现在您的脑海中。你迅速跳过去，为它创建一个新文件，试着写下一个简短的草图，这样你以后就不会忘记了。当你这样做的时候，你意识到你正在添加的新东西需要一个测试用例，所以你跳过去“很快”记下你正在做的测试用例。您运行您的测试以确保一切都如预期的那样失败，但是您忘记了配置您的测试，并且输出都是丑陋的，谁能在那种环境中继续工作呢？你毕竟不是一个野蛮人！然后两个小时后…等一下，我最初在做什么？

有些解决办法就是自律——强迫自己在开始下一件事之前完成手头的工作。然而，我发现这些精神上的自我打断特别成问题的一个地方是当我试图解决一个大问题并且不得不一直担心解决方案的小部分的时候。这就是我的技巧派上用场的地方。

## 一解:用你的方法*祝你过得*

这可能最好用一个例子来描述。

假设您正在编写一个脚本，用于控制为您的狗自动分配零食。

[![My dog Willy](img/0e81e36883324f336546100d3969193a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9G-tn4vy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assertnotmagic.com/img/wishful-willy.jpg)

这是威利。他是个非常好的男孩。

在您第一次浏览代码时，您可能会写出类似这样的内容。

```
def train(dogs)
  dogs.each do |dog|
    if dog. # Pause here 
```

Enter fullscreen mode Exit fullscreen mode

现在你可能会想，“开枪！现在我需要弄清楚这些狗什么时候应该得到款待！”而就这样，你对其余高级功能的思路就没了。

这是我的解决方案。

我建议你自信地继续下去，就像什么都没有错一样，并且使用你希望将来会写的方法。

```
def train(dogs)
  dogs.each do |dog|
    if dog.good_boy?
      dog.pat!
      give_treat(dog)
      @treats -= 1
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*“可是瑞恩！我们还没有编写像`good_boy?`、`pat!`或`give_treat`这样的方法。”*完全正确。这是未来美国的问题。现在最重要的是让你保持流畅的状态，确保在你忘记你应该做什么之前，把你的全部想法都显示在屏幕上。我们稍后会回来填补这些漏洞。

(对于那些真正关心的人，这里是`good_boy?`是如何实现的):

```
class Dog
  # ...
  def good_boy?
    true
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

😬

## 总结起来

这就是秘诀！写代码，当你遇到一个看起来可能会让你陷入困境的部分**甚至一点点**，就假装你已经有了一个方法。这样做的第二个好处是，它可以帮助您确定将代码放入不同方法的最佳位置。当一切都变得更好的时候，你可以随时回来收拾残局。

有没有其他阻挡干扰的专业建议？告诉我——我很想听听他们的意见！

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2017/12/13/wishful-coding/)*