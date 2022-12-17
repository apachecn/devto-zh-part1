# 在 React 中用纯函数和函数组合构建用户界面

> 原文：<https://dev.to/tylermcginnis/building-user-interfaces-with-pure-functions-and-function-composition-in-react>

如果你正在读这篇文章，你可能对函数的概念很熟悉。什么时候使用什么时候不使用一个函数对你来说很自然。在这篇文章中，我们将学习如何利用这些知识来构建更好的用户界面。

React.js 最好的部分之一是，您可以使用与函数相同的直觉来决定何时创建新的 React 组件。然而，不是你的函数接受一些参数并返回一个值，而是你的函数将接受一些参数并返回一些 UI。这个思想可以用下面的公式来概括，f(d)=V .一个函数接受一些数据，返回一个视图。这是考虑开发用户界面的一个很好的方式，因为现在你的 UI 只是由不同的函数调用组成，这就是你已经习惯的构建应用程序的方式，你从函数中获得的每一个好处现在都转移到了你的 UI 上。

现在让我们看一些实际的代码。

```
var getProfilePic = function (username) {
  return 'https://photo.fb.com/' + username
}

var getProfileLink = function (username) {
  return 'https://www.fb.com/' + username
}

var getProfileData = function (username) {
  return {
    pic: getProfilePic(username),
    link: getProfileLink(username)
  }
}

getProfileData('tylermcginnis') 
```

Enter fullscreen mode Exit fullscreen mode

看上面的代码，我们有三个函数和一个函数调用。你会注意到我们的代码非常干净和有条理，因为我们把所有的东西都分成了不同的功能。每个函数都有特定的用途，我们通过一个函数(getProfileData)利用另外两个函数(getProfilePic 和 getProfileLink)来组合我们的函数。现在，当我们调用 getProfileData 时，我们将获得一个代表我们用户的对象。你应该对上面的代码非常熟悉。但是现在我想做的不是让这些函数返回一些值，而是让我们稍微修改它们来返回一些 UI(以 JSX 的形式)。在这里你会真正看到 React 的**渲染**方法的美妙之处。

```
var ProfilePic = React.createClass({
  render: function() {
    return (
      <img src={'https://photo.fb.com/' + this.props.username'} />
    )
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

```
var ProfileLink = React.createClass({
  render: function() {
   return (
      <a href={'https://www.fb.com/' + this.props.username}>
        {this.props.username}
      </a>
    )
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

```
var Avatar = React.createClass({
  render: function() {
    return (
      <div>
        <ProfilePic username={this.props.username} />
        <ProfileLink username={this.props.username} />
      </div>
   )
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

```
<Avatar username="tylermcginnis" /> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们不是通过编写函数来获得一些值，而是通过编写函数来获得一些 UI。这个想法在 React 中非常重要，React 0.14 引入了无状态功能组件，允许将上面的代码编写为普通函数(我们将在本课程的后面更深入地讨论)。

```
var ProfilePic = function (props) {
  return <img src={'https://photo.fb.com/' + props.username'} />
} 
```

Enter fullscreen mode Exit fullscreen mode

```
var ProfileLink = function (props) {
  return (
    <a href={'https://www.fb.com/' + props.username}>
      {props.username}
    </a>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

```
var Avatar = function (props) {
  return (
    <div>
      <ProfilePic username={props.username} />
      <ProfileLink username={props.username} />
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<Avatar username="tylermcginnis" /> 
```

Enter fullscreen mode Exit fullscreen mode

上述每个函数和组件的一个共同点是它们都是“纯函数”。

也许我最喜欢 React 的一点是它给了我一个函数式编程(FP)的简单介绍，FP 的一个基本部分是纯函数。

纯函数的整个概念是一致性和可预测性(在我看来，这是编写优秀软件的关键)。

一致性和可预测性的原因是因为纯函数具有以下特征。

*   给定相同的参数，纯函数总是返回相同的结果。
*   纯函数的执行不依赖于应用程序的状态。
*   纯函数不会修改其作用域之外的变量。

当你调用一个“纯”函数时，你可以根据它的输入准确预测将要发生什么。这使得函数很容易推理和测试。

让我们看一些例子。

```
function add (x,y) {
  return x + y
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然简单， **add** 是一个纯函数。没有副作用。给定相同的参数，它总是给我们相同的结果。

现在让我们看看两个本地 JavaScript 方法。。切片然后。拼接

```
var friends = ['Ryan', 'Michael', 'Dan']
friends.slice(0, 1) // 'Ryan'
friends.slice(0, 1) // 'Ryan'
friends.slice(0, 1) // 'Ryan' 
```

Enter fullscreen mode Exit fullscreen mode

通知**。slice** 也是纯函数。给定相同的参数，它将总是返回相同的值。这是可以预见的。

我们来对比一下。切片的朋友，。拼接

```
var friends = ['Ryan', 'Michael', 'Dan']
friends.splice(0, 1) // ["Ryan"]
friends.splice(0, 1) // ["Michael"]
friends.splice(0, 1) // ["Dan"] 
```

Enter fullscreen mode Exit fullscreen mode

**。splice** 不是一个纯粹的函数，因为每次我们调用它传入相同的参数时，都会得到不同的结果。它也在改变状态。

为什么这对 React 很重要？主要原因是 React 的 **render** 方法需要是一个纯函数，因为它是一个纯函数，所以纯函数的所有好处现在也适用于你的 UI。另一个原因是，习惯于使你的函数变得纯粹，并将“副作用”推到程序的边界是一个好主意。我会在整个课程中说，如果你以正确的方式学习 React，React 会让你成为更好的开发人员。学习编写纯函数是这个旅程的第一步。

* * *

在 twitter 上关注我