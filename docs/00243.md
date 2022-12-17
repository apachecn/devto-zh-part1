# 角度 2+的依赖注入样式替代

> 原文：<https://dev.to/3zsforinsomnia/dependency-injected-style-overrides-for-angular-2-21ia>

## 我不喜欢 Angular 2+的一点是....样式覆盖

Angular 2+很棒。在目前的形式下，它是一套强大的工具，运行迅速，易于使用，并提供一些开箱即用的神奇能力。但是有一个地方我总是发现自己对 Angular 2+感到沮丧，那就是与它的几乎所有其他方面相反，styles 感到很难超越。

“标准”选项通常包括滥用 ng-class，这更适合在运行时更改 CSS 类，而不是覆盖现有组件默认设置的 CSS 类。此外，您还必须设置一个使用 ng-class 的条件，比如一个组件的输入，ng-class 可以使用它来设置正确的覆盖集。

但是也可以对字符串值进行插值，例如“class= " "。这仍然需要某种类型的输入来确定要获取什么，可以是传递给组件的充满字符串的对象，也可以是组件可以传递给所提供的服务的类似 ng-class 的输入，该服务然后可以返回适当的字符串来获取对象。

这两个都很恶心。在这两种情况下，我们都必须向我们的组件添加另一个依赖项，无论我们是否覆盖任何内容，都必须采用这个依赖项，并且这两种情况都比在编译时确定类要慢，因为当 ng-class 运行时类会发生变化(导致可能出现样式不正确的内容)或者服务返回新的字符串。

我们需要一种解决方案，它不需要我们提供任何不必要的输入，并在组件编译时使覆盖类可用，以便组件以正确的样式编译。

## 解决方案是存在的！

我们可以通过一个注入的依赖项，通过配置而不是代码来实现这一点！

我先解释一下，然后通过一个实例来说明。

您可以将对象作为值提供。这些可以是简单的、类似 JSON 的对象，通过简单的“导出常量”就可以得到。。简单地插入对象中可用的字符串(如果没有提供值，它仍然工作，不会抛出任何错误！)和……你完了。实际上，这些对象只是配置 JSON，并且在组件层次结构中您希望覆盖应用的任何地方都提供了一行依赖配置。

唯一的另一个要求是相关的 CSS 类是可用的，但是通过简单地使覆盖的 CSS 对组件可用(通过从中进行绘制的样式文件的正常数组),我们不必牺牲对阴影 DOM 或其 polyfills 的使用。换句话说，CSS 的用法与正常角度分量的用法完全一样。

这种解决方案不允许在提供了依赖关系之后，不正确样式的内容作为有角度的呈现而闪烁的可能性，此时已经提供了要内插的 CSS 类。我们不需要提供每个覆盖的*,因为不存在的属性不会返回任何东西，并且没有覆盖被应用。*

## 它看起来像什么

那么，让我们看一下这个例子，在 Stackblitz 上看到的[。](https://ngx-modifiable-component-styles.stackblitz.io/)

如果您检查一下 [my-button.component.ts](https://stackblitz.com/edit/ngx-modifiable-component-styles?file=app%2Fmy-button%2Fmy-button.component.ts) 文件，您可以看到我们没有指定提供者，而是在第 14 行的构造函数中为“值”指定了一个@Injector。这就是我们所需要的注入样式覆盖对象！

您还可以看到，我们在第 6 行定义了一个样式文件列表，其中包括普通的 my-button.styles.css 文件以及两个覆盖文件。这只是让我们能够访问默认样式和覆盖。对任何角度分量来说都很正常。

在 [app.module.ts](https://stackblitz.com/edit/ngx-modifiable-component-styles?file=app%2Fapp.module.ts) 文件中，我们看到我们导入了一个“默认”对象(在第 23 行),它导入了一个默认对象来提供。[这个物体](https://stackblitz.com/edit/ngx-modifiable-component-styles?file=app%2Fvalues%2Fdefault.value.ts)包含…没什么！我们不需要覆盖默认值，所以它可以是一个空对象。如果我们愿意，我们可以在这里定义默认的 CSS 类，而不是 my-button.component.html 的[文件，但这是我正在使用的方法。](https://stackblitz.com/edit/ngx-modifiable-component-styles?file=app%2Fmy-button%2Fmy-button.component.html)

我在组件中使用相同的提供者行([第一个覆盖组件](https://stackblitz.com/edit/ngx-modifiable-component-styles?file=app%2Ffirst-container%2Ffirst-container.component.ts)，[第二个覆盖组件](https://stackblitz.com/edit/ngx-modifiable-component-styles?file=app%2Fsecond-container%2Fsecond-container.component.ts)；查看第 9 行组件的“providers”属性，这两个组件都像在 app.module 中一样覆盖样式，但是使用它们各自的覆盖对象([第一个覆盖](https://stackblitz.com/edit/ngx-modifiable-component-styles?file=app%2Fvalues%2Ffirst.value.ts)，[第二个覆盖](https://stackblitz.com/edit/ngx-modifiable-component-styles?file=app%2Fvalues%2Fsecond.value.ts))。

就是这样。除了为您正在用重写的 CSS 类定义 CSS 规则之外，其他什么都不需要。

总之，我们有一个快速、可配置的选项，只需对现有代码进行最少的修改，就可以将现有样式覆盖为普通的角度组件。没有古怪，没有滥用不合适的工具，没有条件。

## 示例中需要注意的一些观点

### CSS 文件组织

我选择将用于覆盖的 CSS 类放在不同的文件中，以表示它们不是组件的正常样式的一部分。你可以把 CSS 类定义放在任何你想放的地方。

### 默认的 CSS 对象

虽然您可以将默认的 CSS 类应用到默认对象中，但我更喜欢将它们放在 HTML 中。这要求使用的 CSS 遵循一个易于覆盖的系统，以及命名的 CSS 类为 HTML 提供奇妙的语义上下文的事实。它确实需要使用一种强调低特异性值的 CSS 方法，以便轻松地覆盖现有的类——出于这个原因，我建议在下一节中使用 BEM。这也使得对所有组件使用相同的默认配置对象变得容易。

## 使用建议

### 组合的“超级”配置对象

这是一个简单的例子，但是在更现实的情况下，您可能不希望为存在的每个可配置组件处理配置对象。我仍然建议为每个组件创建一个配置对象，但是不要单独提供它们，而是提供一个更大的“超级配置”对象。这个对象可以用一个简单的函数创建(然后通过依赖注入提供),该函数返回一个对象，该对象包含每个对象中定义的所有键值对。这可以像示例中一样使用，因为对象的键将按预期存在，而其他所有内容都将被忽略。

### 不要过度使用

你可能不应该过度使用这个。当组件只有 1-3 个非默认配置时，它最有可能发挥作用，比如当某类用户需要给定的元素以特定的方式出现时。

但是这个“规则”也有例外，比如图标。您可以为每个想要使用给定组件的图标定义这种类型的覆盖。如果这些组件需要一个点击处理程序，可以很容易地由父组件作为点击处理属性指令提供。这显然是您的特定用例的问题。

### 保持 CSS 易于覆盖

另一件事是，这最适合鼓励 CSS 类与 HTML 元素一对一关系的 CSS 类方案。在这个例子中，我使用了我通常喜欢的 BEM 方法，但是任何为一个 HTML 元素生成一个 CSS 类的方法都可以。这是由于这样一个事实，即如果你只需要一个 CSS 类，覆盖一个已经应用的 CSS 类是最容易的。另一个 CSS 类。CSS 特异性规则意味着，如果只有一个 CSS 类与目标 HTML 元素相关，这是最容易的，因为多个类会立即造成特异性方面的复杂性。

### 您可以在 React 中使用这种方法！

由于 React 组件只是具有附加功能的 JS 对象，所以您可以通过 React 的上下文 API 提供配置对象，它提供了与我们在 Angular 2+中使用的类似的层次依赖注入功能。只需将覆盖返回的任何字符串附加到您提供的 className 中。

尽情享受吧！