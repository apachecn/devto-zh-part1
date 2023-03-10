# 多人游戏？在勒杜姆大冒险 35 号果酱上？哦，真的吗？

> 原文：<https://dev.to/senior_sigan/multiplayer-game-on-the-ludum-dare-35-jam-oh-rly-2gl4>

这是我们的第九次 Ludum Dare，所以我们决定这次做一些有挑战性的东西。我们试图创造多人动作恐怖射击游戏，两个角色试图杀死对方。一个是狼人(近程攻击，视角广)，另一个是猎人(远程攻击，视角小，视距远)。是的，由于某些原因，这的确很有挑战性。

[![](img/7efb5c6a694d9b8924fc3c2678c1bd83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0fauoaEd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ag624l2r8596cancum0v.png)

# 联网

我们为 LD#34 制作了第一个网络游戏。它相当原始，是两个玩家的点对点游戏。但它证明了我们的团队可以处理网络游戏特有的问题，并使网络游戏至少可以玩。这一次我们决定更进一步，创建一个将所有游戏实例连接成对的服务器。这仍然是相当简单的客户端-服务器架构，但是它需要大量的时间来实现，并且需要一些 websockets 魔法的知识。

[![](img/ec6a29fa4fa8632562091e47933fb4ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TrTN08c2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jmfqgwma6k1jhibzg5pz.gif)

# 动态阴影

我们的游戏基于非常简单的 LibGDX 引擎，当然，没有任何内置的阴影支持。所以，阴影是用一堆黑色三角形、几十个数学公式和按距离从远到近对障碍物进行分类制作的。

[![](img/e7bfa570a7bb6f90388cece0163548bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wv22oTef--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fc21bm8gb6sq6cb1pa32.png)

# 碰撞检测

三角魔法字符不应该穿过树，对吗？LibGDX 有内置的 box2d 引擎支持，但是对于这样简单的任务，它给代码增加了太多的开销，所以我们决定自己做所有的事情。这也需要一些数学知识，有时会导致有趣的虫子，比如让你困在树里并把你撕碎的树。

[![](img/ba9f1e282efa2cf075fd8fb01f695e23.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yuy56k53--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wbat2vb8o2g8y99o4sx7.jpeg)

# 柔和的火炬光束效果

动态阴影看起来很简单。但在里面，它带有一些 OpenGL 顶点和片段着色器的魔力。我们中的一个人花了 5 个小时左右的时间让这个效果看起来合适并且可以配置。这种柔光火炬效果也可以用渐变透明位图来实现，但是制作起来没那么有挑战性，也没那么有趣。

[![](img/cafbb19f91c49b87b9b584d32d5dc9fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Zkb2XE8V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ahpcg84vfzxsfzkz7wi.gif)

# 平衡和小细节

在基本的游戏机制完成后，我们决定增加一些新的功能来修正游戏的平衡，让游戏更有趣。首先，我们让一只狼跳过树来补偿猎人在攻击范围上的优势，然后我们给我们的角色添加了一些痕迹来帮助他们在黑暗的森林中找到彼此。为了让游戏更加平衡，我们增加了角色走路的声音，这样玩家可以听到彼此走路的声音。这也让一款游戏更加大气。

[![](img/b6cc1a797f283f541f10d3aa3184ce6a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vpkWSQyl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/27jo9zq7p9as61itbdhj.jpeg)

毕竟，我们对结果非常满意，我们希望你能喜欢玩这个游戏。有时很难找到对手，所以准备好抓住你的朋友，通过互联网和他一起玩。

我们也有一个游戏统计和电报聊天的页面，给那些想收到关于活跃玩家等待对手的通知的人。

[![](img/ebfde5b6c9d48613cb5983fc4ef89920.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zSYYCrk2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bipv1x0897exsz3j3nu0.png)

您可以从[这里](https://catinthedark.itch.io/za-bochok)下载并玩我们的游戏。

游戏视频:

[https://www.youtube.com/embed/X_CfocY6BvQ](https://www.youtube.com/embed/X_CfocY6BvQ)

此外，我们还有一个发展时间间隔。

[https://www.youtube.com/embed/qkEgv-Azkxk](https://www.youtube.com/embed/qkEgv-Azkxk)

T2】

*[原载于 2016 年 4 月 26 日](https://medium.com/cat-in-the-dark/multiplayer-game-on-the-ludum-dare-35-jam-oh-rly-77d5dd04d51c)媒体[基里尔·莱弗](https://medium.com/@KirillLeyfer)和[伊利亚·西加诺夫](https://dev.to/senior_sigan)*