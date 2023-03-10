# 我的 3 大 Ruby on Rails 最佳实践

> 原文：<https://dev.to/mscccc/top-3-ruby-on-rails-best-practices>

这是能让你的努力获得最大回报的东西。

1.  学习**展示者**和**服务对象**的模式。这允许你将复杂的逻辑分解成小的、自包含的和容易测试的类。这使得编写/更改/读取代码变得容易。记住命名事物，并在不同的对象之间建立清晰的分隔。在这里了解更多:[重构脂肪的 7 种模式 ActiveRecord 模型](http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/)

2.  大致遵循 Sandi Metz 规则: [Sandi Metz 对开发者的规则](https://robots.thoughtbot.com/sandi-metz-rules-for-developers)。没必要完全遵循他们。但是把它们作为指导方针。小模型/短方法。方法应该只做一件事。你可以用 Rubocop 之类的东西来强迫自己/整个团队遵守这些规则。

3.  不要太聪明。我的意思是:**保持简单，遵循模式**。不要过早地增加复杂性。你可能认为你需要那个奇特的事件处理程序/rabbit MQ/etc Ruby gem——但是它可以等等。我喜欢遵循这样的规则:“我能用这个解决 3 个问题吗？”在我添加一些非标准的东西之前(我想我是从桑迪·梅斯的书: [POODR](http://www.poodr.com/) 中剽窃了这个想法)。

希望这有帮助！