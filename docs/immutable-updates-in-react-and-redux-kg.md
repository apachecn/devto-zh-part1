# React 和 Redux 中的不可变更新

> 原文：<https://dev.to/dceddia/immutable-updates-in-react-and-redux-kg>

> 当不允许*改变*状态时，如何更新状态？

起初，不变性有点难以理解。这似乎是一个悖论。改数据不改数据？啊？

最重要的是，编写不可变状态更新的代码是很棘手的。下面，你会发现几个常见的模式。

无论是在浏览器开发人员控制台中还是在真实的应用程序中，都可以自己尝试一下。请特别注意嵌套对象更新，并进行实践。我发现这些是最棘手的。

所有这些实际上也适用于 React state，所以无论您是否使用 Redux，您在本指南中学到的东西都将适用。

最后，我应该提到，通过使用像 [Immutable.js](https://facebook.github.io/immutable-js/) 这样的库，一些代码可以变得更容易编写，尽管它有自己的权衡。如果您对下面的语法感到畏缩，请查看 Immutable.js。

## 传符

这些例子对数组和对象大量使用了 **spread** 操作符。它由`...`表示，当放在一个对象或数组之前时，它会展开其中的子对象。

```
// For arrays:
let nums = [1, 2, 3];
let newNums = [...nums]; // => [1, 2, 3]
nums === newNums // => false! not the same array

// For objects:
let person = {
  name: "Liz",
  age: 32
}
let newPerson = {...person};
person === newPerson // => false! not the same object

// Internals are left alone:
let company = {
  name: "Foo Corp",
  people: [
    {name: "Joe"},
    {name: "Alice"}
  ]
}
let newCompany = {...company};
newCompany === company // => false! not the same object
newCompany.people === company.people // => true! 
```

Enter fullscreen mode Exit fullscreen mode

如上所示使用时，spread 操作符使得创建一个包含与另一个对象或数组完全相同的内容的新对象或数组变得容易。这对于创建一个对象/数组的副本，然后覆盖您需要更改的特定属性很有用:

```
let liz = {
  name: "Liz",
  age: 32,
  location: {
    city: "Portland",
    state: "Oregon"
  },
  pets: [
    {type: "cat", name: "Redux"}
  ]
}

// Make Liz one year older, while leaving everything
// else the same:
let olderLiz = {
  ...liz,
  age: 33
} 
```

Enter fullscreen mode Exit fullscreen mode

对象的 spread 操作符是第 3 阶段的草案，这意味着它还不是 JS 的正式部分。你需要使用像 Babel 这样的 transpiler 来在你的代码中使用它。如果使用 Create React App，已经可以使用了。

## 配方更新状态

这些例子是在从 Redux reducer 返回状态的上下文中编写的:假设顶部的`state = {whatever}`是传递给 Redux reducer 的状态，然后更新的版本被返回到下面。

#### 应用 React 和 setState

为了将这些例子应用到普通的反应状态，你只需要调整一些东西:

```
return {
  ...state,
  updates here
}

// becomes:
this.setState({
  ...this.state,
  updates here
}) 
```

Enter fullscreen mode Exit fullscreen mode

以下是一些常见的不可变更新操作:

### 更新一个对象

```
const state = {
  clicks: 0,
  count: 0
}

return {
  ...state,
  clicks: state.clicks + 1,
  count: state.count - 1
} 
```

Enter fullscreen mode Exit fullscreen mode

### 更新嵌套对象

```
const state = {
  house: {
    name: "Ravenclaw",
    points: 17
  }
}

// Two points for Ravenclaw
return {
  ...state,
  house: {
    ...state.house,
    points: state.house.points + 2
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 通过键更新对象

```
const state = {
  houses: {
    gryffindor: {
      points: 15
    },
    ravenclaw: {
      points: 18
    },
    hufflepuff: {
      points: 7
    },
    slytherin: {
      points: 5
    }
  }
}

// Add 3 points to Ravenclaw,
// when the name is stored in a variable
const key = "ravenclaw";
return {
  ...state,
  houses: {
    ...state.houses,
    [key]: {
      ...state.houses[key],
      points: state.houses[key].points + 3
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 在数组的开头添加一个元素

```
const array = [1, 2, 3];
const newItem = 0;
return [
  newItem,
  ...array
]; 
```

Enter fullscreen mode Exit fullscreen mode

### 在数组末尾添加一个元素

```
const array = [1, 2, 3];
const newItem = 4;
return [
  ...array,
  newItem
]; 
```

Enter fullscreen mode Exit fullscreen mode

### 在数组中间添加一个元素

专业提示:为这些事情编写单元测试。很容易犯一个接一个的错误。

```
const array = [1, 2, 3, 5, 6];
const newItem = 4;
return [ // array is new
  ...array.slice(0, 3), // first X items unchanged
  newItem,
  ...array.slice(3) // last Y items unchanged
]; 
```

Enter fullscreen mode Exit fullscreen mode

### 改变数组中间的一个元素

这与添加项目的模式相同，只是索引不同。

专业提示:为这些事情编写单元测试。很容易犯一个接一个的错误。

```
const array = [1, 2, "X", 4];
const newItem = 3;
return [ // array is new
  ...array.slice(0, 2), // first X items unchanged
  newItem,
  ...array.slice(3) // last Y items unchanged
]; 
```

Enter fullscreen mode Exit fullscreen mode

## 一切搞定

这有帮助吗？我错过了你想看到的模式吗？请在下方留言评论。

[React 和 Redux](https://daveceddia.com/immutable-updates-react-redux/) 中的不可变更新最初由戴夫·塞迪亚于 2017 年 11 月 29 日在[戴夫·塞迪亚](https://daveceddia.com)发布。

[代码项目](http://www.codeproject.com)