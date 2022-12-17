# 微光与反应的非单调比较

> 原文：<https://dev.to/jmbejar/unopinionated-comparison-of-glimmer-and-react-h48>

*最初发表于 [Wyeworks 博客](https://wyeworks.com/blog/2017/12/20/unopinionated-comparison-of-glimmer-and-react/)。*

在这篇文章中，我们将讨论使用[微光](https://glimmerjs.com/)与[反应](https://reactjs.org/)的不同(或相似)之处。Glimmer 是三月份发布的[余烬](https://www.emberjs.com/)生态系统中的一个新库。自从这个项目创建以来，Ember 团队一直在尝试一种新的组件 API，这项工作的结果有望在未来逐渐集成到 Ember 本身中。

在某种程度上，Glimmer 与 React 具有可比性，因为两个库的范围非常相似:用于 web 的 UI 组件。但是，让我们看一些例子来突出一些相似之处，以及不同之处。

## 我们先比较一下

让我们从比较一些基本的东西开始。假设我们有一个包含按钮的组件。当点击所述按钮时，显示随机的动物名称。

这就是我们如何用 React 实现它:

```
import React, { Fragment } from "react";

const animals = ["Cat", "Dog", "Rabbit"];

class RandomAnimal extends React.Component {
  constructor() {
    super();
    this.state = { animal: null };
    this.setRandomAnimal = this.setRandomAnimal.bind(this);
  }

  setRandomAnimal() {
    const animal = animals[Math.floor(Math.random() * 3)];
    this.setState({ animal });
  }

  render() {
    let renderAnimal;

    if (this.state.animal) {
      renderAnimal = <h1>Hello, {this.state.animal}</h1>
    }

    return (
      <Fragment>
        <button onClick={this.setRandomAnimal}>Set Random Animal</button>
        { renderAnimal }
      </Fragment>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 试试这个代码[这里](https://jsfiddle.net/69z2wepo/94872/)

Glimmer 的翻译是什么？好吧，下面做同样的事情(请注意，我们需要两个文件):

*模板. hbs*

```
<button onclick={{action setRandomAnimal}}>Set Random Animal</button>
{{#if randomAnimal}}
  Hello, {{randomAnimal}}!
{{/if}} 
```

Enter fullscreen mode Exit fullscreen mode

*component . ts*T2】

```
import Component, { tracked } from '@glimmer/component';

const animals = ["Cat", "Dog", "Rabbit"];

export default class extends Component {
  @tracked randomAnimal = null;

  setRandomAnimal() {
    const animal = animals[Math.floor(Math.random() * 3)];
    this.randomAnimal = animal;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 试试这个代码[这里](http://tinyurl.com/ychd4suv)

显然，HTML 代码在每种情况下都有不同的处理方式:React 依靠 JSX 将 HTML 嵌入到 Javascript 代码中，而 Glimmer 需要一个特定的模板文件，使用[手柄](http://handlebarsjs.com/)将 HTML 代码写入该模板。

您可能已经注意到，我们的组件没有惟一的根元素。Glimmer 支持开箱即用。在 React 中，创建具有多个根元素的组件的一种可能方式是使用`Fragment`标签，如示例中所做的那样。在最新的 React 版本中，对这种情况的支持已经有所改善，正如这里的所解释的。

我们有一个按钮，它附加到一个动作上，当单击按钮时，这个动作被调用。在 React 中，我们通过将组件函数传递给按钮的`onClick`属性来实现这一点。然而，我们需要确保函数被正确地绑定到`this`(我们将函数绑定到构造函数)。另一方面，Glimmer 附带了一个`action`助手，当组件函数`setRandomAnimal`被传递给按钮的`onclick`属性时，我们使用它。

`setRandomAnimal`函数的实现非常相似，但是根据组件的内部状态在每种情况下是如何更新的而有所不同。React 附带了`setState`函数，如果我们想要重新呈现组件，必须用它来改变任何内部状态。

在 Glimmer 中，我们使用`tracked properties`，它通过使用常规 Javascript 语法分配一个新值来更新(在我们的示例中，相关代码是`this.randomAnimal = animal;`)。但是，这依赖于财产申报。我们必须使用`@tracked`注释，以便 Glimmer 跟踪这些值，并在这些属性发生修改时触发组件重新呈现。

我们可以看到 Glimmer 遵循了更具声明性的方法，而 React 的模型更具命令性(由于需要调用`setState`)来管理组件何时在 UI 中被刷新。

## 遍历数组

让我们重写 React 组件来显示动物列表:

```
import React, { Fragment } from "react";

const animals = ["Cat", "Dog", "Rabbit"];

class RandomAnimal extends React.Component {
  constructor() {
    super();
    this.state = { animals: [] };
    this.setRandomAnimal = this.setRandomAnimal.bind(this);
  }

  setRandomAnimal() {
    const animal = animals[Math.floor(Math.random() * 3)];

    this.setState((prevState) => (
      { animals: prevState.animals.concat(animal) }
    ));
  }

  render() {
    const renderedAnimals = this.state.animals.map((animal, index) =>
      <li key={index}>{animal}</li>
    );

    return (
      <Fragment>
        <button onClick={this.setRandomAnimal}>Set Random Animal</button>
        <ul>{ renderedAnimals }</ul>
      </Fragment>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 试试这个代码[这里](https://jsfiddle.net/69z2wepo/94874/)

这里我们修改了代码，以显示动物列表。按钮的 click 事件将调用一个向列表中添加新动物的函数。React 文档声明 [`this.state`不能直接变异](https://reactjs.org/docs/react-component.html#state)，因为 React 的设计思想是组件的状态只能通过`setState`更新。为了避免这种情况，我们使用`concat`来生成动物列表的新实例，包括添加的项目。

另一条关于`setState`的建议值得一提:我们需要依靠`prevState`的值来确保如果 React 决定批量调用`setState` [时，我们不会弄乱动物的数组，正如这里的](https://reactjs.org/docs/state-and-lifecycle.html#state-updates-may-be-asynchronous)所解释的。

以上都是需要注意的基本 *React 的问题*。实际上，我们的组件可以通过改变列表或者依赖于`setState`回调中的`this.state.animals`和*来实现，它可以工作*...大多数时候。不幸的是，这将引入微妙的问题，如果竞争条件确实存在，这将很难跟踪。

现在让我们探索一下它在 Glimmer 中的样子:

*模板. hbs*

```
<button onclick={{action setRandomAnimal}}>Set Random Animal</button>
<ul>
  {{#each randomAnimals key="@index" as |animal| }}
    <li>{{animal}}</li>
  {{/each}}
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

*component . ts*T2】

```
import Component, { tracked } from '@glimmer/component';

const animals = ["Cat", "Dog", "Rabbit"];

export default class extends Component {
  @tracked randomAnimals = [];

  setRandomAnimal() {
    const animal = animals[Math.floor(Math.random() * 3)];

    this.randomAnimals = this.randomAnimals.concat(animal);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 试试这个代码[这里](http://tinyurl.com/y9jsdubp)

这里我们有一些 Glimmer 和 React 之间非常相似的东西:我们需要改变数组来更新 UI。如果我们直接改变数组值，Glimmer 不会刷新组件，因为它不会检测到被跟踪属性中的值变化。当跟踪的属性是数组或对象时，必须提供具有修改值的新实例。这在“不可变模式”一节中有解释，这里的[是](https://glimmerjs.com/guides/tracked-properties)。但是，如果我们忽略了这个建议，并且对动物数组进行了变异，那么当我们单击按钮时，组件根本不会更新。因为动物列表中的变化没有反映在网页上，所以我们知道有些东西没有按预期工作，因此，不太可能出现 React 案例中的竞争条件问题。

关于用户界面是如何构建的，我们观察在每种情况下产生 HTML 的机制是如何的不同。在 Glimmer 中，我们使用手柄，它带有控制结构，如`if`和`each`块来构建动态内容。此外，您可以访问组件属性(所有的属性，无论是否被跟踪)，并且您永远不会通过`this`来引用组件本身。事实上，这基本上是一种声明性的方法...又一次。

在 React 中，您将最终以一种更加程序化(和命令性)的方式形成 HTML 输出，很可能将输出计算的一部分拆分到几个变量或辅助函数上，最后将由`render`函数返回的值中的所有内容连接起来。由于我们的案例并不复杂，我们只需要在`map`函数的帮助下使用局部变量`renderAnimals`来构建`li`元素的列表。

## 构成组件

以探索组件之间的交互为借口，让我们重写 React 示例，使其只包含按钮的一个特定组件:

```
import React, { Fragment } from "react";

const animals = ["Cat", "Dog", "Rabbit"];

function AnimalButton(props) {

  function setRandomAnimal() {
    const animal = animals[Math.floor(Math.random() * 3)];
    props.onAnimalPicked(animal);
  }

  return (
    <button onClick={setRandomAnimal}>{props.caption}</button>
  );
}

class RandomAnimal extends React.Component {
  constructor() {
    super();
    this.state = { animals: [] };
    this.addAnimalToList = this.addAnimalToList.bind(this);
  }

  addAnimalToList(animal) {
    this.setState((prevState) => (
      { animals: prevState.animals.concat(animal) }
    ));
  }

  render() {
    const renderedAnimals = this.state.animals.map((animal, index) =>
      <li key={index}>{animal}</li>
    );

    return (
      <Fragment>
        <AnimalButton caption="Set Random Animal" onAnimalPicked={this.addAnimalToList}/>
        <ul>{ renderedAnimals }</ul>
      </Fragment>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 试试这个代码[这里](https://jsfiddle.net/69z2wepo/94873/)

注意，我们添加了无状态组件`AnimalButton`，并且我们正在为这个组件确定列表中的下一个动物。然后，我们传递一个新动物调用的回调函数。将值添加到列表中是我们主要组件的工作。标题文本和回调函数都是作为属性传递的值。

类似地，我们可以在 Glimmer 中进行类似的重构:

AnimalButton/template.hbs

```
<button onclick={{action setRandomAnimal}}>{{@caption}}</button> 
```

Enter fullscreen mode Exit fullscreen mode

动物按钮/组件. ts

```
import Component, { tracked } from "@glimmer/component";

const animals = ["Cat", "Dog", "Rabbit"];

export default class extends Component {
  setRandomAnimal() {
    const animal = animals[Math.floor(Math.random() * 3)];

    this.args.onAnimalPicked(animal);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

RandomAnimal/template.hbs

```
<AnimalButton @caption="Set Random Animal" @onAnimalPicked={{action addAnimalToList}} />

<ul>
  {{#each randomAnimals key="@index" as |animal| }}
    <li>{{animal}}</li>
  {{/each}}
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

RandomAnimal/component.ts

```
import Component, { tracked } from '@glimmer/component';

export default class extends Component {
  @tracked randomAnimals = [];

  addAnimalToList(animal) {
    this.randomAnimals = this.randomAnimals.concat(animal);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 试试这个代码[这里](http://tinyurl.com/y9j3lunu)

查看 Glimmer 解决方案，我们注意到它在将值传递给`AnimalButton`组件的方式上非常相似(注意，在 Glimmer 中，参数以字符`@`开始)。在这两种情况下，我们都向`AnimalButton`组件传递了一个按钮元素的字符串，以及一个将动物添加到列表中的函数。

在我们的 React 解决方案中，回调是`props`的一个属性，所以当按钮被单击时，来自父组件的`addAnimalToList`函数最终被调用。在 Glimmer 中，传递的函数作为`this.args`的属性在子组件上下文中可用，这也使得调用非常容易。这里唯一的区别是`action`关键字的用法，它自动将函数绑定到父组件上下文，这通常是最方便的绑定。React 中通常需要手动将回调绑定到组件实例，就像我们示例中的`constructor`函数的最后一行所做的那样。

这里最明显的区别与非常简单的组件有关，如`AnimalButton`。在 React 中，可以将`AnimalButton`实现为无状态组件。这是一种简化不包含内部状态的组件定义的方法。Glimmer 中没有类似的概念，在 Glimmer 中，最简单的选择是只包含手柄代码的组件。但是，必须有一些类型脚本代码来处理 click 事件和调用回调，因此在我们的例子中这两个文件都是必需的。

我们之前不是提到过 Glimmer 默认使用 Typescript 而不是 Javascript 吗？🤠

另一件重要的事！在 Glimmer 中，文件的位置和名称非常重要。所有组件都应该放在单独的文件夹中的`ui/components`下，文件夹中有特定的文件`template.hbs`和`component.ts`。为了保持事物的正确联系，必须遵守这个约定。在 React 世界中，人们有更多的自由来组织事情，因为您负责使用 [ES6 模块导入语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)在需要时导入组件。

## 进一步比较

除了上面的代码示例之外，这两个库之间还有其他值得强调的相似之处。

例如，两者都支持为组件定义内联内容。看看下面的`AnimalButton`组件的渲染函数的替代实现:

```
function AnimalButton(props) {

  ...

  return (
    <button onClick={setRandomAnimal}>
      {props.children}
    </button>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

标签`<AnimalButton>`下嵌套的任何内容都将替换`props.children`。下面是一个父组件如何调用它的例子:

```
function RandomAnimal() {
  return (
    <AnimalButton onAnimalPicked={this.addAnimalToList}>
      Set Random Animal
    </AnimalButton>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Glimmer 中，使用`yield`关键字也可以做到这一点。虽然在 Glimmer 文档中没有正式提到，但它的工作方式与预期一样。嗯，如果你有过使用 Ember 的经验，你可能会想到一些东西😌。

AnimalButton/template.hbs

```
<button onclick={{action setRandomAnimal}}>
  {{yield}}
</button> 
```

Enter fullscreen mode Exit fullscreen mode

RandomAnimal/template.hbs

```
<AnimalButton @onAnimalPicked={{action addAnimalToList}}>
  Set Random Animal
</AnimalButton> 
```

Enter fullscreen mode Exit fullscreen mode

这两个库都有额外的相似特性，比如能够在组件的 DOM 层次结构之外进行渲染(参见 React 中的[门户](https://reactjs.org/docs/portals.html)和 Glimmer 中的`{{in-element}}`助手，这里提到的)。

另一个重要的方面是组件 API 中生命周期挂钩的可用性。事实上，Glimmer 并没有包含很多这样的内容；只有`didInsertElement`、`didUpdate`和`willDestroy`出现在公共 API 中。React 有更多的挂钩，但我希望在不久的将来在 Glimmer 中看到类似的功能(例如，类似 React 的`componentWillReceiveProps`将是一个不错的选择)。

## 结论

本文中的例子让我们能够涵盖两个库中相似的方面。同时，我们讨论了每个库解决不同问题的方法。虽然这种比较并不是对每个库的能力的完整回顾，但是已经强调了许多相关的方面，并且提供了您可以从 Glimmer 和 React 中得到什么的总体想法。

我们应该注意到，Glimmer 是一个相当年轻的库，因此，已经向开发者公开的组件 API 仍在不断变化和进一步建立。另一方面，这个库由 GlimmerVM 提供支持，这是 Ember 更高版本附带的渲染引擎，因此它已经在许多现有的应用程序中使用。Ember 团队目前正在改进这个引擎，这将很快影响 Glimmer。例如，他们正试图优化 GlimmerVM 生成的字节码，并尝试增量渲染和再水合。关于这些主题的进一步阅读可以在这里找到:[微光进展报告](https://www.emberjs.com/blog/2017/10/10/glimmer-progress-report.html)。当然，React 本身是一个非常活跃的项目，并且随着今年 Fiber 的到来[渲染引擎完全改变了。](https://code.facebook.com/posts/1716776591680069/react-16-a-look-inside-an-api-compatible-rewrite-of-our-frontend-ui-library/)

Glimmer 的一个有趣的方面是，它依赖 ES6 类作为组件，这使它在这方面更像 React 而不是 Ember，这解释了一些相似之处。同时，这两种方法在组件状态管理、模板语言和操作 DOM 的底层机制方面有所不同。自然，React 提供了更广泛的功能，因为 API 比 Glimmer 的更大，后者只覆盖了最基本的需求，至少目前是这样。

作为一名 web 开发人员，我觉得了解不同库之间的比较很有趣。我们已经看到了前端环境中的 UI 库如何整合组件的架构，采用 ES6/ES7 习惯用法和好东西，并在可访问的 API 和可靠的呈现性能之间寻找平衡。