# Vuejs 从头开始反应

> 原文：<https://dev.to/koehr/vuejs-reactivity-from-scratch>

*这是来自[我的个人博客](https://log.koehr.in/2017/08/17/vuejs-reactivity-from-scratch)的交叉帖子。*

*原版本包含 JSFiddle 的嵌入式代码，在这里不行。因此，我把链接放在了相应的 JSFiddle 页面上。*

Vuejs 是 Javascript 框架世界中的明星新人。人们喜欢它把复杂的事情变得非常简单却富有表现力。更令人兴奋的特征之一是它看似神奇的反应能力。当属性改变时，组件中的普通数据对象神奇地调用 rerender。

[链接到 js dild](//jsfiddle.net/koehr/ajwchnko/embed/)

单击按钮会调用一个函数，该函数只是为一个属性分配一个新值。模板仍然会自动重新呈现。但我们都知道这和仙尘无关，对吧？那么它实际上是如何工作的呢？

## 消气定神的魔术

有了 [ES5 标准](http://www.ecma-international.org/ecma-262/5.1/)，JavaScript 有了许多令人兴奋的新特性。在我看来，其中一些被严重低估和利用不足。[getter 和 setter](http://www.ecma-international.org/ecma-262/5.1/#sec-8.6.1)就是一个例子。如果你从未听说过它们，我会推荐你去读读约翰·里西格关于它们的文章。

一旦你知道了什么是 getters 和 setters:在每次属性访问时透明调用的函数，你可能就已经知道这是怎么回事了。嘣！所有的仙尘突然消失了。

## 自动吸气剂和设置剂

现在我们至少在理论上知道了 Vuejs 是如何实现模板数据魔法的，为了全面理解，让我们自己构建它吧！

摘要:一个函数，它获取一个对象并返回一个属性被 getterss 和 setterss 替换的对象，这些 getter 和 setter 在调用时重新呈现一个模板。到目前为止一切顺利。如果实在不耐烦，可以在 JSFiddle 中找到[最终代码。](https://jsfiddle.net/koehr/e2q9vme3/15/)

让我们从一个非常简单的方法开始:

[链接到 js dild](//jsfiddle.net/koehr/e2q9vme3/embed/)

该函数遍历所有对象键，并用 getters 和 setters 创建一个新对象。它还可以直接操纵原始对象:

[链接到 js dild](//jsfiddle.net/koehr/k72aoorc/1/embed/)

我个人不喜欢操纵现有对象，更喜欢第一种方式。

## 简介:Object.defineProperty

现在，在我们继续摧毁我们对仙尘计算的幻想之前，让我们看看是否有一种更方便的方式来完成我们现在所做的事情。这里我引入了`Object.defineProperty`，它允许为一个对象属性设置所有可能的属性。你可以在 MDN 上找到[的详细描述。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)

有了这个新的知识，通过将所有内容压缩到一个调用中，代码可以变得更具可读性:

[链接到 js dild](//jsfiddle.net/koehr/e2q9vme3/2/embed/)

所有这些下划线都很烦人。我一般建议你，多读一些关于`Object.defineProperty`的书。它大大扩展了可能性的范围！

## 为穷人模板

为了能够在数据改变时重新呈现一个组件，我们应该引入一些能够真正呈现的组件，并且在正确的情况下重新呈现一个模板。

[链接到 js dild](//jsfiddle.net/koehr/e2q9vme3/3/embed/)

这段代码描述了一个非常简单的组件，它有一个数据对象和一个呈现函数。如果调用这个函数，它会用呈现的输出替换给定内容元素的`innerHTML`。整洁！让数据变得有反应性！

## 无功分量

首先，简单地使数据属性具有反应性就足够了:

[链接到 js dild](//jsfiddle.net/koehr/e2q9vme3/4/embed/)

是的，这似乎很好，但它并没有真正更新模板。看看第 11-14 行就清楚了:从来没有呈现调用。但是`reactive`不应该了解组件渲染吧？让我们尝试一种更通用的回调方法:

[链接到 js dild](//jsfiddle.net/koehr/e2q9vme3/5/embed/)

是的，这是可行的，等等，但是看起来我们在代码中慢慢地偏离了优雅。`reactive()`中的变化似乎没问题，但是第 31 行中的函数 bind monstrosity 是我们最好对父母隐藏的东西。在我们被踢出局或自我憎恨之前，让我们介绍一个组件工厂:

[链接到 js dild](//jsfiddle.net/koehr/e2q9vme3/6/embed/)

酷！那行得通。`createComponent()`函数只是为我们做所有的脏工作，并返回一个漂亮的、反应性的组件，它仍然只是一个简单的对象。如果你在本地设置中有这样的代码并运行类似于`component.data.name = 'Ada Lovelace'`的东西，那么它会自动地重新渲染模板以显示‘你好，阿达·洛芙莱斯’。

## 嵌套数据结构

这些都是很酷很时髦的东西，但是在下面的场景中会发生什么呢？

[链接到 js dild](//jsfiddle.net/e2q9vme3/7/embed/)

设置更深的嵌套属性(第 44，45 行)根本不起作用。原因是反应性只对数据对象的第一个嵌套级别起作用。现在你可以说:简单，我们只是一次设置整个对象:

[链接到 js dild](//jsfiddle.net/e2q9vme3/8/embed/)

但这不是我们真正奋斗的目标，不是吗？需要一种方法，使所有嵌套的对象以递归方式反应。令人惊讶的是，这只需要几行代码:

[链接到 js dild](//jsfiddle.net/e2q9vme3/9/embed/)

只添加了三行(7-9)。如果给定值是一个对象，它们就调用`reactive()`。现在嵌套层次已经不重要了。无功诸事！！

## 多个组件

考虑到组件通常非常合群，如果我们为我们的组件找一个朋友会怎么样？会融合吗？呃，我是说，反应？

[链接到 js dild](//jsfiddle.net/e2q9vme3/10/embed/)

确实如此。万岁！

细心的读者可能已经看到了第 7 行中的变化:因为数组的类型是 object，所以这里需要进行额外的检查。否则，该数组将被转换为一个带有键 0、1 等的普通对象。

但是当我们直接操作数组时会发生什么呢？

[链接到 js dild](//jsfiddle.net/e2q9vme3/11/embed/)

真扫兴。设置整个数组像预期的那样工作，但是操作它不会触发任何变化。

## 无功阵列

正如 Vuejs 指南中关于列表呈现的[警告部分](https://vuejs.org/v2/guide/list.html#Caveats)所述，有几个关于数组反应性的警告。它写道:

```
Due to limitations in JavaScript, Vue cannot detect the following changes to an array:
1\. When you directly set an item with the index, e.g. vm.items[indexOfItem] = newValue
2\. When you modify the length of the array, e.g. vm.items.length = newLength 
```

Enter fullscreen mode Exit fullscreen mode

好吧，很公平。那么，在 Vue 中发生了什么使阵列具有反应性呢？仙尘？不幸的是是的。而且这东西很贵！不，只是开玩笑。当然，这也不涉及魔法。对不起我亲爱的角色扮演朋友们。实际发生的情况是，数组的操作函数被一个包装版本所取代，该版本通知组件发生了变化。这个功能的源代码在 [core/observer/array.js](https://github.com/vuejs/vue/blob/dev/src/core/observer/array.js) 中。

Vuejs 的方法相当复杂，但可以浓缩成类似于这里的前 24 行的内容:

[链接到 js dild](//jsfiddle.net/e2q9vme3/12/embed/)

所以这是一个相当大的消化块。新函数`reactiveArray`从创建原始数组的副本开始(还记得吗？我不喜欢操纵原始对象)。然后，对于操作数组函数列表中的每个函数，原始函数被保存，然后由包装函数替换。这个包装函数除了调用原始数组函数之外，还简单地调用呈现回调函数。

现在`lipsumComponent.data.content`也不再直接设置，而是使用覆盖推送方法。直接设置是不行的。解决这个问题让我们进入最后一步:

## 集合上的反应性

现在 setter 函数不关心值。如果它是一个嵌套对象，它的子对象就不会是被动的。这意味着，如果你设置`data.x`为一个对象`{foo: 1}`，然后改变 foo `data.x.foo++`，模板不会重新呈现。这应该改变:

[链接到 js dild](//jsfiddle.net/14/embed/)

第 49 行调用了`reactive(value, callback)`，而不是设置普通值。这个小小的改变只在一定程度上起作用。函数必须决定如何处理非对象或数组，这是`reactive()`中的第一步。一个普通的非对象(记住:数组是对象)简单地按原样返回(第 30 行)，数组将以它们的反应版本返回(第 31 行)。

## 结论

恭喜你！你做到了这一步，或者只是跳过了只看结论，这很好，我有时也这样做。

大约在 70 年[SLOC](https://jsfiddle.net/koehr/e2q9vme3/15/)，我们建立了一个完全反应元件系统。我们使用了 getters、setters 和`Object.defineProperty`并了解到我不喜欢直接操作对象。除了最后一点，这应该是有价值的信息，在未来可能会变得方便。

你可能会问，还能做些什么？Vuejs 的代码更加复杂，可以处理一些为了简单起见我没有提到的 egde 情况。例如，如果尚未成为反应对象已经有一些 getters 和/或 setters，它们将被我们的简单解决方案覆盖。Vuejs' `defineReactive`使用`Object.getOwnPropertyDescription`来获取关于它将要包装的属性的详细信息，并在适用的情况下合并现有的 getters 和 setters。它还忽略了不可配置(根本不打算更改)的属性。如何工作可以在源代码中找到[。](https://github.com/vuejs/vue/blob/dev/src/core/observer/index.js#L140)