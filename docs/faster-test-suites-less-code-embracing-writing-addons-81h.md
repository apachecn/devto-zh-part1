# 更快的测试套件，更少的代码，支持编写插件。

> 原文：<https://dev.to/gaiety/faster-test-suites-less-code-embracing-writing-addons-81h>

通过拥抱开源，可以实现更少的代码、更快的测试套件和对核心功能更紧密的关注。我在开源一个广泛使用的[组件时了解到，Ember addons](https://github.com/q2ebanking/ember-select-light) 正在兑现这样一个承诺。

关键是关注点的分离。修改代码会重新构建应用程序，并且在发布之前，所有测试都必须再次验证，不管修改了什么。将一个应用程序的一些部分，比如一个组件，转移到一个开源插件上，可以获得代码隔离带来的所有好处。诚然，通过仔细的抽象和不开源也可以实现相似，但我觉得这是我和其他人努力的正确方向。

开发人员必须记住的代码越少，他们就越容易思考应用程序的运行方式。一个经过良好测试和良好记录的插件承诺了一种功能水平，这种功能水平通常是另一个要在应用程序中调试的文件所不能提供的。虽然插件代码最终会被编译成最终的输出，但我发现将代码隔离到插件中的行为迫使我更简洁明了地编码，以供公众使用。如果没有开源插件的目标，我可能会偷工减料，认为“这只是对我来说，没什么大不了的”，这从长远来看弊大于利。

更快的测试套件运行，没有覆盖成本，将测试隔离到单独的插件项目。事实上，这是双向的。测试从原始应用程序中移除，插件将只包含测试，从而快速构建和测试。孤立的测试关注点可以导致更高的相对代码覆盖率和更快的[测试驱动开发的反馈循环](https://www.youtube.com/watch?v=2b1vcg_XSR8)。

更加关注核心功能是因为需要将插件的关注点分离成一个简洁的想法。为一个插件指定一个名字并编写文档是一个健康的练习，可以产生一个非常有意图并且有价值的插件。

在不再害怕开源世界之后，我已经学会了拥抱来自创建广泛项目的优势。插件对我来说是一个新的令人兴奋的世界，希望对你也是如此。