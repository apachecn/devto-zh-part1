# 有 BEM-efit 的朋友

> 原文：<https://dev.to/adamaso/friends-with-bem-efits-4ehp>

## 边界元法简介

### 什么是 BEM？

**块**、**元素**和**修饰符** (BEM)方法是由 [Yandex](https://en.bem.info/) 开发的 CSS 类的通用命名约定。BEM 背后的核心思想是提供一种严格的方法来将 CSS 类安排到模块中。BEM 通过其 CSS 类名语法来捕捉目的和含义:

*   **块**:表示一个模块或上下文，元素在其中找到自己。(*“**什么**是什么？”*)
    *   航行
    *   页脚
    *   项目-审核
*   **元素**:描述块内执行特定功能的组件。(*“这个**是做/显示**的？”*)
    *   项目
    *   环
    *   按钮
*   修饰符:定义一个块或元素的外观、状态或行为。(*“在什么**州**？”*)
    *   活跃的
    *   红色
    *   大的

我们用两个下划线表示元素，两个破折号表示修饰符:

```
.navigation__item--active
.footer__link--red
.item-review__button--large 
```

Enter fullscreen mode Exit fullscreen mode

### 比喻

为了进一步理解这些是如何相互关联的，可以把它想象成一个类比。

#### 等级制度

```
Car
|-- Transmission
|   -- Automatic
|   -- Manual

Person
|-- Female
|   -- Lips
|-- Male
|   -- Beard 
```

Enter fullscreen mode Exit fullscreen mode

#### CSS

```
.car {}
.car__transmission {}
.car__transmission--automatic {}
.car__transmission--manual {}

.person {}
.person--male__beard {}
.person--female__lips {} 
```

Enter fullscreen mode Exit fullscreen mode

假设我们想要显示不同的类来描述汽车的变速器。**块**或模块将是`Car`。我们展示的**元素**是一个`Transmission`，是**修改**或者用`Automatic`和`Manual`来描述。

在我们的第二个例子中，我们看到修饰符出现在元素之前！！

如果你看看我们的树层次结构，这种方法更有意义。在这个例子中，一个`Person`被分成一个`Female`和一个`Male`类。每个修改器都有自己的元素，比如`Lips`或者`Beard`。

记住，在创建命名层次结构时，要考虑周到，要有品味。没有人想阅读`.person--female__arm__hand--left__finger--pinky`并且很容易弄乱你的标记。如果需要这种程度的特异性，尝试将其分解成子模块，以便于阅读和组织。

### 萨斯+贝姆= < 3

在 Sass 文件中，构造类的一种方法是通过嵌套。

```
.block {
    &__element {
        &--modifier { 
        }
    }
    &--modifier {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将打印出:

```
.block__element {}
.block__element--modifier {}
.block--modifier[] 
```

Enter fullscreen mode Exit fullscreen mode

这种架构的伟大之处在于处理可伸缩性的潜力。代码被模块化分组，并描述其用途和状态。让我们来看一个例子:

#### HTML

```
<ul class="tabs">
    <li class="tabs__header--active">
        <img class="tabs__content__image" src="">
    </li>
    <li class="tabs__header">
        <img class="tabs__image" src="#">
        <img class="tabs__image--inverse" src="#">
    </li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

#### 萨斯

```
.tabs {
    &__header {
        &--active {
        }    
    }
    &__image {
        &--inverse {
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 输出的 CSS

```
.tabs {

}
.tabs__header {

}
.tabs__header--active {

}
.tabs {

}
.tabs__image {

}
.tabs__image--inverse {

} 
```

Enter fullscreen mode Exit fullscreen mode

相当酷！随着应用程序的增长，这种嵌套结构使得类名更容易阅读，并消除了其他开发人员阅读您的代码时的猜测。

只要记住: ***尽量不要窝你的斗嘴超过[三级深](https://www.sitepoint.com/sass-basics-nesting/)！！***

### 如此...为什么是 BEM？

*   自我记录的 CSS！它显示了 DOM 中不同节点之间的层次关系。
*   严格的类命名促进了封装，减少了命名冲突或在给定上下文之外使用类。
*   [性能优势！](https://developers.google.com/web/fundamentals/performance/rendering/reduce-the-scope-and-complexity-of-style-calculations#use_block_element_modifier)
*   仅从名称上告诉其他开发人员一段标记正在做什么。
*   移除对[后代选择器](https://css-tricks.com/efficiently-rendering-css/#article-header-id-3)的需求。
*   组件风格是分离的，并且在项目之间具有高度的可移植性。实际上，这意味着代码质量和开发速度的提高！

简而言之，BEM 将促使你关注细节，思考问题，从而提高代码的质量:)