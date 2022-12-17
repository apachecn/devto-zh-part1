# 使用 React Native 的领域驱动设计

> 原文：<https://dev.to/ddonprogramming/domain-driven-design-with-react-native-1k0>

问题:

如何指导程序员实现领域驱动设计和[模块化架构](https://dzone.com/refcardz/patterns-modular-architecture)

使用的相关技术:

*   阿波罗客户端
*   graphql
*   反应自然
*   nodejs
*   回家的

作为一名程序员，我总是倾向于优先考虑技术细节和`just making things work`。防止构建封闭的单一代码库的技术之一是不断重构。

我从开发领域驱动设计中学到的一个架构技巧是 twick 问题。重构时，我通常会问自己“这个对象是做什么的？”，这是一个关于领域的问题，而不是“我如何让所有这些部分一起工作”，这是一个关于底层技术或基础设施的问题。

为了允许有节奏的开发，我了解到设计领域可能是一个更长的过程，只要每个人都同意更大的图景，并且他们朝着那个目标努力。

鉴于此，我决定利用手头的技术来实现一个目标。我用 PHP 重构一个整体的架构看起来是这样的:

```
application
     controllers[web-specific]
     services
model
     [bounded-contexts]
              entities
              interfaces[or contracts of the domain]
infrastructure
     [bounded-contexts]
          services
persistence 
```

在大多数语言中，可以遵循这些通用的规则，但是其中一些部分的实现和需求可能会被忽略，特别是一个 react-native 应用程序可能很少甚至没有持久性需求，但是我希望如果在使用片段时考虑到领域，它会变得更加重要并在领域模型中根深蒂固。

作为开发 react-native 第一个代码库时的额外工作，当考虑架构时，我喜欢问的首要问题是:`how would a web app use this module ?`。这主要是因为能够在 react native 和 react 之间交换表示组件，并从相同的底层逻辑中获得 web 体验。

在做一些关于这个主题的研究时,[微观世界](http://code.viget.com/microcosm/guides/quickstart.html)在我能找到的领域建模的最佳帮助方面是最接近的。
在应用程序中，微观世界可以作为替代 redux 的候选，然而微观世界有着根深蒂固的领域驱动设计规则，有时可以作为一个架构示例。
他们的建议[在这里](https://www.viget.com/articles/microcosm-our-data-layer-for-react)可能看起来像是取代 redux，当构建一个相对较小的应用程序或刚刚开始时，这可能是一个相当公平的评估，我可以看到他们一起工作，尽管 redux 可能也会稍微影响架构。

对您的基础架构的另一个重大影响是[如何分离您的组件](https://reactarmory.com/answers/how-should-i-separate-components)，armory 的人谈到了 4 种组件类型，有些可能会放弃一些类型，当在一个定义了不同边界的领域中时，我可以看到这些服务(我仍然会使用服务组件或功能)也在变大，所以有时这 4 种类型是有意义的。

这些[关于在大型应用程序中使用 redux 的技巧](https://techblog.appnexus.com/five-tips-for-working-with-redux-in-large-applications-89452af4fdcb)仍然适用。正确使用领域工具可以保持数据对象之间的分离，但是其他技巧是很棒的，至少要注意。

有鉴于此，我希望我给了你一些东西来思考，在开发或重构你的下一个 react 原生应用时咀嚼。如果你不同意或者你觉得这些观点完全不可思议，不要羞于发表评论。

将感谢任何类型的反馈，因为这是一个正在进行的发展。

还发布于:

*   [https://decebalonprogramming . net/post/domain-driven-design-with-react-native/](https://decebalonprogramming.net/post/domain-driven-design-with-react-native/)
*   [https://medium . com/@ decebaldobrica/domain-driven-design-with-react-native-Fe 7530 b 8696 e](https://medium.com/@decebaldobrica/domain-driven-design-with-react-native-fe7530b8696e)
*   [https://steemit . com/software-architecture/@ dece bal 2 DAC/domain-driven-design-with-react-native](https://steemit.com/software-architecture/@decebal2dac/domain-driven-design-with-react-native)