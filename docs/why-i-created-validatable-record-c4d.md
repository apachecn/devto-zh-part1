# 我为什么创建可验证记录

> 原文：<https://dev.to/izumisy/why-i-created-validatable-record-c4d>

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [泉西](https://github.com/IzumiSy) / [可验证记录](https://github.com/IzumiSy/validatable-record)

### 由 validate.js 支持的不可变. js 记录

<article class="markdown-body entry-content container-lg" itemprop="text">

# 有效记录

[![CircleCI](img/532ec5e171de9fadd44845b749d49b3d.png)](https://circleci.com/gh/IzumiSy/sinatra-errorcodes)[![standard-readme compliant](img/4552d4251267b16c95db499b598d7979.png)](https://github.com/RichardLitt/standard-readme)[![npm version](img/7442a9365397e661dd6539a4e50c7d82.png)](https://badge.fury.io/js/validatable-record)[![MIT License](img/bf51d6e0f0cb518ca90c73500b3e5ac5.png)T11】](https://raw.githubusercontent.com/IzumiSy/validatable-record/master/LICENSE)

> 由 validate.js 支持的不可变. js 记录

## 目录

*   [安装](https://raw.githubusercontent.com/IzumiSy/validatable-record/master/README.md#Install)
*   [用途](https://raw.githubusercontent.com/IzumiSy/validatable-record/master/README.md#Usage)
*   [投稿](https://raw.githubusercontent.com/IzumiSy/validatable-record/master/README.md#Contribute)
*   [测试](https://raw.githubusercontent.com/IzumiSy/validatable-record/master/README.md#Test)
*   [执照](https://raw.githubusercontent.com/IzumiSy/validatable-record/master/README.md#License)

## 安装

```
$ npm install --save validatable-record
```

Enter fullscreen mode Exit fullscreen mode

## 使用

ValidatableRecord 在 Immutable.js 中返回`Record`来扩展自己的类。用法和 Immutable.js 里的`Record`差不多，但是有`validate.js`的威力。使用 ValidatableRecord，您可以使用内置的验证逻辑来定义模型。

```
const ManRecord = ValidatableRecord({
  name: null,
  age: null
}, {
  name: {
    presence: true
  },
  age: {
    presence: {
      message: "is invalid"
    }
  }
});
class Man extends ManRecord {
}
const man = new Man({
  name: "Justine";
  age: 25
});
man.validate() // == true

// Of course you can use `Immutable.Record` methods
man.size        // 2
man.get('name') // "Justine"
man
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/IzumiSy/validatable-record)

> 几个月前，我创建了一个由 [Immutable.js](https://facebook.github.io/immutable-js/) 和 [validate.js](https://validatejs.org/) 组成的小模块。这是我的方法的一种实现，我认为你应该把你的验证逻辑放在前端应用程序的什么地方。如果你看重可伸缩性或可维护性之类的东西，大规模前端应用程序架构领域与你的爱好应用程序完全不同。

在应用程序中的什么地方放置您自己的验证逻辑实际上不是一个小问题。然而，在前端应用程序开发的开始阶段，验证逻辑通常看起来并不重要。在这种情况下，视图层倾向于扮演验证的角色，因为很多人通常认为验证只是视图层的工作。

*“我应该把自己的验证逻辑放在哪里”没有正确答案。*，因为它确实取决于案例，但可以肯定的是视图层中的验证逻辑不应该是与您的业务规则相关的逻辑。

让我们考虑一个常见的验证规则，如*“您的姓名不得包含特殊字符”*。这主要是基础设施层的一种需求，通常包括数据库、外部 API 服务器等等。然而，像“你不能订购这个项目超过一个”或“你的会员资格不提供免费送货”这样的验证是不同的。这些是您自己的应用程序所特有的业务规则。独特性在于，是否仅仅因为您的业务规则才需要该规则。

作为领域层的业务规则永远不会受到视图层和基础设施层等其他层的影响，因为视图和基础设施只是将您自己的业务作为软件提供的实现，而实现永远不会改变您的业务规则。在分层架构的概念中，几乎所有情况下的验证逻辑最好是域层的一部分。验证是业务规则之一，它将应用程序组成一个域，而不是基础结构或视图。

`validatable-record`是用 Immutable.js 和 validate.js 构建的一个小模块，目的是用验证规则定义不可变的记录模型。与 RoR 的 ActiveModel 不同，JavaScript 中没有标准的内置模型验证模块，所以几乎所有时候，如果你想将验证逻辑放在域模型的一部分中，你就必须编写自己的实现。在这种情况下，validatable-record 可以作为内置模型验证的一种方法。

```
const ManRecord = ValidatableRecord({
  name: null,
  age: null
}, {
  name: {
    presence: true
  },
  age: {
    presence: {
      message: "is invalid"
    }
  }
});

// You can use ManRecord as Immutable.Record
class Man extends ManRecord {
  ...
}

const man = new Man({
  name: "Justine";
  age: 25
});

// You can check if the instance is valid or not
man.validate() // == true

// Of course you can use `Immutable.Record` methods
man.size        // 2
man.get('name') // "Justine"
man.get('age')  // 25 
```

Enter fullscreen mode Exit fullscreen mode

`ValidatableRecord`通过将约束规则作为第二个参数，返回用`validate.js`增强的`Immutable.Record`。当然，你仍然可以使用`Record`中的方法。也可以在验证后得到给定的错误消息。更多关于 https://www.npmjs.com/package/validatable-record 的

在本文中，我想用我的`validatable-record`方法强调的是，扩展前端应用程序有时需要认真规划每一层的职责。如今开发前端应用程序比以前更容易了，但是软件工程师仍然需要从可伸缩性和可维护性的角度深入考虑架构。如果这篇文章能让您后退一步，重新思考您自己的前端应用程序架构，我会很高兴。