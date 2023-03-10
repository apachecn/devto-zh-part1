# 如何用方面编程处理异常并责怪 Covfefe

> 原文：<https://dev.to/k1r0s/how-to-handle-exceptions-with-aspect-programming-and-blame-covfefe>

今天我将向你展示一个简短而有用的面向方面编程的例子。

```
import * as React from "react";

import Sidebar from "../Sidebar/Sidebar.main";
import Content from "../Content/Content.main";

import { Advices } from "../../advices/Advices"
import { onException, afterMethod } from "kaop-ts"

export default class Root extends React.Component<null, null> {

    someProp: any

    @onException(Advices.blameCovfefe)
    @afterMethod(Advices.throwOnError)
    render(){
        return (
            <div className="mainContainer">
                <Sidebar />
                <Content />
                {this.someProp.nil}
            </div>
        )
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们在执行时得到的结果:

> 未捕获的类型错误:无法读取 undefined 的属性“nil ”,尽管常量 press covfefe 为负值

这里是`Advices`的实现

```
import { AdvicePool, adviceMetadata, IMetadata } from 'kaop-ts'

export class Advices extends AdvicePool {
  static blameCovfefe (@adviceMetadata meta: IMetadata) {
    meta.exception.message += " despite the constant negative press covfefe"
  }

  static throwOnError (@adviceMetadata meta: IMetadata) {
    if(meta.exception) {
      throw meta.exception
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

也许这不是一个好例子，但如果你知道 Twitter 的 API，它可能有助于转移一些纳税人的注意力..

但是如果你觉得前面的例子很有趣..与其抛出一个异常，不如在每次我们的应用失败时呈现一个错误组件:

```
import { AdvicePool, adviceMetadata, IMetadata } from 'kaop-ts'
import { Covfefe } from './covfefe-components'

export class Advices extends AdvicePool {
  static blameRussia (@adviceMetadata meta: IMetadata) {
    if(meta.exception) {
      meta.result = <Covfefe/>
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 好的

Welp。面向方面编程(也称为 AOP)的思想是在一个地方管理常见的问题，并且仍然可以访问所需的上下文。

在本文中我们将使用 [**kaop-ts**](https://github.com/k1r0s/kaop-ts/)

在前面的例子中，我们需要在异常以某种方式发生时捕获异常，但是我们不想到处粘贴相同的代码块而弄乱我们的代码。我们只是想告诉我们的应用程序在 OOP 范例中的几个生命周期挂钩中应用封装的模式(又名:“连接点”)。

AOP 是 OOP 的扩展。它帮助我们鼓励抽象和模块化。这是反对重复的强有力的工具。

您可能还记得，为了复制某些行为，您不得不粘贴一些代码块，只替换一个变量或引用。想想圆木，是的。

但是还有许多其他情况，其中大多数都被框架很好地覆盖了。例如，在 NodeJS 环境中，Express 处理许多几乎没有人必须处理的“常见问题”，比如从 HTTP 请求接收有效载荷。

AOP 无处不在，但是大多数框架都没有提供工具来扩展这种技术，通过提供对连接点的访问。例如，当 JQuery 或 EJS/下划线模板是处理 DOM 操作的唯一方式时，AngularJS 为常见问题带来了一系列很好的解决方案。

包含 DOM 操纵的现代框架，如 Vue 或 Angular，以及许多其他框架都鼓励声明式编程，声明式编程包含要执行的隐式指令，以通过消除副作用来实现其目的(DOM 操纵)。React 说得更直白一些，JSX 和虚拟世界的想法简直太棒了。

kaop-ts 非常适合用强大的抽象层和架构资产从底层构建大的东西。但它也通过提供对连接点的访问增强了您的代码(也就是:“通过访问动态上下文在 OOP 范例中的几个生命周期钩子中应用封装的模式”)。

如今，它可以访问:`AfterInstance, BeforeInstance, AfterMethod, BeforeMethod, OnException`，在这里您可以检索:

```
export class Registry extends AdvicePool {
  static log (@adviceMetadata meta: IMetadata) {
    meta.args // Arguments to be received by decorated method
    meta.propertyKey // Name of the decorated method as string
    meta.scope // Instance or the context of the call stack
    meta.rawMethod // Original method (contains metadata)
    meta.target // Class definition
    meta.result // The returned value by the method
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以读写这些引用，当然，也可以执行异步请求，而不会弄乱调用堆栈。换句话说，您可以访问通知中的服务层(也称为:“在连接点中执行的函数”)，并执行异步请求来检索数据和注入修饰的方法。

仔细阅读这段代码:

```
// view.ts
import { beforeMethod } from 'kaop-ts'
import { PersistanceAdvices } from './persistance-advices'
import { FlowAdvices } from './flow-advices'
import { OrderModel } from './order-model'

class View {
  @beforeMethod(PersistanceAdvices.read, OrderModel)
  @beforeMethod(FlowAdvices.validate)
  update (data?) { ... }
}

// persistance-advices.ts
import { AdvicePool, adviceMetadata, adviceParam, IMetadata } from 'kaop-ts'
import { Service } from './somewhere'
import { ICommonModel } from './somewhere'

export class PersistanceAdvices extends AdvicePool {
  static read (@adviceMetadata meta: IMetadata, @adviceParam(0) model: ICommonModel) {
    Service.get(model.url)
    .then(data => meta.args.push(data))
    .then(this.next) 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`this.next`是一个明确告诉 kaop-ts 当前操作需要时间来完成的工具，因此后续操作将等待直到完成

注意，`View class`有一个名为`update`的方法，如果我们实例化`View`并对其调用`update()`，我们将触发一个组合调用栈，但是实现是干净的、声明性的，并且没有副作用。

我们的`viewInstance`将接收请求数据作为更新方法中的一个参数，而**将被执行，就像我们作为参数**显式提供的一样。

所以，感谢阅读。希望你喜欢它。[检查回购](https://github.com/k1r0s/kaop-ts/)并随时贡献:)快乐编码！

向科维费致敬。