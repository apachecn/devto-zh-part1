# Elixir GenServer 向 Redux 开发者解释

> 原文：<https://dev.to/juliendemangeon/elixir-genserver-explained-to-redux-developers-1foh>

注意:这个帖子最初发布在[marmelab.com](https://marmelab.com/blog/2017/10/04/elixir-genserver-explained-to-redux-developers.html)。

虽然还很年轻，但自从诞生以来，仙丹语言已经广受欢迎。鉴于基于一种叫做 [Erlang](https://en.wikipedia.org/wiki/Erlang_(programming_language)) [BEAM](https://stackoverflow.com/questions/16779162/what-kind-of-virtual-machine-is-beam-the-erlang-vm?answertab=votes#tab-top) 的古老语言/平台，Elixir 提供了强大的功能、并发和分布式能力。感觉既现代又有趣。这就是为什么我无法抗拒玩它。

在这篇文章中，我将给出帮助我理解 GenServer 的关键，GenServer 是许多 Elixir 分布式应用程序/系统的基石。

## GenServer 瑞士刀

像任何其他建立在 Erlang VM 之上的语言一样，Elixir 依赖于轻量级的隔离线程(通常称为[进程](https://elixir-lang.org/getting-started/processes.html))以及这些线程之间传递的消息。这种机制的主要优点是容错性、分布性和可伸缩性。

[gen_server](http://erlang.org/doc/design_principles/gen_server_concepts.html) (通用服务器的意思)是底层 [Erlang OTP](http://learnyousomeerlang.com/what-is-otp) (开放电信平台)中包含的标准模块。它提供了构建一个具有公共共享资源(状态)的基本客户端< - >服务器系统的抽象。它被用于 Elixir 社区的许多开源项目中。

GenServer 通常充当一个有自己状态的中央注册中心，我们可以通过消息传递在其上执行(a)同步操作。在 web 开发领域，它可以粗略地比作一个异步的 [Redux](http://redux.js.org/) 存储。

那么它和 Redux 相比怎么样呢？在下面的例子中，我将创建一个简单的引用注册表。我将使用 GenServer 和 Redux 来展示它们的共同点。

## 仙丹版

下面是如何用 GenServer 实现报价注册表:

```
# quotes_list.ex

defmodule QuotesList do
    use GenServer

    # Client API

    def start_link do
        GenServer.start_link(__MODULE__, :ok, [])
    end

    def read(pid) do
        GenServer.call(pid, {:read})
    end

    def add(pid, quote) do
        GenServer.cast(pid, {:add, quote})
    end

    # Server Callbacks

    def init(:ok) do
        {:ok, []}
    end

    def handle_call({:read}, from, list) do
        {:reply, list, list}
    end

    def handle_cast({:add, quote}, list) do
        {:noreply, list ++ [quote]}
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个代码片段中，我定义了一个名为`QuotesList`的 Elixir 模块。由于`GenServer`来自一个底层的 Erlang 库，所以它是通过一个带有某种“mixin”的 Elixir 桥提供的(这要感谢[“use”](https://elixir-lang.org/getting-started/alias-require-and-import.html#use)宏)。

在 Elixir 世界中，`start_link`是启动一个流程的常用方法名，该流程附加到当前的[监督树](https://elixir-lang.org/getting-started/mix-otp/supervisor-and-application.html)。该方法的第三个参数表示 QuotesList 模块的初始状态(一个空数组)。

**客户端 API** 部分可以定义为我们模块的公共接口。我们可以称之为`read`、`add`、...在我们方便的时候操纵或进入我们的商店。

另一方面，**服务器回调**部分负责 GenServer 的内部工作状态操作。由于 Elixir 强大的[模式匹配](https://elixir-lang.org/getting-started/pattern-matching.html)功能，它分别响应客户端 API 部分中的不同调用/转换。在下一章，我将更深入地讨论这一部分。

在结束关于 Elixir 实现的这一部分时，这里有一个我们的`QuotesList`模块使用 GenServer 的例子。

```
# another_module.ex

# Start the quote server
{:ok, pid} = QuotesList.start_link

# Adding some items
QuotesList.add(pid, "All that glitters is not gold.")
QuotesList.add(pid, "Elementary, my dear Watson.")
QuotesList.add(pid, "Houston, we have a problem.")

# Read the quotes back
QuotesList.read(pid) 
```

Enter fullscreen mode Exit fullscreen mode

## Redux 版本

在 Redux 方面，这里相当于前面的例子中的 [ES6](https://en.wikipedia.org/wiki/ECMAScript#6th_Edition_-_ECMAScript_2015) 。

```
// quoteListStore.js

import { createStore } from 'redux'

function quoteListReducer(state = [], action) {
    switch (action.type) {
    case 'add':
        return [ ...state, action.quote ];
    default:
        return state;
    }
}

return createStore(quoteListReducer) 
```

Enter fullscreen mode Exit fullscreen mode

与 Elixir 实现相反，在 Redux 中不需要创建“read”方法。我们可以用商店上的 [getState](http://redux.js.org/docs/api/Store.html#getState) 直接访问状态值。

此外，初始状态被直接定义到[缩减器](http://redux.js.org/docs/basics/Reducers.html)的默认值中，而不是在 [createStore](http://redux.js.org/docs/api/createStore.html) 初始化器中。

Elixir 依靠 [atom](https://elixir-lang.org/getting-started/basic-types.html#atoms) 标签为我们的处理程序带来匹配功能。在 EcmasScript 中，我们使用一个`type`属性来匹配 reducer 开关的大小写。

在 Elixir 中，需要通过从另一个进程传递消息来访问数据。在单线程的 EcmaScript [EventLoop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop) 中不需要。

下面是如何在 ES 中使用 Redux 注册表:

```
// index.js

import quoteListStore from './quoteListStore.js';

const addQuote = item => ({ type: 'add', quote });

quoteListStore.dispatch(addQuote('All that glitters is not gold.'));
quoteListStore.dispatch(addQuote('Elementary, my dear Watson.'));
quoteListStore.dispatch(addQuote('Houston, we have a problem.'));

quoteListStore.getState(); 
```

Enter fullscreen mode Exit fullscreen mode

## 呼叫，投&信息回调

如果你以前从未遇到过 GenServer，你肯定问过自己`cast`和`call`的含义。这些“动词”对应于可以被触发的两种不同类型的动作。

`cast`动作由对存储的异步动作组成，对结果没有任何期望。这意味着该动作已经排队，准备由相应的 GenServer `handle_cast`处理程序尽快处理。这类似于 Redux 中著名的[调度](http://redux.js.org/docs/api/Store.html#dispatch)方法。

另一方面，`call`动作由对商店的同步动作组成。调用过程暂停，直到相应的 GenServer `handle_call`处理程序处理该动作。它最常用于访问数据。

另一个`info`动作存在，但通常不被使用。当传入消息来自未知来源/进程(不是客户端 API 部分)时，将触发此操作。相应的`handle_info`可以比作某种“包罗万象”的处理程序(Redux reducer `switch / case`中的`default`案例)。

## 结论

如果让我比较 Redux 和 Elixir GenServer，我会说:

*   GenServer！= Redux 因为每个运行平台的性质。Erlang BEAM / Elixir 是分发的，EcmaScript 不是。
*   GenServer 通过调用提供了类似于 [RPC](https://en.wikipedia.org/wiki/Remote_procedure_call) 的功能。Redux 没有。
*   当 Redux 的唯一目的是操纵“状态”时，GenServer 就不是这样了。
*   Redux reducer 逻辑是集中式的，GenServer 逻辑被拆分成多个`handle_*`方法。

您可能还会对下面的相关链接感兴趣，以便更深入地了解 Elixir 和 GenServer。

*   [https://hexdocs.pm/elixir/GenServer.html](https://hexdocs.pm/elixir/GenServer.html)
*   [http://cult TT . com/2016/08/24/understanding-gen server-elixir/](http://culttt.com/2016/08/24/understanding-genserver-elixir/)
*   [https://medium . com/blackode/when-and-where-to-use-cast-cal-info-messages-in-elixir-Erlang-gen server-9 BAF 937 b 6494](https://medium.com/blackode/when-and-where-to-use-cast-cal-info-messages-in-elixir-erlang-genserver-9baf937b6494)