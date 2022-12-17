# 解决游戏状态机循环依赖性

> 原文：<https://dev.to/technocoder/resolving-the-game-state-machine-cyclic-dependency-26m>

如果你已经制作(或试图制作)了一段时间的游戏，很可能你已经见过并使用过状态机。下面是 C++中的一个最小实现:

```
class State {
    StateMachine* stateMachine;
public:
    virtual void onEnter() {}
    virtual void onExit() {}
}

class StateMachine {
    std::stack<std::unique_ptr<State>> states;
public:
    void push_state(std::unique_ptr<State> state) {
        states.top()->onExit();
        states.push(std::move(state));
        states.top()->onEnter();
    }
    void pop_state() {
        states.top()->onExit();
        states.pop();
        states.top()->onEnter();
    }
    void change_state(std::unique_ptr<State> state) {
        states.top()->onExit();
        states.pop();
        states.push(std::move(state));
        states.top()->onEnter();
    }
    State& peek() {
        return *states.top();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

State 类中的指针允许自己访问 StateMachine 类中的方法。这在游戏开发中特别有用。

假设你正在设计一个平台游戏。你可以有一个主菜单状态，一个中间状态和一个设置状态。为了让 MainMenu 状态能够转换到 InLevel 状态，它需要访问 change_state 方法。

问题是我们在这里有一个循环依赖！StateMachine 需要了解状态，以便调用它的 onExit 和 onEnter 方法(以及更多)。State 需要了解 StateMachine，这样它才能改变当前的状态。

在 C++中运行得足够好，但是在 Rust(这是我正在创建的游戏)中造成了一个巨大的问题。参见下面的代码:

```
pub trait State {
    fn event(&mut self);
    fn update(&mut self, state_manager: &mut StateManager);
    fn draw(&self);
}

pub struct MainMenuState {}

impl State for MainMenuState {
    fn event(&mut self) {}
    fn update(&mut self, state_manager: &mut StateManager) {}
    fn draw(&self) {}
}

pub struct StateManager {
    states: Vec<Box<State>>
}

impl StateManager {
    pub fn new(initial_state: Box<State>) -> StateManager {
        StateManager {
            states: vec![initial_state],
        }
    }

    pub fn peek(&mut self) -> &mut Box<State> {
        return self.states.last_mut().expect("StateManager is empty");
    }
}

fn main() {
    let mut state_machine = StateManager::new(Box::new(MainMenuState{}));
    state_machine.peek().update(&mut state_machine);
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我在 Rust 中复制状态机的尝试。如果我们编译这段代码，我们会得到下面的错误:

```
error[E0499]: cannot borrow `state_machine` as mutable more than once at a time
  --> src/main.rs:33:38
   |
33 |     state_machine.peek().update(&mut state_machine);
   |     -------------                    ^^^^^^^^^^^^^- first borrow ends here
   |     |                                |
   |     |                                second mutable borrow occurs here
   |     first mutable borrow occurs here 
```

Enter fullscreen mode Exit fullscreen mode

我只想指出 Rust 的错误信息有多棒。没有我在 C++编译器中发现的那些神秘的东西。

如果你对 Rust 不太熟悉，这里是正在发生的事情的要点(不，不是 Github 要点)。Rust 只允许一段代码同时修改同一个变量(可变借用)。这是为了在编译时发现并防止数据竞争。当我们使用 StateManager 中的 peek 方法和 update 方法(它修改给定的 StateManager)时，我们修改 state_machine 变量。

在这一点上，我决定改变代码的结构，而不是“对抗借用检查器”。

不知何故，我需要一种方法来通知 StatesManager 改变它的状态，而不直接这样做。这意味着负责通知 StatesManager 的代码不能是状态结构。

为此，我让 State struct 返回一个 Rust enum，表示它是否想要改变当前状态。然后，StateManager 将根据枚举的值更改状态。

Rust 使这变得特别容易，因为您可以将任何值“附加”到任何枚举。见下文:

```
pub enum StatesRequest {
    PushState(Box<State>),
    ChangeState(Box<State>),
    PopState,
    None,
}

pub trait State {
    fn event(&mut self);
    fn update(&mut self) -> StatesRequest;
    fn draw(&self);
}

fn main() {
        let mut state_manager = StateManager::new(Box::new(MainMenu{}));
        let request_status = state_manager.peek().update();
} 
```

Enter fullscreen mode Exit fullscreen mode

现在 State 不需要知道任何关于 StateManager 的信息。它唯一的依赖项是 StatesRequest 枚举。每当它想要改变状态时，它只需要创建一个 StatesRequest 的新实例，并将状态添加到 enum 中。

```
// Inside the MainMenuState impl
fn update(&mut self) -> StatesRequest {
        if userPressedStartGame {
            return StatesRequest::ChangeState(Box::new(InLevel{}));
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是修改后的代码的剩余部分:

```
impl StateManager {
    pub fn new(initial_state: Box<State>) -> StateManager {
        StateManager {
            states: vec![initial_state],
        }
    }

    pub fn update_states(&mut self, states_status: StatesRequest) {
        match states_status {
            StatesRequest::PushState(state) => {
                self.states.push(state)
            },
            StatesRequest::ChangeState(state) => {
                self.states.pop().expect("StateManager is empty");
                self.states.push(state);
            },
            StatesRequest::PopState => {
                self.states.pop().expect("StateManager is empty");
            },
            StatesRequest::None => ()
        }
    }

    pub fn peek(&mut self) -> &mut Box<State> {
        self.states.last_mut().expect("StateManager is empty")
    }
}

fn main() {
    let mut state_manager = StateManager::new(Box::new(MainMenu{}));

    // The following code would be in some sort of loop
    let request_status = state_manager.peek().update();
    state_manager.update_states(request_status);
} 
```

Enter fullscreen mode Exit fullscreen mode

万岁！没有更多的编译错误，我们消除了循环依赖。

这在 Rust 中很容易做到(因为它的枚举很棒),但是我们能在 C++中做到吗？

绝对的。不过看起来确实略有不同:

```
struct StatesRequest {
    std::unique_ptr<State> new_state;
    enum Type {
        PUSH,
        POP,
        CHANGE,
        NONE
    } type;
} 
```

Enter fullscreen mode Exit fullscreen mode

(由于生锈，我开始有点喜欢蛇包而不是骆驼)

如果你有任何问题，请随时提问。我会尽力回答这些问题，但无论如何我都不是 Rust 或 C++专家(或程序设计专家)。否则，感谢阅读！