# 了解演员模型

> 原文：<https://dev.to/roperzh/get-to-know-the-actor-model>

*本帖最初发表于[monades.roperzh.com](https://monades.roperzh.com/get-to-know-the-actor-model/)T3】*

Actor 模型是由 Carl Hewitt 在 70 年代提出的，作为处理并发计算的概念模型。从完整的编程语言到库和框架，有大量的[实现](actor-implementations);最受欢迎的是[二郎](https://www.erlang.org/) / [仙丹](https://elixir-lang.org/)和[阿卡](http://akka.io/)。每个实现都有自己的特殊性，但基本概念是相同的。

在这篇文章中，您将构建一个实现 Actor 模型的 JavaScript 库，虽然上面提到的大多数实现都更加健壮，但这篇文章的目的是探索模型的基础并思考可能的用例。

## 背景

在 actor 模型中，Actor 是构建应用程序结构的基础，它具有对外部世界不可见的内部状态，并通过异步消息与其他 Actor 交互。

如果这听起来很像面向对象编程(OOP)，那么你是对的。Actor 模型可以认为是对消息进行特殊处理的 OOP:消息由接收者异步地*交付*和同步地*执行*。

每个演员都有一个唯一的地址,通过这个地址你可以向它发送消息。当一个消息被处理时，它被匹配到参与者的当前**行为**；它只不过是一个函数，定义了对消息做出反应时要采取的动作。作为对消息的响应，行动者可以:

*   创造更多的演员。
*   向其他参与者发送消息。
*   指定内部状态来处理下一条消息。

## 实现

记住基本概念后，看一下如何使用该库可能是个好主意:

```
import Actor from "actor-model";

// Define a behavior to handle messages
const counter = {
  // Define the initial state of the actor
  init() {
    return { count: 0 };
  },

  // Define methods to be invoked in response
  // to messages

  incrementBy(state, { number }) {
    let count = state.count + number;
    return { count };
  },

  logTotal(state) {
    console.log(state.count);
  }
};

// Initialize an actor with the `counter` behavior
const address = Actor.start(counter);

// Log the initial state
Actor.send(address, ["logTotal"]); // => { count: 0 }

// Increment the counter by 2
Actor.send(address, ["incrementBy", { number: 2 }]);

// Log the current state
Actor.send(address, ["logTotal"]); // => { count: 2 } 
```

## 消息传递系统

如前所述，消息是异步发送的，但是一旦它们到达目的地，就会被同步排队和处理。

这种机制可以被认为是 FIFO(先进先出)队列，这就是 JavaScript [事件循环](node-event-loop)的工作方式。该库利用了这一点，将事件循环变成了消息传递系统的例行工作。

这篇文章使用了 node.js [事件系统](https://nodejs.org/api/events.html)，因为这个 API 没有 Web APIs 那么冗长，作为复习，这段代码的要点展示了`events`是如何工作的:

```
import EventEmitter from "events";

const mailbox = new EventEmitter();

// Binding listeners to custom events
mailbox.on("channel-name", message => {
  console.log(message);
});

// Firing custom events: the second argument
// is sent to the callback function
mailbox.emit("channel-name", "hello!"); 
```

## 创建演员

> E.O .威尔逊有句名言“一只蚂蚁不是一只蚂蚁”，对吧？嗯，一个演员不是演员，他们是成体系的！—卡尔·休伊特

首先要定义的是`start`函数。这个函数负责创建一个新的 actor 并返回一个地址，其他 actor 将消息发送到这个地址。

为了生成这个唯一的地址，库利用了 [`Symbol()`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Symbol) 函数，该函数在每次被调用时返回一个唯一的值。目前，`start`的第一个实现只是返回一个地址:

```
const Actor = {
  start() {
    return Symbol();
  }
};

const address = Actor.start();
console.log(address); //=> Symbol() 
```

## 消息传递

虽然[消息传递系统](#messaging-system)的幕后是由事件循环处理的，但是库仍然需要提供一种清晰的方式来将消息发送到特定的参与者地址。

一种方便的方法是使用参与者地址(`Symbol`原语)作为事件名:

```
const mailbox = new EventEmitter();

const Actor = {
  start() {
    const address = Symbol();

    // Use the address as the event name
    mailbox.on(address, function() {});

    return address;
  }
}; 
```

actor 现在可以接收消息，但是系统缺少发送消息的标准方式，这就是`send`函数发挥作用的地方。这个函数接收一个参与者地址、一条消息，并使用`events` API 发出一个事件。

```
const Actor = {
  //...

  send(target, message) {
    mailbox.emit(target, message);
  }
}; 
```

## 内部状态

当消息到来时，参与者希望指定内部状态来处理下一个即将到来的消息。一种可能的方法是利用闭包:在调用`start`时定义一个初始状态，并在消息传入回调函数时更新它:

```
const Actor = {
  start() {
    const address = Symbol();

    // Define an initial state
    let state = {};

    mailbox.on(address, function(message) {
      // Do something based on `message` and
      // update the state (assuming `newState`
      // has been defined somewhere in this
      // function)
      state = newState;
    });

    return address;
  }

  //...
}; 
```

## 行为

有了系统的基础，最后一步是提供定义定制行为来处理消息的能力。在行动者模型中，您通过行为来实现这一点。

对于库来说，行为是公开方法的对象。为了方便起见，这种行为必须遵循一定的规则:

*   要设置初始状态，行为必须实现一个名为`init`的函数，该函数返回初始状态。
*   要定义处理以下消息的状态，调用的方法必须返回一个用作下一个状态的值。
*   当一个参与者向另一个参与者发送消息时，它必须提供一个“元组”，其中包含需要执行的方法的名称和可选数据。
*   当被调用时，行为中的每个方法都接收一个代表当前状态的`state`参数和一个可选的包含数据的第二个参数。

就代码而言，这意味着:

```
const Actor = {
  start(behavior) {
    const address = Symbol();
    // Set the initial state, if the behavior defines an init function invoke
    // it, otherwhise set the state to an empty object.
    let state = typeof behavior.init === "function" ? behavior.init() : {};

    // Desconstruct the data provided by the caller
    mailbox.on(address, function([method, message]) {
      // Call the required method on the behavior, if the method returns
      // a value, use it to define the next state.
      state = behavior[method](state, message) || state;
    });

    return address;
  }
  //...
}; 
```

## 把所有的东西放在一起

该库的第一个最终版本如下所示:

```
import EventEmitter from "events";

const mailbox = new EventEmitter();

const Actor = {
  start(behavior) {
    const address = Symbol();
    let state = typeof behavior.init === "function" ? behavior.init() : {};

    mailbox.on(address, function([method, message]) {
      state = behavior[method](state, message) || state;
    });

    return address;
  },

  send(target, message) {
    mailbox.emit(target, message);
  }
};

export default Actor; 
```

它提供了使用 Actor 模型所需的最基本的功能。虽然我不建议任何人用它来构建一个真正的应用程序，但希望它能很好地介绍这个模型是如何工作的。

没有什么是完美的，在这种实现的许多潜在问题中，我想强调的是:

*   一个 actor 系统可能包含无限数量的 actor，因此它们必须尽可能的轻量级。知道了这一点，闭包似乎不是保持状态的最有效的方式。
*   在 JavaScript 中很容易搞砸不变性，如果库的用户不是非常小心的话，actor 的内部状态可以从外部修改。

这个帖子到此为止！一如既往，有任何问题/批评/顾虑，请随时联系我！我在 Twitter 上是 [@roperzh](twitter) ，我的[电子邮件](email)一直是开着的。