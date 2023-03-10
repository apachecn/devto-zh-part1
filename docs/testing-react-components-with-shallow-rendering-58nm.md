# 使用浅层渲染测试 React 组件

> 原文：<https://dev.to/farisj/testing-react-components-with-shallow-rendering-58nm>

脸书的 React 为开发者提供了一个轻量级的前端框架，该框架在所谓的组件中混合使用 Javascript 和 HTML 来创建视图。虽然 React 可以使用纯 Javascript 编写，但我们的团队使用 JSX 构建这些组件——我们的团队更喜欢它，因为它极大地提高了可读性，并且对性能没有影响，因为它在每次构建时都转换为纯 Javascript。虽然转换需要时间，但一个现代的 web 应用程序无论如何都需要一个构建步骤。

组件被设计成模块化和可重用的，这实际上使它们非常适合单元测试！这篇文章假设你对 React 有一定的了解，但是让我们看看两个 React 类相互交互的一个实例——一个简单的`TodoList`和一些`Todo` s.

```
import React from "react";

import Todo from "components/todo";

class TodoList extends React.Component{
  render(){
    var { todoTexts } = this.props;
    return (
      <div className="todo-list">
        <span className="greeting">Welcome. Here are your todos.</span>
        {
          todoTexts.map( (todo, index) => {
            return (<Todo key={ `todo-${index}` } text={ todo } />);
          })
        }
      </div>
      );
  }
}

TodoList.displayName = "TodoList";
export default TodoList; 
```

这个`TodoList`非常简单——它用一个简单的`span`问候用户，然后列出通过其父节点提供给它的尽可能多的`Todo`(让我们假设`Todo`的父节点正在联系一个商店或 API 或其他什么来收集 todos)。

现在，像优秀的开发人员一样，让我们建立一个测试套件来确保我们的`TodoList`完全按照我们的要求去做。

### 回车:TestUtils！

React 提供了一套很好的测试工具，允许我们检查和检验我们构建的组件。

有几种不同的用法，但是我想讨论一下**浅渲染**的用法。浅层渲染允许我们检查组件的`render`函数的结果，并查看特定组件返回的 HTML 和 React 组件。

所以，为了确保万无一失，让我们确保`TodoList`返回一个带有类“todo-list”的元素，它有一个问候用户的子元素(我们可以通过它有一个类“greeting”来猜测)，并且它将为所有给它的 todo 呈现`Todo`。

一个测试套件可能是这样的:

```
import chai from "chai";
import jsxChai from "jsx-chai";

import React from "react";
import ReactDOM from "react-dom";
import TestUtils from "react-addons-test-utils";
import shallowTestUtils from "react-shallow-testutils";

import TodoList from "app/components/todolist";
import Todo from "app/components/todo";

chai.use(jsxChai);

var expect = chai.expect;

describe("TodoList", function(){

  var renderer, todolist;

  beforeEach(function(){
    var todoTexts = ["eat breakfast", "have lunch", "go out to dinner"];
    renderer = TestUtils.createRenderer();

    renderer.render(
      <TodoList todoTexts={ todoTexts } />
    );

    todolist = renderer.getRenderOutput();
  });

  it("renders a todolist", function(){
    expect(todolist.props.className).to.equal("todo-list");
  });

  it("greets the user", function(){
    var greetingSpan = todolist.props.children[0];

    expect(greetingSpan.props.className).to.equal("greeting");
  });

  it("will render three todos", function(){
    var todos = shallowTestUtils.findAllWithType(todolist, Todo);
    expect(todos.length).to.equal(3);

    var dinnerTodo = todos[2];
    expect(dinnerTodo).to.deep.equal(
      <Todo key="todo-2" text="go out to dinner" />
      );
  });
}); 
```

哇，好吧，这是一个很大的信息。让我们一块一块地把它打碎。

#### 加载依赖项

我已经加载的依赖项如下:

```
import chai from "chai";
import jsxChai from "jsx-chai";

import React from "react";
import ReactDOM from "react-dom";
import TestUtils from "react-addons-test-utils";
import shallowTestUtils from "react-shallow-testutils";

import TodoList from "app/components/todolist";
import Todo from "app/components/todo";

chai.use(jsxChai);

var expect = chai.expect; 
```

让我们一件一件地检查这些。

为了构造 expect 语句，我使用 Chai 和 jsxChai。这些库将允许我们直接建立对 JSX 组件的期望，例如`expect(component).to.equal(<Todo />)`。

从 React 0.14.0 开始，React 的主要功能被分成两个依赖项，`React`和`ReactDOM`。前者由 React 所依赖的所有基本逻辑组成，后者为我们提供了虚拟 DOM，我们可以在其中呈现组件，并验证它们在测试中以我们想要的方式呈现。

`TestUtils`和`shallowTestUtils`是脸书自己提供的另外两个可选的 React 库。它们将成为主要的库，允许我们人工渲染我们的组件并检查它们的内部工作。

最后，我们正在测试`TodoList`，所以我们需要它，以及它的内部组件`Todo`，我们将在稍后的测试中使用它。

#### 设置

```
beforeEach(function(){
    var todoTexts = ["eat breakfast", "have lunch", "go out to dinner"];
    renderer = TestUtils.createRenderer();

    renderer.render(
      <TodoList todoTexts={ todoTexts } />
    );

    todolist = renderer.getRenderOutput();
  }); 
```

这个`beforeEach`只是为每个测试设置一个合适的测试环境。使用`TestUtils.createRenderer()`，它生成一个对象，该对象可以简单地呈现一个 React 组件，并返回被呈现的对象(在本例中是`todolist`)。由于我们事先声明了`todolist`和`renderer`，现在我们可以在测试范围内访问它们。

#### 测试 Todolist

```
it("renders a todolist", function(){
  expect(todolist.props.className).to.equal("todo-list");
}); 
```

如上所述，`todolist`变量是要渲染的对象。我们可以像对待其他 React 组件一样对待它，并检查它的 props——这里我们只是确保它接收到“todo-list”的`className`。

#### 测试二:问候语

```
it("greets the user", function(){
  var greetingSpan = todolist.props.children[0];

  expect(greetingSpan.props.className).to.equal("greeting");
}); 
```

正如这个测试所示，我们不仅可以检查呈现的组件本身，还可以检查它的子组件。因为孩子是通过道具通过 React 组件传递下来的，所以我们可以在那里获得孩子。这个测试只是获取呈现组件的第一个子组件，并确保它有一个“greeting”类。

#### 测试 Todos

```
it("will render three todos", function(){
  var todos = shallowTestUtils.findAllWithType(todolist, Todo);
  expect(todos.length).to.equal(3);

  var dinnerTodo = todos[2];
  expect(dinnerTodo).to.deep.equal(
    <Todo key="todo-2" text="go out to dinner" />
   );
  }); 
```

`findAllWithType`方法在超级离合器中出现。

它遍历半呈现的`TodoList`并返回第二个参数中传递的类型的所有子组件的数组。这样，我们可以确保有和我们期望的一样多的`Todo`(3，因为我们将 3 传递给了`TodoList`)，并且它们看起来和我们期望的一样。

### 但是等等，杰克！React 不是这样的吧？

我知道你现在可能在想:如果`renderer`呈现了包含`Todo`的`TodoList`，那么`Todo`不也应该被呈现吗？这难道不是一个递归的过程吗？

不会，因为我们用的是`shallowTestUtils`！这个工具允许我们只检查*组件本身的渲染输出，而不渲染所有的 React 子组件。这样，我们只需要检查正确的属性是否被传递给了子组件，而不需要担心它们的内部细节(尽管我们肯定应该做一个`Todo`测试！)*

[![asdf](img/e28c3b60bccbd5739eb09a4f41fb20b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kOIGmO5O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i.giphy.com/vh6B8BQizPBPW.gif)

很酷，对吧？

通过这种方式，React 组件变得非常易于单元测试——我们可以测试一个父组件，以确保它能够呈现所需的子组件，而不必查看在浏览器中加载该组件时呈现的整个 DOM 结构。这真的很有帮助，尤其是考虑到前端和用户模拟测试的复杂性。所以，带着你对 React 的浅层渲染的新知识，去测试吧！