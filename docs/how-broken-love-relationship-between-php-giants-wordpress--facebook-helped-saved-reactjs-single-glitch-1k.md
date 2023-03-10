# PHP 巨头 Wordpress 和脸书之间破裂的爱情关系如何帮助拯救 react.js

> 原文：<https://dev.to/lawrence_eagles/how-broken-love-relationship-between-php-giants-wordpress--facebook-helped-saved-reactjs-single-glitch-1k>

[![](img/c03d835503f030248f3014b81137cbd5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4DpgksmC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fjk0qvq2ch753huhyxia.png)

脸书业界领先的 javascript 库 react js 无疑是前端开发领域的游戏改变者。一个突破性的令人敬畏的产品，田径运动夹克，带有无数的创新；想想 JSX，基于组件的方法，虚拟 DOM，使用 react native 的移动应用程序开发，等等。

react 带来的简单性、性能改进和开发人员的好处是如此具有突破性，以至于它的前身已经成为过去，开发人员很乐意忘记。Backbone.js、Knockout.js、Ember.js 之类的已经是老故事了，开发者愿意讲述。

Angular Js 是 react 唯一最大的竞争对手，在 IT 巨头谷歌的支持下，被迫进行彻底的重写，采用基于 react 组件的方法，并模仿 react 的其他几项最佳创新，以保持相关性。
[https://news.ycombinator.com/item?id=8507833](https://news.ycombinator.com/item?id=8507833)

但是尽管有这些 react.js 还是走了其他的世界:
[![](img/40d3219a6008298974b10129e15e2b12.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ys8xRGKG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ogey90hpet6q3ueqqppo.png)

然而，有了所有这些善良的反应；正如人类创造的一切一样，react 也有一个小故障。不要！不是在图书馆里，但是是的！在它的许可中。这似乎是一个主要的关注对象，有时可能会迫使开发人员、初创公司和公司在将该库纳入他们的技术堆栈之前三思而行。
这是因为脸书选择使用 BSD 衍生的许可证，其中包含一些[麻烦的诉讼问题](https://thenextweb.com/dd/2017/09/19/should-developers-be-afraid-of-zuckerbergs-bearing-gifts/)。

我不是法律从业者，但是如果你考虑一下在 facebook 的 react.js 许可上提出的 [github 问题的数量，并在](https://github.com/facebook/react/issues/10191)[的 react 许可](https://code.facebook.com/posts/112130496157735/explaining-react-s-license/)上做一些阅读，再加上开发者对这个问题的强烈反对，我不知道你怎么想，但是我很惊讶！

关于这个问题的一篇好文章:[https://hacker noon . com/face books-BSD-patents-license-and-how-it-affects-you-66088 e 052845](https://hackernoon.com/facebooks-bsd-patents-license-and-how-it-affects-you-66088e052845)

在获得许可后，你必须仔细检查，以确定自己是否会搬起石头砸自己的脚，因为 react.js 正在寻找一种类似于核心腐烂的钻石的东西。如果你遇到许可问题的话，那就是了。但是为什么要冒不必要的风险呢？

尽管面临各种压力，脸书仍然拒绝改变 react.js 上使用的 BSD+Patents 开源许可证。即使是在 apache 这样的公司宣布不允许 Apache 产品包含脸书的 BSD+Patents 开源许可证时也是如此。脸书表现出大无畏精神。

因此，当你读到脸书现在不仅同意重新许可 react.js，还同意重新许可它的其他几个开源产品时，这是一个令人震惊的快乐时刻。即:Jest，Flow，and immutable . js
[https://code . Facebook . com/posts/300798627056246/re licensing-react-Jest-Flow-and-immutable-js/](https://code.facebook.com/posts/300798627056246/relicensing-react-jest-flow-and-immutable-js/)

哇！是什么移动了这座山？

我想不出其他原因，除了宣布 wordpress 退出 react 不仅在 Gutenberg(他们的新项目)上，也在 calypso (wordpress.com)上。
冰壶来自 mat mullenweg 的博客:[https://ma.tt/](https://ma.tt/)

“我很惊讶也很兴奋地看到脸书将放弃我上周写的专利条款的消息。他们已经宣布，React 16 的许可证将只是常规的麻省理工学院，不会增加专利。”

在考虑 react 的许可后还有更多:

> “我在这里说，古腾堡团队将后退一步，使用不同的库重写古腾堡。这可能会使古腾堡推迟至少几周，并可能将发布时间推迟到明年。

> Automattic 还将使用我们为 Gutenberg 选择的任何内容来重写 Calypso——这将需要更长的时间，Automattic 对专利条款仍然没有问题，但与 core 的长期一致性比重写对 Automattic 业务的短期打击更有价值。超过四分之一的网站都有 WordPress 的核心更新，让它们都继承专利条款让我很不舒服。"

对于脸书来说，wordpress 在名为 calypso 的 wordpress.com 仪表板上采用 react.js 是 react.js 的一大妙招。考虑到 wordpress 目前拥有大约 28%的网络份额，以及 wordpress 完全采用 React js & Node.js 的病毒式言论；在开发 calypso 中成功使用之后，开始在 JS 开发人员中流行起来，当使用 wordpress 开发时，他们宁愿使用 twig 来避免编写丑陋的 PHP 语法。

对于脸书来说，这两个 PHP 巨头之间爱情的崩溃是难以承受的。失去 27%的网页和许多其他公司跟随 suite 的可能性，Vue.js 现在正在追赶，我不关心脸书改变授权的原因，我们在这里找到了:-)。

尽管脸书更换许可证的原因是由他们的工程总监 Adam Wolff 给出的，引用他的话:

> “React 是一个广泛的网络开源软件生态系统的基础，我们不想因为非技术原因而阻碍进步。”

我不知道你怎么想，但是作为一个相信是 wordpress 而不是脸书拯救了 PHP 的开发者，我当然相信，wordpress 在他们所有的项目中放弃 react 是脸书改变他们许可的工具。不仅仅是暗示 wordpress，而是每一个反弹和压力，当然，一直到 wordpress 时刻都是有帮助的。然而，我相信 wordpress 的打击只是将压力推过了阈值水平。当然不可撤销的已经撤销了。

祝贺脸书，感谢 wordpress，apache 和所有其他发表意见的开发者。

哇！现在，我们可以使用我们最喜欢的前端开发工具，而不会有丝毫的担心...