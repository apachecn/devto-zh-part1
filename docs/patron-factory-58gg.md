# 工厂老板

> 原文：<https://dev.to/mangelsnc/patron-factory-58gg>

[![](img/02bf5598e15c0517629dd91b6472698f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VPhpYSH9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AttR3hrWs9gkw7ts2yECXNg.jpeg)

### 导言

在《T0》关于软件设计模式的帖子中，我们看到这些可以分为三大类:创造性、结构性和行为性。在本文中，我们将以属于创作模式组的**工厂**模式进行深入研究，看看其实施情况、使用案例以及优缺点。

> **重要:T1】不要把**工厂**模式与**抽象工厂**模式混为一谈，虽然类型相同，但它们以不同的方式解决不同的问题。**

#### 普罗波西托

与任何创作模式一样，它的任务是解决创建或实例化对象时出现的问题。在这种情况下，factory 图案旨在向客户端隐藏某些对象的实例化详细信息，也就是说:

> 取消关联业务逻辑创建逻辑，从而避免客户知道其依赖的对象实例化的详细信息。

### 实施

#### 例

假设我们正在安排一个经销商信息面板，因此单击其中一个型号可以显示您的数据表。代码可以是这样的: