# 使用 Ember.computed.oneWay 时所冒的风险

> 原文：<https://dev.to/farisj/the-risks-you-take-when-you-use-ember-computed-oneway-3o07>

(注:这篇帖子最初出现在我的公司 AlphaSight 的博客上。你可以在这里查看版本。)

# 使用 Ember.computed.oneWay 时所承担的风险

Ember ，构建雄心勃勃的应用程序(tm)的框架，为开发者提供了许多快速构建应用程序的工具。这个清单中最常用的工具之一是`Ember.computed`，这是一套[函数，用于转换数据](https://www.emberjs.com/api/classes/Ember.computed.html)，以便可以在您的应用程序中使用和操作它。

`Ember.computed`提供的一些功能非常简单。`Ember.computed.empty`获取数组的字符串键，并根据依赖属性是否包含任何元素返回 true 或 false。`Ember.computed.gte`接受一个表示一个数字和一个实际数字的字符串键，并根据相关属性是否大于或等于第二个数字返回 true 或 false。

虽然更多的数学计算函数很容易理解，但是`computed`提供的一些工具需要澄清。

## 别名，只读，单向

有几个函数提供了重命名或重新分配其他值的方法，以便在 Ember 对象中使用。不想一遍又一遍地写`get(this, 'todo.user.name')`，并希望有更快的方法？Ember 会掩护你的。

### 别名

`Ember.computed.alias`在你的 Ember 对象中提供一个双向数据流属性，允许你写一个快捷方式到另一个属性。参考上面的例子，如果你在你的对象中不断重复引用`todo.user.name`，你可以写一个`computed.alias`来使`get`和`set`动作短一些。

```
import Ember from 'ember';

const { computed } = Ember;

export default Ember.Component.exend({
    todo: null,
    userName: computed.alias('todo.user.name'),
    //...
}); 
```

通过这样的设置，您可以在组件的其他地方引用`todo.user.name`(及其模板！)而无需每次都通过`todo`和`user`。

使用`computed.alias`、`get`和`set`对别名属性*的操作就像引用原始属性*一样。这意味着，如果您使用一个组件来`set`T4，这将反映在商店中的用户模型上。

### 只读

`Ember.computed.readOnly`*和*几乎是同一个东西，只有一个非常重要的区别。一个`readOnly`属性的设置是类似的:

```
import Ember from 'ember';

const { computed } = Ember;

export default Ember.Component.exend({
    todo: null,
    userName: computed.readOnly('todo.user.name')
    //...
}); 
```

在这个设置中，调用`get`到用户名正是您所期望的。然而，试图在这个属性上调用`set`实际上会引发一个错误！如果你在一个`readOnly`属性上调用`set`，你会在你的开发工具中看到这个:

`Cannot set read-only property 'userName' on object: <twiddle@component:my-component::ember323>`

`readOnly`函数为我们提供了一个健康的提示，告诉我们在应用程序中应该设置什么，不应该设置什么。

### 单向

`Ember.computed.oneWay`函数是最难理解的一个，也是潜在危险的一个！我们来探究一下原因。

设置基本相同…

```
import Ember from 'ember';

const { computed } = Ember;

export default Ember.Component.exend({
    todo: null,
    userName: computed.readOnly('todo.user.name')
    //...
}); 
```

使用`oneWay`，属性充当其从属属性*的别名，直到它们被设置为*。这意味着这两个属性，一旦表示相同的数据，*就会偏离*，不再表示相同的值。突然，数据流中断了。原始属性和`oneWay`属性不再有联系，因为`oneWay`属性已经以一种不希望影响原始对象的方式进行了更新。

### 这对我的应用意味着什么？

对于像表单这样的东西来说,`computed.oneWay`属性可能非常有用，在这种情况下，您真的不希望一个新的输入立即保存对 Ember 存储中模型字段的更改。然而，某些代码实践可能会导致`oneWay`意外地向用户显示不正确的内容。让我们来探索如何。

假设您有一个`Todos`的集合，并使用计算的属性向用户显示关于这些待办事项的数据。考虑下面的模板:

```
//templates/application.hbs

<div class="todos">
  <div class="selected-todo">
    {{#if selectedTodo}}
      {{todo-display todo=selectedTodo}}
    {{else}}
      <div>Please View A Todo.</div>
    {{/if}}
  </div>
  <br>
  <div>
    {{#each todos as |todo|}}
      <button {{action (mut selectedTodo) todo}}>
        View {{todo.title}}
      </button>
    {{/each}}
  </div>
</div> 
```

并且在`todo-display`组件/模板中:

```
import Ember from 'ember';

const { computed, set } = Ember;

export default Ember.Component.extend({
  todo: null,
  title: computed.oneWay('todo.title'),
  body: computed.oneWay('todo.body'),

  actions: {
    updateBody(){
      set(this, 'body', 'jk, do this thing. haha! youre stuck doing this thing instead');
    }
  }
});

<div class="todo">
  <div class="todo__content">
    <h3>{{title}}</h3>
    <div>{{body}}</div>
  </div>
  <button {{action "updateBody"}}> Update body </button> </div> 
```

这个例子的`application/template.hbs`提供了几个按钮来设置一个`selectedTodo`属性，并将其传递给`todo-display`组件。

在`todo-display`组件中，我们使用`computed.oneWay`属性来显示`todo`的标题和主体。我们还有一个覆盖组件的`body`属性的动作。

这个例子的整体是在一个烬旋转下面。

在这种情况下，首先在 Todos 之间切换会正确更新`todo-display`组件中的所有数据。然而，在第一次调用`updateBody`动作后，组件上从`todo.body`到`body`的数据流被中断。因此，每次切换到不同的 Todo 都不会更新组件中的`body`属性。

这可能很危险，因为您的应用程序突然具有混合状态。现在，您的用户可以使用查看 Todo A 时所执行的不同主体来查看 Todo B。如果您正在执行基于`todo-display`的`body`和`title`属性的操作，您的操作可能不会达到预期的状态！

这种行为在其他情况下也很有用。例如，`Ember.computed.oneWay`在为单个模型构建表单*时会很有用。只要组件被适当地破坏，它就没有机会影响另一个模型。如果我们为多个型号*使用相同的组件*，问题就出在这里。*

### 如何防止这种情况？

这种设置的一个问题是，不同的`todos`被传递到同一个`todo-display`组件，每当数据改变时，该组件的状态不能被正确地重置。解决这个问题的一个办法可能是:

```
//components/todo-display.js
import Ember from 'ember';

const { computed, get } = Ember;

export default Ember.Component.extend({
  todo: null,
  title: computed.readOnly('todo.title'),
  body: null,

  didReceiveAttrs(){
    let body = get(this, 'todo.body');
    set(this, 'body', body);
  }
  //...
}); 
```

通过挂钩到一个组件的生命周期中，我们确保我们在每次新的`todo`被传入时都以相同的方式设置我们的组件。现在，我们的`todo-display`组件更加健壮，不会错误地显示我们的待办事项！

这些只是`Ember.computed`提供的强大工具中的一小部分。既然你已经阅读了这篇文章，并且理解了`alias`、`readOnly`和`oneWay`之间的区别，那就继续创造一些没有副作用的组件吧！干杯；)