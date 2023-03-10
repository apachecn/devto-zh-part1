# 挖掘古老的宝石

> 原文：<https://dev.to/yechielk/digging-up-oldgems>

当我刚开始在 Flatiron School 上学时，我们的第一个项目和第一个主要项目是制作一个用户可以使用命令行进行交互的 Ruby gem。

当时这是一项主要任务，它考验了我对面向对象 Ruby 的脆弱知识。我在这篇文章中描述了这种感觉。

这个项目非常简单；我用 Nokogiri 创建了一个交易博客，这样用户不用离开他们的终端就可以看到当天交易的概要(如果你对你的终端如此投入，你会发现这对你有一点点用处:)

当时我觉得这是一个俏皮的把戏，我甚至在 [RubyGems](https://rubygems.org/gems/dansdeals) 上发布了我的宝石供世人欣赏，然后就再也没有碰过它。

最近我注意到，我正在抓取的网站 DansDeals 经历了一次大修，这时候我第一手发现了为什么抓取是不可靠的；当我尝试运行 gem 时，它立即崩溃了。我煞费苦心教 Nokogiri 的 CSS 选择器都没有在新布局中寻找位置，我可怜的 gem 找不到任何交易。

我的第一个冲动是忘掉它；从那以后，我开始着手更大更好的项目，这块宝石甚至不再出现在我的作品集网站上了。当它持续的时候是美好的，但是所有美好的事情都会结束。

但后来我想为什么不去看看呢？会有多难？总有可能新的布局会变成一个 Nokogiri 死亡陷阱，但为什么不至少尝试一下呢？我想我最多给它一个小时，如果到时候还没修好，我会把它埋在我的 GitHub 账户深处。

于是我打开了网站，启动了控制台，不到半小时，dansdeals 2.0 就在 RubyGems 上上线了。

> ![Yechiel K profile image](img/5ecfa2cee1217327ef0ec56f37c09d6a.png)ye chiel K[@ ye chiel K](https://dev.to/yechielk)![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)[@ dans deals](https://twitter.com/DansDeals)升级了自己的网站？是时候升级我的宝石了。
> 2.0 版本上线！
> [rubygems.org/gems/dansdeals](https://t.co/5wAaQ6rGw6)[twitter.com/rubygems/statu…](https://t.co/UkHcbe5Vfe)02:58AM-2017 年 7 月 11 日ruby gems@ ruby gems[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=884607847883051009)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=884607847883051009)1[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=884607847883051009)1

我为什么要这么做？没有人会用这块宝石(包括我)，所以为什么要这么麻烦呢？

> 乔治·马洛里是一名登山者，他因试图攀登世界最高的山峰珠穆朗玛峰而闻名(1924 年，他在第三次尝试中不幸去世，25 年后希拉里和丹增最终成功登顶)。在《纽约时报》的一次采访中，他被问到“你为什么想攀登珠穆朗玛峰？”他的回答非常简单，以至于闻名于世:“因为它就在那里。”

我想我只是想修复我的旧的无用的宝石，因为我可以。

* * *

*这篇文章是从我的博客 [Coding Hassid](https://blog.yechiel.me/finding-old-gems-c937946ae52c)
交叉发布的，你可以在那里阅读更多关于我的编码之旅，或者在 Twitter 上关注我 [@yechielk](https://twitter.com/yechielk)*