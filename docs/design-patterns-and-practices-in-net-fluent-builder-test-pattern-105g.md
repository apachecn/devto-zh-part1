# 设计模式和实践。Net: Fluent Builder 测试模式

> 原文：<https://dev.to/matheusrodrigues/design-patterns-and-practices-in-net-fluent-builder-test-pattern-105g>

单元测试的安排阶段是我们编写更多代码的阶段。编码数据创建可能很麻烦。最坏的情况发生在我们必须填充所有类的属性时，即使我们在测试中只使用了一个属性。除了乏味之外，数据创建过程可能有大量代码，导致测试可读性和可维护性较差。

为了解决这个问题，我们可以使用构建器模式。该模式在《设计模式》一书中有所描述，并解决如下问题:

*   一个类如何创建一个复杂对象的不同表示？
*   如何简化创建复杂对象的类？

构建器模式通过将复杂对象的构建委托给构建器对象，为复杂对象的创建带来了更大的灵活性。构建器对象可以创建同一复杂对象的不同表示。

在这篇文章中，我将重点放在单元测试方面，我将向您展示一个不同版本的构建器模式，即 Fluent 构建器测试模式。

Fluent Builder 测试模式的主要目标是促进测试数据的创建。将构建器模式和流畅的界面模式结合起来，使得复杂对象的创建变得简单明了。

[继续阅读...](https://www.matheus.ro/2018/01/08/design-patterns-practices-net-fluent-builder-test-pattern/)