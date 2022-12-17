# 向 WordPress.org 提交第一个主题《卡法尔》的体会

> 原文：<https://dev.to/shubham9411/my-experience-on-submitting-first-theme--kafal-to-wordpressorg--2bce>

帖子[我向 WordPress.org 提交第一个主题- Kafal 的经历](https://shubhampandey.in/experience-submitting-first-theme-kafal-wordpress-org/)首先出现在 [Shubham Pandey 的博客](https://shubhampandey.in)上。

二月份我写了一篇关于[开源和 WordPress 主题](https://shubhampandey.in/open-source-and-wordpress-theme/)的博客，并决定向 WordPress.org[提交一个主题](http://wordpress.org/)。现在我带着另一个关于我的 WordPress 主题 Kafal 的博客回来了。很高兴通知你所有的读者，我的主题通过了审查过程，并在 WordPress.org 仓库接受。主题' [Kafal](https://wordpress.org/themes/kafal/) '现已上线，已被下载 1500 多次，目前活跃在 200 多个博客/网站上。对此我非常高兴，每天我至少查看 5 次 WP.org 的主题细节。

这一切都是从二月份开始的，当时我除了学业之外没有太多工作。那时我渴望得到这份工作。然后经过大量的研究，我发表了关于[开源和做主题](https://shubhampandey.in/open-source-and-wordpress-theme/)的博客。那时候我已经是 WordPress slack 的一员了。我认为[松驰](https://make.wordpress.org/chat/)是开始你旅程的最佳地点。在这个博客中，我将分享我的经历，我在这期间学到了什么。

* * *

### 编码改进

我从`<!Doctype html>`开始了我的 HTML 职业生涯，这意味着从 HTML5 开始，但从来不明白 HTML 4 和 HTML5 之间的[区别。在这个主题中，我使用了 HTML5 中的新语义元素，如`header`、`header`、`footer`、`article`、`main`、`aside`等。](https://stackoverflow.com/questions/134727/whats-the-key-difference-between-html-4-and-html-5#answer-577778)

WordPress 提供了许多 API，我已经使用了其中的许多。这次对我来说新的是[主题定制 API](https://codex.wordpress.org/Theme_Customization_API) 。增加了自定义选项的实时预览选项，如“标题颜色”和“博客标题&标语”等。对于为主题用户设计好的 UX 来说，这是一个很酷的 API。

以前在开发一个主题的时候，我从来没有为使用转义和净化函数而烦恼过。如果你检查我第二年创作的第一个主题[‘让我好奇’](https://github.com/shubham9411/Makes-me-wonder)，我从来没有使用过任何转义函数。或者如果我使用了它，那么它一定是我从 [SO](https://stackoverflow.com/) 或 [codex](https://codex.wordpress.org/) 复制的代码。但是现在，每当代码涉及到数据库或用户输入时，我首先想到的是数据的转义和净化。编写安全性较差的糟糕代码可能会导致网站受到 XSS 攻击。

我学到了更多关于编码风格的东西，现在我记不起来了，但我肯定会在将来分享它们。

### 主题定制 API 和 Bootswatch 主题

创建了名为“[孤独](https://github.com/shubham9411/solitude)”的主题库并开始工作。我希望它是同类中最好的。并开始设计主题。我是 [Bootswatch](https://bootswatch.com/) 的忠实粉丝，因为它们为 Bootstrap 提供了很酷的主题。我想创建一个主题，你可以用这些[靴子样本](https://bootswatch.com/)主题改变主题变体。于是我下载了不同的 Bootswatch 主题，用[主题定制 API](https://codex.wordpress.org/Theme_Customization_API) 集成到主题中。你可以用不同的 bootstrap 来改变你的 Bootstrap 主题。但由于缺乏设计技巧和其他工作，我停止了主题'孤独'的工作。这个主题有更多的选择，但它的设计滞后，并有一个糟糕的编码风格。也许将来我会从事这方面的工作。

### 使用 _s(下划线)

在《孤独》之后，过了两个月，我又开始了这个主题的创作。这次带着更多的热情，更多的精力。但是当涉及到仅仅为了搭建而再次编写相同的代码时，这对我来说是一个沉重的任务。所以这一次我做的是，我分叉' [_s(下划线)【T1]'这是 Automattic 为黑客创建的起始主题。创建了自己的分支，开始黑主题。](https://github.com/automattic/_s)

顺便说一句，如果你刚刚开始使用 WordPress 并且不知道基本知识，我不会更喜欢使用 _s。

### 使用基本测试方法&构建工具

我写了一篇关于整合 Travis CI 和 WordPress 主题的博客。使用构建工具和 CI(持续集成)对于项目来说是必须的，因为一个人为的错误可能会是一个巨大的问题。所以我们教机器检查我们的代码，并警告我们的错误。我已经用 Travis CI 测试了我的主题，看它是否通过了 WordPress 编码标准。还有一些插件可以用 WordPress 编码标准测试你的主题，比如“[主题检查](https://wordpress.org/plugins/theme-check/)”。这些工具将有助于用编码标准制作一个好的主题。

### 各种开源许可

当我在 6 月 19 日第一次提交我的主题( [Trac ticket](https://themes.trac.wordpress.org/ticket/44006) )时，门票在 6 月 21 日关闭了两天。这是由[主题评论团队](https://make.wordpress.org/themes/handbook/about/members/)负责人 [@poena](https://profiles.wordpress.org/poena) 之一的快速评论。主题被拒绝，因为有超过 5 个不同的错误。第一个也是最重要的错误是声明主题的许可。

我意识到，上传到 WordPress.org 的主题应该有一个 GPLv3 或兼容的开源许可证。但是我还没有申报。我所说的主题也指主题中使用的资产，如图像、库等。你可以查看[票](https://themes.trac.wordpress.org/ticket/44006)上的对话，查看她提供的所有关于主题开发的链接。

要了解有关许可证的信息，choosealicense.com 是一个很好的来源。

### 设定现实的截止日期

没有截止日期，一个副业可能会成为垃圾，因为它永远不会完成。我学到的是，设定一个现实的截止日期可以提高工作效率。不管是我们的作业，项目报告等等。甚至我们在考试前才开始学习。走出舒适区，做你喜欢的项目&停止拖延。我也为这个主题设定了一个截止日期，是的，因为这个原因，我在 git 中有一个关于“黑客马拉松”的承诺(检查日期)！😛

[![Hackathon for Kafal](img/886baf0c6a3ea7064759db46e1fc6c6e.png)T2】](https://i2.wp.com/shubhampandey.in/wp-content/uploads/2017/09/Screenshot-from-2017-09-01-03-08-06.png)

Kafal 的黑客马拉松

### 大家想帮你尽管开口

我不是 WordPress Slack 的活跃成员，但我经常打开它，阅读发生的对话。每次打开都能学到新东西。志愿者总是试图给你正确的信息，即使你问一些愚蠢的问题。

WordPress 是一个社区，有很多善良的人总是愿意分享他们的知识。如果你有任何问题，请提出来，你一定会得到答案。想要试玩，查一下 trac 票 [44111](https://themes.trac.wordpress.org/ticket/44111) 和 [44006](https://themes.trac.wordpress.org/ticket/44006) 。！😉

### 耐心

是的，你没看错耐心。耐心在第二次提交我的主题后，一张新票被分配给了我。沉默 2 个月。在我的主题被放在评论的首位之前，什么都没发生。在评论队列中看到你的主题需要很大的耐心。

最终在 8 月 8 日，@kafleg 作为评审被分配到我的主题。这次它(主题)有 3-4 个小问题。所以我完成了他们的工作，并更新了新的变化。之后我就在等审稿人关于主题的另一个回复。这次我的回答是这样的:

[![Theme set to live](img/7d8640b5be55425578783d5c5421a5c1.png)T2】](https://i2.wp.com/shubhampandey.in/wp-content/uploads/2017/09/Screenshot-from-2017-09-01-03-24-12.png)

主题设置为直播

那是一个‘啊哈’的时刻，我想告诉每个人我的主题被选中了，看看链接和电子邮件。我很高兴在 WP.org 看到我的主题。

> 河流知道这一点:没有匆忙。总有一天我们会到达那里。"
> 
> –[艾拉·米尔恩](https://www.goodreads.com/author/show/81466.A_A_Milne)、[小熊维尼](https://www.goodreads.com/work/quotes/1225592)

当你想到很多用户在使用你的主题时，这听起来很棒。我们写的这段代码是有价值的。回馈社区，这是开源的全部意义吗？

* * *

现在开心的是，在我的个人资料中看到主题开发者的徽章。

[![Profile Shubham Pandey WordPress](img/96ef1fb034ae03d9319e8693e2091d91.png)T2】](https://i1.wp.com/shubhampandey.in/wp-content/uploads/2017/09/Screenshot-from-2017-09-01-03-37-58.png)

简介 Shubham Pandey WordPress

对此有什么看法，请在评论区告诉我。

谢谢！