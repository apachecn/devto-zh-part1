# Elm 的几个步骤:Mdl、架构和“本地”更新

> 原文：<https://dev.to/leojpod/a-few-more-steps-with-elm-mdl-architecture-and-local-update>

#### 从我上次停下的地方继续，我将分享我从一个丑陋的网页转移到一个基于 elm-mdl 的网页的经验，以及如何分割代码和更新方法(虽然这看起来并不流行)

### 开始玩 elm-mdl

尽管 elm-mdl 模块证明了自己相当好用，但是设置它并不容易。我跟随[这篇文章](https://medium.com/@dailydrip/introduction-to-using-material-design-in-elm-dc2320087410)来设置我的应用程序。第一步是在我的主模型上创建一个“属性”来插入 mdl 模型。这就是 elm-mdl 发挥其魔力并跟上整个应用程序的状态的地方。elm-mdl 还为我们提供了其模块“初始”状态。