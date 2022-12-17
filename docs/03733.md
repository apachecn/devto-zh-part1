# 。NET Standard 向开发者解释:如果是接口呢？

> 原文：<https://dev.to/thatfrankdev/net-standard-explained-to-developers-what-if-it-was-an-interface-bjj>

最近，微软用一个新概念震撼了整个网络生态系统:[。网标](https://docs.microsoft.com/en-us/dotnet/standard/net-standard)。很多像我一样的人，已经很难跟上所有的事情。NET，心想“现在是什么？!"。

现在，我花了一点时间来思考这个问题，我想我已经想出了一个对开发者友好的解释。NET 标准真的是。事实上，我已经将它具体化为一小段(不完整的)代码。

为了让事情变得简单一点，试着想象一下。NET 标准作为接口，每个平台将它们作为类来实现。你会得到这样的结果:

```
interface INETStandard_1_0 { /* Lots of methods */ }
interface INETStandard_1_1 : INETStandard_1_0 { /* More methods */ }
interface INETStandard_1_2 : INETStandard_1_1 { /* More methods */ }
interface INETStandard_1_3 : INETStandard_1_2 { /* More methods */ }
interface INETStandard_1_4 : INETStandard_1_3 { /* More methods */ }
interface INETStandard_1_5 : INETStandard_1_4 { /* More methods */ }
interface INETStandard_1_6 : INETStandard_1_5 { /* More methods */ }
interface INETStandard_2_0 : INETStandard_1_6 { /* More methods */ }

// .NET Core Platform
class DotnetCore_1_0 : INETStandard_1_6 { /* Implements methods */ }
class DotnetCore_2_0 : DotnetCore_1_0, INETStandard_2_0 { /* Implements methods */ }

// .NET Framework Platform
class DotnetFramework_4_5 : INETStandard_1_1 { /* Implements methods */ }
class DotnetFramework_4_5_1 : DotnetFramework_4_5, INETStandard_1_2 { /* Implements methods */ }
class DotnetFramework_4_6 : DotnetFramework_4_5_1, INETStandard_1_3 { /* Implements methods */ }
class DotnetFramework_4_6_1 : DotnetFramework_4_6, INETStandard_2_0 { /* Implements methods */ }

// Mono Platform
class Mono_4_6 : INETStandard_1_6 { /* Implements methods */ }
class Mono_5_4 : Mono_4_6, INETStandard_2_0 { /* Implements methods */ }

// and so on... 
```

Enter fullscreen mode Exit fullscreen mode

现在，事实上，事情有点复杂，因为 API 表面是由。NET 标准包括类和它们的成员，但本质上这就是:一个接口，有几个实现。

如果你对此感兴趣，你可以去官方回购处，所有的工作都在那里进行。

编辑:团队中有人刚刚告诉我大卫·福勒对这个主题的看法，当然这很棒。我建议你也看看这个！