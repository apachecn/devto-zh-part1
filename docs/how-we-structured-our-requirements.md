# 我们如何构建我们的需求

> 原文：<https://dev.to/stoft/how-we-structured-our-requirements>

# 我们如何构建我们的需求

一篇关于我们如何在一个以前有点混乱的项目中构建事物的文章，该项目具有几种不同格式的需求，并且它们与我们的测试用例之间的相关性很差。为什么一开始会这样的政治已经超出了这篇文章的范围。

免责声明:我从来没有在这个项目之前与用户故事广泛合作，所以我的经验是有限的，我们的术语用法可能不同于“行业标准”。

## 基本结构

我们有三个基本的人工制品:

*   故事:一个用户故事，非常具体的关于用户希望做或被做的事情。
*   Epic:基于一些公共功能的用户故事的集合。
*   Saga:设定特定用户的特定情况的背景的短 saga。

我们的用户故事遵循以下基本模式:

“作为一个*角色*，我要*的目标/愿望*，让*受益/价值*。

每个故事最终都可以通过自动化测试或用户接受度测试来验证。

传奇和史诗是相互正交的。在一个表格或矩阵中，一个轴是传奇，另一个轴是史诗，每个单元是一个故事。

epic 收集围绕给定功能的用户故事，例如“登录”，可能来自多个角色/利益相关方。最终用户的登录应该很容易，但是从系统所有者的角度来看，它必须是安全的。

saga 从给定用户/角色和情况的角度收集用户故事。它关注并增加对特定用户的理解，确保项目团队了解用户的日常生活，以避免忽略一些显而易见的东西。一个传奇可以将来自许多不同史诗的用户故事收集到一个单一的情境中。

## 举例

### 佐贺:在家查收邮件

简用户:在家里，我坐在电视机前的沙发上，在广告时间，我想用手机查看电子邮件。我通常不在沙发上写回复，因为我更喜欢一个真正的键盘来打字。我通常对做以下事情感兴趣:

1.  登录(如果尚未登录)
2.  获取任何新邮件
3.  查看新电子邮件列表(或类似内容)
4.  挑选一封新的(或旧的)邮件阅读
5.  阅读电子邮件

### 史诗:登陆

*   缓存用户名
*   隐藏密码
*   安全通信

### 故事:

*   作为一个用户，我希望我的用户名被缓存，这样我就不用每次都重写了。
*   作为一个用户，我希望我的密码被隐藏起来，这样那些试图越过我的肩膀阅读的家伙就看不到了。
*   作为一名系统管理员，我希望登录通信被加密，这样敌对实体就不会嗅探凭证。

## 结果和讨论

最后，这个设置对我们来说相当不错。由于来自各种不同公司的地理分布的团队和很少的公共工具，我们最终得到了一个共享的电子表格(最小公分母)，在一个页面上是传奇，在另一个页面上是故事(带有史诗般的专栏)。

我们只为我们的主要用户角色编写了传奇，因为该项目是该角色的新前端，但在其他情况下，为几个不同的角色编写传奇可能会有好处。

你做过类似的事情吗？你会做些不同的事情吗？你认为我们可以对这个结构做些什么改进吗？请分享你的想法。