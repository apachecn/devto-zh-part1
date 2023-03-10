# 使用 react 和 firebase 创建应用程序-第一部分

> 原文：<https://dev.to/aurelkurtula/creating-an-app-with-react-and-firebase---part-one-814>

在探索了 firebase 和 react 的基础知识之后，我想在本教程中一起使用它们。在这个由三部分组成的系列中，我将创建另一个 todo 应用程序。我将使用 react，我在这里[介绍了它的基础知识](https://dev.to/aurelkurtula/introduction-to-react-532)，在这里我制作了同一个应用程序的一个更简单的版本。我还将使用 react 路由，我在这篇文章中也提到了[。](https://dev.to/aurelkurtula/introduction-to-react-router-ha)

因为我不想让这个教程太长，所以我打算在第二部分把 firebase 添加到这个项目中。在本教程中，我们将把数据从 react 组件状态转移到 firebase 数据库。然后在第三部分，我们将添加身份验证，用户可以添加他们自己的私有待办事项。

### 创建静态标记

首先，我们将快速创建应用程序的基本设计。我在这里做的每件事在其他地方都已经讲过了。让我们从安装 react 中路由所需的包开始。

```
yarn add react-router-dom 
```

Enter fullscreen mode Exit fullscreen mode

`App`组件将成为主要组件。它将保存应用程序的状态和逻辑。然而，让我们从创建基本结构开始。如果你想在 [codesandbox](https://codesandbox.io/) 中开始，这意味着在`index.js`中开始编辑。如果您通过终端创建一个 react 应用程序，您将从`src/App`开始。

```
import React, {Component} from 'react' 
import { BrowserRouter, Route, Link } from 'react-router-dom';
import './App.css'
class App extends Component {
  state = {
    items: {
      1123: {
        item: 'item one',
        completed: false
      },
      2564321: {
        item: 'item two',
        completed: true
      }
    }
  }
  render() {
    return (
      <BrowserRouter>  
        <div className="wrap">
          <h2>A simple todo app</h2>
          <ul className="menu">
            <li><Link to={'/'}>To do</Link></li>
            <li><Link to={'/completed'}>Completed</Link></li>
          </ul>
          <Route exact path="/" render={props => {
              let lis = []
                for(let i in this.state.items){
                  if(this.state.items[i].completed === false){
                    lis.push(<li key={i}>{this.state.items[i].item} <span >x</span></li>)
                  }
                }
                return(<ul className="items"> { lis } </ul>  )
              }   
            }  />
          <Route exact path="/completed" render={props => {
              let lis = []
                for(let i in this.state.items){
                  if(this.state.items[i].completed === true){
                    lis.push(<li key={i}>{this.state.items[i].item} <span >x</span></li>)
                  }
                }
                return(<ul className="items"> { lis } </ul>  )
              }   
            }  />
        </div>
      </BrowserRouter>   
    );
  }
}
export default App; 
```

Enter fullscreen mode Exit fullscreen mode

当在浏览器中加载应用程序时，您将能够在主页和`/completed`之间导航，并看到不同之处。

关于以上代码如何工作的解释，请阅读我之前关于[反应路由器](https://dev.to/aurelkurtula/introduction-to-react-router-ha)基础的教程

### 使用子组件

让我们创建一个子组件来处理重复的代码。在`components/ItemsComponent.js`处创建一个文件，并添加以下代码。

```
import React from 'react'
const ItemsComponent=({items, done})=> {
    let lis = []
    let mark = done === false ? '\u2713' : 'x';
    for(let i in items){
        if(items[i].completed === done){
          lis.push(<li key={i}>{items[i].item} <span >{mark}</span></li>)
        }
    }
    return(<ul className="items"> {lis} </ul>  )
}
export default ItemsComponent; 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，这是一个无状态的功能组件，它不需要一个类(向@omensah 欢呼，感谢他在这个方向上给了我启发)。它非常适合这种情况，在这种情况下，逻辑不需要使用我们从`Component`类继承的功能。科里豪斯在这篇文章中完美地比较了两种风格

让我们修改`App`组件以利用`ItemsComponent`，这也将阐明第 2 行中被解构的参数。

```
import ItemsComponent from './components/ItemsComponent';
class App extends Component {
  ..
    return (
      <BrowserRouter>  
        <div className="wrap">
          ...
          <Route exact path="/"
            render={props => 
              <ItemsComponent  items={this.state.items} done={false}/> 
            }/>
          <Route exact path="/completed" 
            render={props => 
              <ItemsComponent  items={this.state.items} done={true}/> 
            }/>
        </div>
      </BrowserRouter>   
    );
  }
}
export default App; 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`render`而不是`component`属性来呈现`ItemsComponent`组件，我[在撰写 react 路由器](https://dev.to/aurelkurtula/introduction-to-react-router-ha)时已经介绍过了，因为我们需要向它传递一个布尔值来指示要显示哪些项目。因此，使用 ES6 解构是不言自明的:

```
const ItemsComponent=({items, done})=> { ... } 
```

Enter fullscreen mode Exit fullscreen mode

否则，上述内容可以写成

```
const ItemsComponent=(props)=> { ... } 
```

Enter fullscreen mode Exit fullscreen mode

我们将不得不在`props`对象中获取`items`或`done`。

### 添加动作

我们要做的前两个动作是将一个项目标记为完成，以及完全删除任何完成的项目。

正如我所说的,`App`组件将成为主要组件。它掌握着我们的主要状态。因此，让我们编写修改状态的方法。

```
class App extends Component {
  state = {
    items: {
      1123: {
        item: 'item one',
        completed: false
      },
      2564321: {
        item: 'item two',
        completed: true
      }
    }
  }
  completeItem=(id)=>{
    let items =   {
        ...this.state.items, 
        [id]: {...this.state.items[id], completed: true      }
      }
    this.setState({ items })
  }
  deleteItem = (id) => {
    let  {[id]: deleted, ...items} = this.state.items;
    this.setState({ items })
  }
  ... 
```

Enter fullscreen mode Exit fullscreen mode

`completeItem`方法从当前状态获取项目，然后我们选择具有相关`id`的项目，最后将其`completed`属性更改为`true`。

删除相关对象略有不同。我目前正在努力学习更多关于 spread 操作符的知识，这也是我在上面添加它的原因。我找到了片段...猜猜在哪里？...[在 stackoverflow](https://stackoverflow.com/questions/36553129/what-is-the-shortest-way-to-modify-immutable-objects-using-spread-and-destructur)

接下来，`completeItem`和`deleteItem`方法需要被传递给`ItemsComponent`

```
 render() {
    return (
      ...
          <Route exact path="/"
            render={props => 
              <ItemsComponent  
                items={this.state.items} 
                done={false}
                action={this.completeItem}
                /> 
            }/>
          <Route exact path="/completed" 
            render={props => 
              <ItemsComponent  
                items={this.state.items} 
                done={true}
                action={this.deleteItem}
                /> 
            }/>
       ...
    ) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将`action`与`components/itemsComponent.js`的`onClick`事件捆绑在一起

```
const ItemsComponent=({items, done, action})=> {
    let lis = []
    let mark = done === false ? '\u2713' : 'x';
    for(let i in items){
        if(items[i].completed === done){
          lis.push(<li key={i}>{items[i].item} 
            <span onClick={()=> action(i)}>{mark}</span></li>)
        }
      }
      return(<ul className="items"> {lis} </ul>  )
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，唯一改变的是第一行中对`action`方法的解构。然后我把它加到跨度上。`i`是`items`对象中每个对象的 id。

### 添加物品

如果用户不能添加项目，todo 应用程序就没有用。目前，项目的硬编码，但这是为了帮助我们达到这一点。

这将工作的方式是，我希望用户能够添加新的项目，只有当他们正在查看未完成的项目，换句话说，只有当他们在根路径而不是`/completed`路径。让我们在`components/ItemsComponent.js`文件中添加输入框:

```
const ItemsComponent=({items, done, action})=> {
    ...
    return (
        <div>
            {done
            ? (<ul className="items"> {lis} </ul>)
            : (
            <div>
                <form>
                    <input type="text" /> 
                </form>
                <ul className="items"> {lis} </ul>
            </div>
            )}
        </div>
    );                   
} 
```

Enter fullscreen mode Exit fullscreen mode

请记住，`done`是一个布尔值，如果`true`表示项目被标记为已完成，因此我们不想看到表单，否则，我们会看到。

React 要求外部 div 包装整个输出，还要求用元素包装`form`和`ul`。

最后，正如删除和完成操作一样，我们将在`App`组件中添加表单的逻辑，并通过 props 将其与表单链接起来。让我们在`App.js`中创建逻辑

```
class App extends Component {
  ...
  addItem=(e)=> {
    e.preventDefault();
    let items =  {
      ...this.state.items, 
      [new Date().valueOf()]: {
        item: this.todoItem.value, 
        completed: false     
      }
    }
    this.setState({
      items
    });
  }
  render() {
    return ( 
        ...
          <Route exact path="/"
            render={props => 
              <ItemsComponent  
                ...
                addItem={this.addItem}
                inputRef={el => this.todoItem = el}
                /> 
            }/>
         ...   
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`addItem`将在表单提交时执行。然后，它简单地向状态添加一个项目。`new Date().valueOf()`是创建唯一 id 的基本方法。`this.todoItem.value`是从我们在`ItemsComponent`中创建的`inputRef`属性中创建的。你可以在[文档](https://reactjs.org/docs/refs-and-the-dom.html)中阅读更多关于参考文献的信息

现在让我们在`ItemsComponent.js`的表格中使用`addItem`和`inputRef`。

```
const ItemsComponent=({items, done, action, addItem, inputRef})=> {
    ...
    return (
      ...
       <form  onSubmit={addItem}>
          <input ref={inputRef} type="text" /> 
       </form>
       <ul className="items"> {lis} </ul>
       ...
    );                   
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将`input`节点作为引用附加到`inputRef`(通过 props 传递)。

## 结论

到目前为止，我们有一个基本的 react 应用程序，我们可以添加项目，将它们标记为完成，然后删除任何已完成的项目。我们还利用路由来区分这两者。

完成的项目可以在 [github](https://github.com/aurelkurtula/todo-app-with-react-and-firebase) 找到。每个教程我都有一个分支。

下一个教程将把 react 状态和 Firebase 数据库连接起来。