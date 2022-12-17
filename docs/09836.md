# 面向对象的函数式编程或者你如何使用类作为 redux-reducer

> 原文：<https://dev.to/ustun/object-oriented-functional-programming-or-how-can-you-use-classes-as-reduxreducers>

*注:本文原载 [Ustun Ozgur 在 Medium](https://medium.com/@ustunozgur/object-oriented-functional-programming-or-how-can-you-use-classes-as-redux-reducers-23462a5cae85#.815k9536e) 上的博客*。

TL；你可以使用 ImmutableJS 记录类和 Redux reducers 方法，结合 FP 和 OOP 的优点。
看这里最后的结果:[https://gist . github . com/us tun/f 55 DC 03 ff 3 f 7 A0 c 169 c 517 b 459d 59810](https://gist.github.com/ustun/f55dc03ff3f7a0c169c517b459d59810)

在过去的十年里，函数式编程一直在稳步获得流行，而面向对象编程受到越来越多的质疑。名词王国现在正受到动词王国
的威胁，我们可以看到这场革命在 Rich
Hickey 的《让简单变得容易》中得到最好的解释。

在 JavaScript 前端生态系统中，React 打破了最后一个功能边界，UI 开发和来自功能世界的想法，如不变性，高阶函数现在在行业中变得很普遍。

面向对象程序和
函数式程序之间的主要区别是它们在处理数据和
状态上的立场。对象本质上封装数据，而在函数式
程序中，数据通常与代码分离。一个额外的
重要区别是，大多数 OOP 系统还包含身份
跟踪，即一个对象不仅是其状态(数据)
和方法(或 FP 世界中的函数)的总和，还是身份。

所以，

*   开箱即用的 OOP 给了你身份+状态+方法。
*   FP 开箱给你数据+功能。

追踪身份是留给读者的练习，这是一个
祝福也是一个诅咒；作为多家
公司的顾问和培训师，人们在
转变范式时面临的最大困惑来源。

退耦

分析大系统的基本思想是解耦和分层。当面对状态时，函数式编程基本上会问下面这个问题:如果我们把这三个概念，
状态、身份和方法解耦会怎么样？

优点是这些不同的部件可以单独构造和组装。缺点是你冒着失去抽象的内聚性的风险。

1.  函数和方法

让我们从方法开始举例。大多数类充当
方法的包，所以如果你有几个方法，你可以
实际上把它们作为不同的函数，把被操作的主数据
作为第一个参数。实际上，thing.doIt()变成了 doIt(thing)。

这样的函数显然可以接受额外的参数，然而大多数情况下，在遵循
域模型模式的业务应用程序设置中，函数的第一个参数将是我们正在操作的
域模型。

随着函数数量的增加，你的程序面临着被大量分散的函数填满的危险。FP
语言在这里没有给出太多的指导，实际上你可以自由地
做任何你喜欢的事情。又是祝福又是诅咒。

在面向对象的世界里，函数放在哪里是非常明确的；例如，在不太灵活的语言如 Java(Java 8 之前)中，
函数属于类。

然而，在更灵活的语言中，比如 JavaScript，我们可以将与数据结构相关的函数收集到一个模块或一个对象中。

例如，如果我们有 3 个不同的函数对像 Person 这样的数据
结构进行操作，我们可以收集三个对
Person 数据进行操作的函数，如下所示:

person functions = {
doThis(person，β…){β…}
doThat(person，β…){β…}
doBar(person，β…){β…}
}

这有效地解决了解耦过程的第三部分，即处理方法的放置。

这里的另一个选择是创建一个 JS 模块(实际上是一个文件
，它在顶层有这些函数，如下所示:
in person _ functions . JS
function doThis(person，β…){β…}
function doThat(person，β…){β…}
函数 doBar(人，β…){β…}

(例如，在 Clojure 这样的语言中，等价的做法是将这些函数放到名称空间中。)

1.  状态、数据和身份

如前所述，函数式程序有效地分离了状态
(数据)和身份。大多数 OOP 系统就地操作数据，
,而功能对等体需要显式地处理数据的输入和
输出。因此，在 OOP 中，`this`关键字为函数式程序中的以下 3 个步骤提供了便利:

a --获取数据= >状态为数据
b --转换数据=>some _ function(data)
c --将数据放在获取数据的位置。= >状态=某些 _ 功能(数据)

在 OOP 世界中，如果你访问这个关键字指向的东西的状态，步骤 a 和 c 是自动的。这是这里的主要解耦，OOP 采取的立场是，大多数时候，你将把数据从你拿回的地方放回去，而 FP 采取的立场是，这三个步骤可以解耦。

如果你想在 FP 系统中追踪身份，你必须手动完成
，尽管这并不像听起来那么费力。

例如，Clojure 提供了原子，这实际上更类似于 Java 或 JavaScript 中的对象；其中包含了纯数据。

任何在 atom 上操作的函数调用都有效地向内部对象发送相同的调用，并将输出对象写回。

假设我们有一个封装了一些数据的原子。

my _ object = atom(data)
swap(my _ object，some_function)

实际上变成了三个操作:

1-从对象中提取数据。2-对数据执行一些功能。
3-将数据写回对象。

因此，如果添加身份跟踪，FP 系统
相当于 OOP 系统。

Redux

这就是 Redux 的用武之地。Redux 基本上标榜为“一个
状态容器”，将你的数据(状态)包装在一个对象
(存储)中。你做的任何转换都是一个转换功能
，叫做“减速器”。

尽管排除了像状态包含和减少
操作这样的花哨术语，这实际上正是 OOP 所提供的。OOP
为你的数据提供了一个容器，并提供了一些方法
(相当于函数，reducers)来操作那些数据，并在转换完成后将结果
放回原处。
因此，Redux reducers 等同于传统的面向对象
编程，有以下两点不同:

1-默认情况下它不提供调度，所以您必须使用 if/else/switch 来选择要操作的方法。所有的数据都被建模为不可变的数据结构。

因此，显而易见的问题是:我们能不能鱼与熊掌兼得？

也就是说，精通对象建模的人如何在 Redux 应用程序中重用他的
技能？

必须的 TodoÂ App

让我们考虑一下 TodoApp(一个缩减器)的如下转换函数。基本的领域建模如下:

*   您可以添加、删除待办事项，切换待办事项的完成状态，以及添加一个临时待办事项文本，该文本将在用户按提交时添加。我将只实现 REMOVE_TODOS，这样代码就简洁了。

```
function todoAppReducer(state={todos:[], newTodo: â€˜'}, action) {
    switch (action.type) {
    case â€˜REMOVE_TODO':
            return {…state, todos: state.todos.filter(todo=>todo.description!= action.payload.description)}
    case â€˜ADD_TODO':
    case â€˜TOGGLE_TODO':
    case â€˜ADD_TEMP_TODO':
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

第一次重构的结果如下，其中我们用方法的对象包替换了分派函数。

```
function todoAppReducer(state={todos:[], newTodo: â€˜'}, action) {
    methods = {
    REMOVE_TODO: function (payload) return {…state, todos: state.todos.filter(todo=>todo.description != payload.description)},
    ADD_TODO: function () …,
    TOGGLE_TODO: function () …,
    ADD_TEMP_TODO: function ()
    }

    return methods[action.type](action.payload)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，由于 methods object 中的函数在 main 函数内部，所以它们都可以访问名为 state 的变量。如果我们将方法对象从这些对象中取出，我们必须显式地传递状态。

```
methods = {
    REMOVE_TODO: function (state, payload) return {…state, todos: state.todos.filter(todo=>todo.description != payload.description)},
    ADD_TODO: function (state, payload) …,
    TOGGLE_TODO: function (state, payload) …,
    ADD_TEMP_TODO: function (state, payload)
}

function todoAppReducer(state={todos:[], newTodo: â€˜'}, action) {
    return methods[action.type](state, action.payload)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，object literal 方法开始看起来更像一个传统的对象包，一个类。首先，让我们将它们移动到一个
适当的类中，这里我们暂时不使用它。实际上，
这是一类静态方法，将“状态”作为第一个变量。

```
class Todo {
     REMOVE_TODO(state, payload) {
     return {…state, todos: state.todos.filter(todo=>todo.description != payload.description)};
    }
    ADD_TODO(state, payload) {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个阶段，我们几乎处于 FP 和 OOP 的中间。精神上更接近 FP，外观上更接近 OOP。不可变值的生成非常难看，使用了 spread 操作符和各种技巧，会让大多数新手感到厌烦。
进入 ImmutableJS 库，让这些转换变得很自然。获得一个不可变对象的新版本，除了一个字段保持不变，就像设置那个字段一样简单。
例如，假设我们有一个对象 A，想要获取对象 B，但是名称设置为 foo。

B = A.set(â€˜name', â€˜foo')

实际上，作为一名 OOP 程序员，您可以将 ImmutableJS 视为您当前对象的一个克隆，而无需定义克隆操作和设置不同的值。
想要与对象 A 中的相同，但名为“foo ”,姓为“bar ”?
您可以通过连续设置这些选项来实现:

a . set(name，foo)。集合(姓，栏)

或者在一个步骤中合并第二个对象，如:

a . merge({ name:foo，surname:bar ' })

因此，将我们之前的类转换为使用 ImmutableJs，我们得到了下面的结果:

```
class Todo {

    REMOVE_TODO(state, payload) {
    return state.set(â€˜todos', state.todos.filter(todo=>todo.get(â€˜description') != payload.description));
    }

    ADD_TODO(state, payload) {
    }
}

function todoAppReducer(state=Immutable.fromJS({todos:[], newTodo: â€˜'}), action) {
    return Todo[action.type](state, action.payload)
} 
```

Enter fullscreen mode Exit fullscreen mode

你会看到我们仍然在显式地传递状态，而我们只是在 OOP 应用程序中使用它来显式地传递状态。输入不可变的记录，这给了你两个世界最好的东西，你可以在这里定义操作这些记录的方法。
让我们转换 Todo 类以利用不可变记录。

```
class Todo extends Immutable.Record({todos:Immutable.List(), newTodo: â€˜'}){
    REMOVE_TODO(payload) {
    return this.set(â€˜todos', state.todos.filter(todo=>todo.get(â€˜description')!= payload.description));
    }

    ADD_TODO(payload) {

    }
}

function todoAppReducer(state=new Todo(), action) {
    return state[action.type](action.payload)
} 
```

Enter fullscreen mode Exit fullscreen mode

明白我们的目的了吗？只剩下几个修饰步骤了。

1-如何处理我们不认识的方法？在 JS 中，这很容易，我们可以访问适当的状态[action.type]并检查它是否是一个函数。

2-丑陋的方法名:在 Redux 应用程序中，事件名通常是常量大小写，我们希望它们是 camelCames。由于 lodash.camelcase，这种转变很容易。

现在，让我们提取一个不可变的记录类，并产生一个兼容的 Redux reducer。

```
class Todo extends Immutable.Record({todos:Immutable.List(), newTodo: ''}) {

    removeTodo(payload) {
    return this.set(â€˜todos', state.todos.filter(todo=>todo.get(â€˜description')!= payload.description));
    }

    addTodo(payload) {
    }
}
function todoAppReducer(state=new Todo(), action) {
    var fn = state[camelcase(action.type)]
    if (fn) {
    return state[camelcase(action.payload)](action)
    } else {
    // we don't recognize the method, return current state.
    return state;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最终产品:
你可以在 Github 上的[这里得到这个图案的最终版本](https://gist.github.com/ustun/a1b441d54894117e8b172065d14a849a) 

```
var camelCase = require('lodash.camelcase');
const {Map, Record, List} = require('immutable');

class Todo extends Record({ description: null, completed: false }) {
    toggle() {
        return this.set('completed', !this.completed);
    }
}

const InitialTodoApp = Record({
    newTodo: '',
    todos: List(),
    activeFilter: ''
});

class TodoApp extends InitialTodoApp {

    init(data) {
        return this.merge(data);
    }

    // action methods: kind of like IBActions

    setTempTextAction({value}) {
        return this.setNewTodo(value);
    }

    removeTodoAction({description}) {
        return this.removeTodo(description);
    }

    addTodoAction() {
        return this.addTodo();
    }

    // other methods

    setNewTodo(newTodo) {
        return this.set('newTodo', newTodo);
    }

    addTodo() {
        return this.addTodoFromDescription(this.newTodo).resetNewTodo();
    }

    resetNewTodo() {
        return this.set('newTodo', '');
    }

    addTodoFromDescription(description) {
        const newTodos = this.todos.push(new Todo({ description: description }));
        return this.setTodos(newTodos);
    }

    removeTodo(description) {
        const newTodos = this.todos.filter(todo => todo.description != description);
        return this.setTodos(newTodos);
    }

    setTodos(todos) {
        return this.set('todos', todos);
    }

    setTodosFromJS(todosJS) {
        const todos = todosJS.map(todoJS => new Todo(todoJS));
        return this.setTodos(todos);
    }

    incompleteTodos() {
        return this.todos.filter(todo => !todo.completed);
    }

    nIncompleteTodos() {
        return this.incompleteTodos().length;
    }

    completeTodos() {
        return this.todos.filter(todo => todo.completed);
    }

    nCompleteTodos() {
        return this.completeTodos().length;
    }

    allTodos() {
        return this.todos;
    }

    toggleTodo({description}) {
        var newTodos = this.todos.map(todo => todo.description != description ? todo : todo.toggle())
        return this.setTodos(newTodos);
    }

    describe() {
        console.log(JSON.stringify(this.toJS(), null, 4));
        console.log("incomplete todos", this.nIncompleteTodos());
    }
}

function reducerFromRecordClass(klass) {
    return function (state = new klass(), action) {
        var fn = state[camelCase(action.type + '_ACTION')];
        if (fn) {
            return state[camelCase(action.type + '_ACTION')](action);
        } else {
            if (state[camelCase(action.type)]) {
                console.warn('You tried to call an action method, but no such action method provided.', action.type)
            }
            return state;
        }

    }
}

const todoAppReducer = reducerFromRecordClass(TodoApp);

export default todoAppReducer;
// main(); 
```

Enter fullscreen mode Exit fullscreen mode

与传统的 OOP 应用程序相比，我们可以观察到一些情况:

1-所有 setters 都必须返回一个新对象。2-身份追踪由 redux 完成。
3- Redux 动作的后缀是“action ”(这是完全可选的，只是提供给通过 Redux 从普通方法调用的独立方法。Redux 方法只是委托给普通的类方法。)

除此之外，它几乎是函数式和面向对象世界中最好的。不像大多数 Redux 应用程序在一个叫做“状态”的 amorph、未命名的
数据结构上操作，我们有一个真正的域模型来减轻
我们的精神数据抽象能力。我们也可以在其他地方轻松重用这个模型
，甚至使用其他 OOP 技术，比如继承来
派生新的类。

与大多数 OOP 应用程序不同，它像 FP
一样对不可变数据进行操作，因此解决了状态和身份之间的紧密耦合。
在这个特定的实例中，身份跟踪留给 Redux，但是像 Clojure atom 这样的
简单有状态包装器将为您带来 OOP 的
身份跟踪优势。

致谢:

感谢来自 T2 Yazilim 的 Ahmet Akilli 向我介绍了 JumpState，它基本上实现了相同的思想，但没有使用不可变的记录。更多讨论见此:[https://medium . com/@ machnicki/why-redux-is-not-so-easy-some-alternatives-24816 D5 ad 22d # . 912 ks 1 hij](https://medium.com/@machnicki/why-redux-is-not-so-easy-some-alternatives-24816d5ad22d#.912ks1hij)

结论

我希望这篇文章能为您在开发应用程序时利用混合范例提供指导。我们相信 FP 和 OOP 范例可以共存来构建强大的产品。

如果你需要帮助、咨询和培训，请随时给我们打电话。tech([contact @摩天楼. tech](mailto:contact@skyscraper.tech) )我们很乐意为您提供帮助。
我们提供咨询服务，领导团队，同时
编写代码。我们还提供框架，以便我们客户的现有团队可以在良好的基础上继续发展。

我们支持许多平台，从 Django 到 nodejs 再到 Clojure 应用，这取决于需求。我们也主要在 JavaScript(后端和前端)和我们支持的其他
平台上提供培训
。

更多信息，请参见[http://摩天楼. tech](http://skyscraper.tech) 。【https://news.ycombinator.com/item?id=13578656】在 HackerNews 上讨论这篇文章: