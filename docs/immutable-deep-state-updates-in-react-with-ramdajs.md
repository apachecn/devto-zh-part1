# React with Ramda.js 中的不可变深层状态更新

> 原文：<https://dev.to/avanslaars/immutable-deep-state-updates-in-react-with-ramdajs>

使用`setState`，React 中的基本状态更新轻而易举，但是更新状态中深度嵌套的值可能有点棘手。在这篇文章中，我将向你展示如何利用 [Ramda](http://ramdajs.com/) 中的镜头，以一种简洁而实用的方式处理深层状态更新。

让我们从一个简单的计数器组件开始。

```
import React from 'react';
import { render } from 'react-dom';

class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      count: 0
    }
    this.increase = this.increase.bind(this)
    this.decrease = this.decrease.bind(this)
  }

  increase() {
    this.setState((state) => ({count: state.count + 1}))
  }

  decrease() {
    this.setState((state) => ({count: state.count - 1}))
  }

  render() {
    return (
      <div>
        <button onClick={this.increase}>+</button>
        <div>
          {this.state.count}
        </div>
        <button onClick={this.decrease}>-</button>
      </div>
    )
  }
}

render(<App />, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用一个函数作为`setState`的参数，并根据传入的状态值增加或减少计数。这对于一个位于状态树顶层的简单属性来说很好，但是让我们更新我们的状态对象的形状，并将那个`count`移动到状态的更深处。

```
this.state = {
  a: {
    name: 'pointless structure',
    b : {
      stuff: 'things',
      count: 0
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个新的`state`令人难以置信地做作，但它将有助于说明这一点。现在，为了更新计数，我们需要更新属性`a`，它又需要一个更新的`b`，并且将包含我们更新的`count`。`increase`的更新函数现在需要看起来像这样:

```
increase() {
  this.setState((state) => ({a: {...state.a, b: {...state.a.b, count: state.a.b.count + 1}} }))
} 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但是可读性不是很好。让我们简单看看这里发生了什么。

现有状态被传入函数，我们想返回一个对象，表示要与`state`合并的对象。`setState`方法不会递归合并，所以像`this.setState((state) => ({a: {b:{ count: state.a.b.count + 1}}}))`这样的操作会更新计数，但是`a`和`b`上的其他属性会丢失。为了防止这种情况，返回的对象是通过将`state.a`的现有属性扩展到一个新对象中来创建的，然后我们在这个新对象中替换`b`。由于`b`也有我们想保留，但不想改变的属性，我们展开`state.b`的道具，只替换`count`，用基于`state.a.b.count`中值的新值替换。

当然，我们需要对`decrease`做同样的事情，所以现在整个组件看起来像这样:

```
import React from 'react';
import { render } from 'react-dom';

class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      a: {
        name: 'pointless structure',
        b : {
          stuff: 'things',
          count: 0
        }
      }
    }
    this.increase = this.increase.bind(this)
    this.decrease = this.decrease.bind(this)
  }

  increase() {
    this.setState((state) => ({a: {...state.a, b: {...state.a.b, count: state.a.b.count + 1}} }))
  }

  decrease() {
    this.setState((state) => ({a: {...state.a, b: {...state.a.b, count: state.a.b.count - 1}} }))
  }

  render() {
    return (
      <div>
        <h1>{this.state.a.name}</h1>
        <h2>{this.state.a.b.stuff}</h2>
        <button onClick={this.increase}>+</button>
        <div>
          {this.state.a.b.count}
        </div>
        <button onClick={this.decrease}>-</button>
      </div>
    )
  }
}

render(<App />, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

那些电话有点乱！好消息是，**有更好的方法**。镜头将帮助我们清理这个问题，并回到状态更新，既可读，又清楚地传达了更新的意图。

镜头可以让你拿着一个物体“窥视”，或者“聚焦”这个物体的某个特性。您可以通过指定一个路径将焦点放在嵌套在对象内部很深的属性上来实现这一点。当镜头聚焦在目标上时，您可以在该属性上设置新值，而不会丢失周围对象的上下文。

为了创建一个聚焦于我们状态中的`count`属性的镜头，我们将使用 ramda 的`lensPath`函数和描述到`count`的路径的数组，比如:

```
import {lensPath} from 'ramda'

const countLens = lensPath(['a', 'b', 'count']) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个镜头，我们可以将它与 ramda 中可用的镜头消耗函数之一一起使用:`view`、`set`和`over`。如果我们运行`view`，传递给它我们的镜头和状态对象，我们将得到`count`的值。

```
import {lensPath, view} from 'ramda'
const countLens = lensPath(['a', 'b', 'count'])
// somewhere with access to the component's state
view(countLens, state) // 0 
```

Enter fullscreen mode Exit fullscreen mode

不可否认，`view`看起来并不是非常有用，因为我们可以引用`state.a.b.count`的路径或者使用 ramda 的`path`函数。让我们看看如何用我们的镜头做些有用的事情。为此，我们将使用`set`函数。

```
import {lensPath, view, set} from 'ramda'
const countLens = lensPath(['a', 'b', 'count'])
// somewhere with access to the component's state
const newValue = 20
set(countLens, newValue, state) 
```

Enter fullscreen mode Exit fullscreen mode

当我们这样做时，我们将得到一个类似于:
的对象

```
{
  a: {
    name: 'pointless structure',
    b : {
      stuff: 'things',
      count: 20 // update in context
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经得到了我们的`state`对象的新版本，其中`state.a.b.count`的值被替换为`20`。因此，我们不仅在对象结构深处进行了有针对性的更改，而且是以不可变的方式进行的！

因此，如果我们利用目前所学的知识，我们可以更新组件中的`increment`方法，使其看起来更像这样:

```
increase() {
    this.setState((state) => {
      const currentCount = view(countLens, state)
      return set(countLens, currentCount+1, state)
    })
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们已经对镜头使用了`view`来获取当前值，然后调用`set`来基于旧值更新该值，并返回整个`state`的全新版本。

我们可以更进一步。`over`函数接受一个镜头和一个应用于镜头目标的函数。然后，函数的结果被指定为返回对象中该目标的值。所以我们可以用 ramda 的`inc`函数来递增一个数。所以现在我们可以让`increase`方法看起来像:

```
increase() {
    this.setState((state) => over(countLens, inc, state))
  } 
```

Enter fullscreen mode Exit fullscreen mode

很酷，对吧？！嗯，还有更好的...不，真的，它是！

ramda 的所有函数都是自动生成的，所以如果我们只传递第一个参数，我们会得到一个新函数，它需要第二个和第三个参数。如果我给它传递前两个参数，它将返回一个函数，该函数需要最后一个参数。这意味着我可以这样做:

```
increase() {
    this.setState((state) => over(countLens, inc)(state))
  } 
```

Enter fullscreen mode Exit fullscreen mode

其中对`over`的初始调用返回一个接受`state`的函数。嗯，`setState`接受一个函数，该函数接受`state`作为参数，所以现在我可以把整个事情简化为:

```
increase() {
    this.setState(over(countLens, inc))
  } 
```

Enter fullscreen mode Exit fullscreen mode

如果这还不足以表达您的意思，您可以将那个`over`函数移出组件，并给它起一个很好的有意义的名字:

```
// outside of the component:
const increaseCount = over(countLens, inc)

// Back in the component
increase() {
    this.setState(increaseCount)
  } 
```

Enter fullscreen mode Exit fullscreen mode

当然，使用 ramda 的`dec`也可以对`decrease`方法做同样的事情。这将使该组件的整个设置如下所示:

```
import React from 'react';
import { render } from 'react-dom';
import {inc, dec, lensPath, over} from 'ramda'

const countLens = lensPath(['a', 'b', 'count'])
const increaseCount = over(countLens, inc)
const decreaseCount = over(countLens, dec)

class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      a: {
        name: 'pointless structure',
        b : {
          stuff: 'things',
          count: 0
        }
      }
    }
    this.increase = this.increase.bind(this)
    this.decrease = this.decrease.bind(this)
  }

  increase() {
    this.setState(increaseCount)
  }

  decrease() {
    this.setState(decreaseCount)
  }

  render() {
    return (
      <div>
        <h1>{this.state.a.name}</h1>
        <h2>{this.state.a.b.stuff}</h2>
        <button onClick={this.increase}>+</button>
        <div>
          {this.state.a.b.count}
        </div>
        <button onClick={this.decrease}>-</button>
      </div>
    )
  }
}

render(<App />, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

这里的好处是，如果状态的形状发生变化，我们可以通过调整`lensPath`来更新状态操作逻辑。事实上，我们甚至可以使用镜头和`view`一起在`render`显示我们的数据，然后我们可以依靠那个`lensPath`来处理**我们引用的所有**来计数！

这意味着在`render`方法中，`{this.state.a.b.count}`将被`view(countLens, this.state)`的结果所代替。

这是最后一次调整，试着旋转一下，看看你能做些什么！

[![Edit Ramda Lenses - React setState](img/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/Yz5Bkz3n)