# A/B 测试的基础知识

> 原文：<https://dev.to/chrislock/the-abcs-of-ab-testing>

## TL；速度三角形定位法(dead reckoning)

1.  你可能应该是 A/B 测试功能。
2.  第三方服务可能会让您承担一些工程责任。
3.  这些服务可能与您闪亮的新 JS 框架不兼容。
4.  可以在 React 中编写 A/B 测试组件。
5.  第三方服务也可能会降低您的网站速度，并导致用户页面闪烁。
6.  分配和跟踪群组也不难，并且允许您使用服务器端渲染。
7.  DIY A/B 测试 FWT。

## 你为什么要这么做？

在一家年轻的初创公司工作，我们强烈地以用户为中心。用户让我们的业务得以维持，这意味着我们保住了工作。然而不幸的是，我们不是我们的用户。尽管我们有几十年的行业经验，我们不能总是预见他们将如何接受变化和新功能。我们与产品甚至整个网络的接近程度会影响我们的视角，阻止我们预测他们的行为。

看起来整个行业都意识到了这一点，并采用 A/B 测试作为解决方案。我们发现它比临时的、面对面的用户测试更有效，这些测试使用的样本组不能准确反映我们的整体用户群，而且它明显优于猜测我们的用户会如何反应。

## 第一个可怕的想法

我们最初的方法是使用第三方服务，起初这似乎很理想。它的集成成本很低，不需要任何工程努力就可以创建实验，但我们很快发现它有一些明显的缺点。最明显的是实验编辑器中可怕的菜单，包括“插入 HTML”和“移动和调整大小”等项目。让工程师之外的人控制我们的 DOM 感觉像是一个重大的责任。这位编辑似乎也不太了解响应式网络，它要求管理实验的人具备良好的工程知识，了解最佳实践，并理解我们的布局如何工作，这远远超出了他们的正常专业知识。如果没有这方面的经验，他们很容易通过使用 4MB 的图像进行测试来破坏布局或损害性能。

## 闪亮的新玩具

当我们引入 React 时，我们旧的测试框架完全停止了工作。两者都会操纵 DOM，但是 React 的生命周期事件最终会覆盖测试。我们发现我们仍然可以为实验分配群组并监控结果，因此我们编写了一个轻量级组件来处理显示变化。

```
render() {
  return (
   <p>
     <ExperimentVariation name={this.experiment} variation='Default'>
        Fancy text
     </ExperimentVariation>

     <ExperimentVariation name={this.experiment} variation='Variation 1'>
        Other fancy text
     </ExperimentVariation>
   </p>
  );
}
```

注意，`Default`和`Variation 1`第三方服务中使用的命名约定不是我们的选择。虽然让每个实验变量作为它的组成部分似乎是多余的，但这并没有强迫`ExperimentVariation`成为兄弟姐妹。考虑下面这个虚构的例子:

```
render() {
  return (
   <article>
     <ExperimentVariation name={this.experiment} variation='Default'>
        <h1>Pay us money</h1>
     </ExperimentVariation>

     <img src='money.png' />

     <div>
       <ExperimentVariation name={this.experiment} variation='Variation 1'>
          <h1>Give us money</h1>
       </ExperimentVariation>

       <p>Not lorem ipsum</p>
      </di>
   </article>
  );
}
```

实际的`ExperimentVariation`是一个相当轻量级的组件。

```
import React, { Component, PropTypes } from 'react';
import ExperimentStore from 'stores/ExperimentStore';

export default class ExperimentVariation extends extends Component {
  static propTypes = {
    name: PropTypes.string,
    variation: PropTypes.string,
  };

  state = {
    isActive: false,
  }

  componentDidMount() {
    this.setState({
      isActive: ExperimentStore.isActiveVariation(this.props.name, this.props.variation),
    });
  }

  render() {
    return (this.state.isActive)
      ? <span>{this.props.children}</span>
      : null;
  }
}
```

我会把实验商店的丛林留给你。它主要只是解析第三方服务粘在我们页面中的巨大的全局对象，并进行值-键比较，以确定实验是否是活动的。

## 分手

这个解决方案很快就建立起来了，这意味着我们不必重新建立群组分配或衡量结果，但我们很快注意到我们正在使用的服务有更多的缺点。

首先，它将所有的实验都限制在客户端，这意味着我们测试的任何东西在`DOMContentLoaded`之前都不会显示，迫使我们只能在折叠线以下测试，或者给我们的用户留下一个闪烁的页面。不是很理想。

我们还在网站速度审计中发现，这个第三方服务贡献了大量的页面权重，并导致了长的渲染帧，因为它通常会操作 DOM。第二次大罢工。

## 过于乐观的工程学

所以现在我们正在建立我们自己的内部测试框架。从概念上讲，这相当简单。您创建数据库支持的实验，存储分配给每个群组的百分比。当新用户访问时，您对其进行 cookie 处理，并对照该值来确定他们看到的是哪个`ExperimentVariation`。我们能够消除闪烁，因为我们的服务器端渲染反应。我们还引导我们的存储，这样服务器和客户端上的数据就会被模仿。即使您不这样做，服务器和客户机仍然可以访问群组 cookie，并且可以保持同步。我们还做了大量的跟踪，所以收集结果有些琐碎。大多数网站已经在使用某种形式的跟踪，即使只是谷歌分析。虽然它可能不会像第三方 A/B 测试框架那样给你一个漂亮的仪表板，但它仍然允许你量化结果。

这可能是一个严重的过度简化。我们很快就会知道，但是主要的观点是:如果你已经购买了 A/B 测试，你可能要在承诺第三方服务之前三思。它们会带来很大的负担，并对您的站点性能产生负面影响，并且考虑到构建您自己的站点所需的工作量有限，它可能是值得的。