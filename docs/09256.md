# Angular 2 及以上的部件和组件

> 原文：<https://dev.to/michaeljota/widgets-and-components-for-angular-2-and-up>

## [T1】简介](#intro)

如果你来自 React，React 社区开发了一些模式，使得用这个框架开发应用程序变得容易和简单。其中一个模式是*智能*和*非智能*组件，或者*容器*和*组件*。这在组件应该如何显示数据和组件实际如何管理数据之间保持了分离。如果你想了解更多，有很多关于它的文章，这不是我要写的。

## 情况

根据标准，一个网页的实际名称是“web 组件”,所以有人可能认为对显示数据的代码段调用*组件*是最准确的命名方式。但是...

[![I reject nature](img/fac0719b4caaa5252237f8a1f749985b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Six_2jxO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/1l5mqr.jpg)

Angular 社区已经发展了他们自己思考组件是什么的方式，在 Angular 社区中，我已经多次看到一个*组件*有一个*行为*。

> 什么？！这太疯狂了。组件应该只关心如何显示数据，而不关心如何管理数据。

嗯，这是真的。角分量被称为*分量*的唯一事实是，这是展示我的观点的例子之一。尽管如此，我们仍然需要一些方法来管理数据的显示。

## 解决方案建议

我知道这只是另一个解决方案，我的实际上并没有给当前的实现增加任何东西，它只是同一件事情的语法糖。

### 介绍小工具

因此...Widgets？它们应该是什么样的？我提议用*小部件*来命名任何只需要关心如何显示的东西。这是它擅长的角度，分离。我不是说 React 不好，但我认为 Angular 团队在这个问题上有更好的方法。

理想情况下，一个小部件应该只有`@input`和`@output`，仅此而已。一个小部件必须显示信息，它必须是一个组件，反映在他们的代码。小部件中不应该有逻辑，但是小部件需要逻辑来显示输入的数据。

我还考虑了在小部件中管理数据的方式，但是那将是另外一天的事情了(或者你可以阅读这个[问题](https://github.com/mgechev/codelyzer/issues/222)

### 应该是什么 widgets

*   小部件应该是角度应用程序的表示组件。
*   小部件应该是无逻辑的。
*   小部件应该关注自己数据的样式。

## 提问

##### widget 只是*组件*？

是的。:).不多不少。只是*组件*。他们必须展示信息，但他们不应该关心信息是如何提供给他们的。

##### 那为什么另起一个名字呢？

正如我所说，Angular community 中的*组件*已经被用于开发由*管理*信息的网页。

##### 为什么叫它们 *widgets* ？

因为，毕竟，*小部件*实际上是用来显示信息的，所以这样称呼它们似乎是合乎逻辑的。

## 例子

不应该有一个动机去使用一个没有例子命名提议，这就是我要给你的。

```
import { Component, Input } from '@angular/core';

import { ITodoItem } from './../../models';

@Component({
  selector: 'my-todo-list-widget',
  template: `
  <ul>
    <my-todo *ngFor="let todo of todoList;trackBy:todo?.id"
      [todo]="todo"
    ></my-todo>
  </ul>
  `,
})
export class TodoListWidget {
  @Input() public todoList: ITodoItem[];
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的使用案例。我试着解释一下代码。

```
import { Component, Input } from '@angular/core';

import { ITodoItem } from './../../models'; 
```

Enter fullscreen mode Exit fullscreen mode

首先，进口。从`@angular/core`我们引入`input`和`component`装饰者。记住，Angular 中的所有东西都是装饰，Widgets 也是，那个名字只是糖。没有一个`Widget`装饰师，我也不认为它应该是一个。从模型文件夹中，导入`Item`模型。

```
@Component({
  selector: 'my-todo-list-widget',
  template: `
  <ul>
    <my-todo *ngFor="let todo of todoList;trackBy:todo?.id"
      [todo]="todo"
    ></my-todo>
  </ul>
  `,
}) 
```

Enter fullscreen mode Exit fullscreen mode

所以我们装修。我使用后缀`widget`作为选择器，因为我有一个同名的组件。所以如果你能比我更好地命名事物，你应该不需要它。还有，模板，越少越好。我认为如果你的模板少于 10 行，你应该内嵌它。但是，这取决于你。重要的是在这里定义所有的显示行为。

```
export class TodoListWidget {
  @Input() public todoList: ITodoItem[];
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们声明这个类。这是重要的部分。如你所见，这里没有逻辑。这只是一个请求一个`input`属性的类，没有别的。

#### 更多例子

如果你想要更多关于小部件的例子，请查看我的回购[angular 2-ultimate-starter](https://github.com/michaeljota/angular2-ultimate-starter)。这是基于`AngularClass` starter 的，但是我添加了`ngrx/store`实现，并且借用了 React 的一些模式，如`Widgets`。

## 谢谢

暂时就这些了。这是我的第一篇关于 Angular 的文章。我希望你喜欢这个，对于你对此要说的任何事情，我期待着阅读你的评论。干杯，编码快乐！