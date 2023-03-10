# 拥抱静态类型系统:强类型

> 原文：<https://dev.to/technocoder/embrace-the-static-typing-system-strong-types-pd>

以下是大约 3 到 5 分钟的阅读。

# 一、什么是强打字？

应该注意的是，“强类型”并不是一个正式的技术术语。然而，当我谈到强类型时，我将使用下面的定义。简单地说，强类型化是编译器加强类型和类型化“原则”的趋势。一些强类型语言的例子(在我看来)是 Haskell、Rust 和 C++。

强类型的反义词是弱类型。弱类型语言通常允许我们很容易地改变某种东西的类型，或者“哄骗”一种类型成为另一种类型。例如在 C 语言中，我们可以强制一个指针变成一个 void*或者任何我们想要的指针。

强类型和弱类型并不相互排斥。相反，它更像是一个轴心。例如，在大多数语言中，我们几乎总能找到某种类型的转换系统。

不应将强类型与静态和动态类型混为一谈。这两个术语指的是检查和实施类型的时间。静态类型在编译时检查类型，而动态类型在运行时检查类型。静态类型与强类型正交。

# 查看类型的不同方式

我假设每个人或多或少都知道面向对象编程是如何工作的。我想强调一下特定用户定义类型和*用户定义类型的区别。*看下面这个做作的例子:

```
struct Circle {
    radius: f32,
}

impl Circle {
    fn new(radius: f32) -> Circle {
        Circle {
            radius,
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于那些不熟悉 Rust 的人来说,“new”函数本质上与构造函数是一样的。

请注意，我还没有用用户定义的类型替换“f32”。目前“f32”描述了可变半径应该是什么:一个浮动。然而，它*并没有描述它对于*来说是什么。我会(希望)让你明白我的意思。

```
fn new(f32) -> Circle {
    // function body
} 
```

Enter fullscreen mode Exit fullscreen mode

这或多或少是函数签名。你能看出来，我们应该在里面放什么参数吗？可能是半径，也可能是直径，甚至是周长。它甚至可能根本不能描述圆圈的大小！

这就是我说的“它没有描述它的用途”的意思。或者换句话说，这种类型没有任何意义。对此已经有了一个术语:原始人执念，但我不认为它只适用于原始人。

当我们在代码中实际使用这个函数时，这个问题尤其普遍。见下文:

```
fn main() {
    let some_random_circle = Circle::new(30.0);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们不知道这段代码在做什么！“30.0”是完全没有意义的。如前所述，它可以指任何东西。了解这里实际发生了什么的唯一方法是查看“新”函数本身的内部。

(我们可以将“30.0”放入一个单独的变量中。但是我们稍后会看到，我们可以用其他更有益的方法来解决这个问题。或者你知道，阅读文档。或者，如果你有一个超级漂亮的 IDE，它会自动在放入的变量旁边显示参数名。*咳嗽咳嗽 CLion 和 Intellij* 。但是一般来说，问题是我们不能只是浏览代码并理解它做什么)

# 我们如何解决这个问题

我们解决这个问题的方法其实很简单。简单地创建一个新的类型来描述 30.0 实际上指的是什么。见下文:

```
struct Radius {
    radius: f32,
}

impl Radius {
    fn new(radius: f32) -> Radius {
        Radius {
            radius,
        }
    }
}

struct Circle {
    radius: Radius,
}

impl Circle {
    fn new(radius: Radius) -> Circle {
        Circle {
            radius,
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在 Circle 不允许我们在里面放除了半径以外的任何东西。我们不可能把它误认为周长或者直径。因为，编译器不会让我们犯那样的错误。下面是更新后的主函数:

```
fn main() {
    let some_random_circle = Circle::new(Radius::new(30.0));
} 
```

Enter fullscreen mode Exit fullscreen mode

完美！现在，任何查看这段代码的人都可以立即看出这是一个由半径值构造的圆。

# 别忘了直径和周长

如果你想通过直径构造一个圆，你可以重载新的构造函数。遗憾的是，Rust 目前还不支持重载(据我所知)。我解决这个问题的方法是将直径明确转换为半径。

```
struct Diameter {
    diameter: f32,
}

impl Diameter {
    pub fn new(diameter: f32) -> Diameter {
        Diameter {
            diameter,
        }
    }
}

use std::convert::From;
impl From<Diameter> for Radius {
    fn from(diameter: Diameter) -> Self {
        Radius::new(diameter.diameter / 2.0)
    }
}

fn main() {
    let circle = Circle::new(Diameter::new(30.0).into());
} 
```

Enter fullscreen mode Exit fullscreen mode

(注意，为 Radius 创建“From”实现会自动为任何直径变量创建 into()函数。这被认为是添加造型而不是为直径创建“进入”实现的首选方式)

当我们浏览这段代码时，我们仍然可以立即理解循环结构。into()函数调用让其他程序员知道我们考虑了代码，并确认我们确实想将这个直径转换成半径。

一个额外的好处是，每个使用半径的函数不再需要一个额外的直径函数(如果我们学过函数重载课程的话)。想象一下如果我们增加一个圆周结构。我们必须检查整个代码库，寻找半径的用法，并创建一个额外的接受周长的函数。

此外，如果我们不小心在没有 into()调用的情况下使用了 Diameter，编译器会抛出一个错误(至少在 Rust 中，其他语言可能会隐式地强制转换它)。如果我们真的打算使用 Radius，这是一个可以避免的错误。

# 我的库中不可能有 3000 个不同形状的结构

的确，到目前为止，我一直在使用一个非常人为的例子。我无法想象一般的程序员每次写程序的时候都会创建一个 shape struct/class。是时候展示一个真实世界的例子了。

这个例子实际上是在我开发游戏的过程中出现的。我正在创建一个虚拟窗口系统，这样我的游戏窗口中就会有单独的窗口*。此外，我必须小心管理坐标，以免混淆虚拟窗口和真实窗口。以下是最初发生的事情:* 

```
// Somewhere inside the depths of my MainMenu state
fn event(&mut self, event: &Event) {
    Event::MouseButtonPressed {x, y, ..} => {
        let cursor_position = some_function_that_uses(x, y);
        if let Some(position) = self.virtual_window.on_mouse(cursor_position) {
            if self.button_inside_virtual_window.contains(cursor_position) {
                // Do something cool
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了清楚起见，一些变量和函数的名称已经更改。不管怎样，你发现逻辑错误了吗？

“contains”函数被传递给“cursor_position”，而实际上它应该接受“position”(变量在“if let Some(position) = ...”中)).更糟糕的是，我甚至想到了这种可能性，但我还是花了很长时间才明白为什么我的按钮会在窗户中间被激活。

下面是我如何添加两个结构，这样就不会再发生这种情况:

```
struct RawPosition {
    x: f32,
    y: f32,
}

impl RawPosition {
    // Some constructor for RawPosition

    fn force_virtual(self) -> VirtualPosition {
        VirtualPosition {
            x: self.x,
            y: self.y,
        }
    }
}

struct VirtualPosition {
    x: f32,
    y: f32
}

impl VirtualPosition {
    // Some constructor for VirtualPosition
}

// some_function_that_uses(x, y) now returns a RawPosition
//
// on_mouse(cursor_position) now takes in a RawPosition and
// spits out a VirtualPosition
//
// contains(cursor_position) now takes in a VirtualPosition 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们试图在编译器抛出错误之前运行相同的代码。我们不能将原始位置传递给虚拟位置的参数！Bug 回避。

那么 force_virtual 函数是怎么回事呢？我并不总是想在虚拟窗口上画一个按钮。有时候我想把它画在真正的窗户上。在这种情况下，我不需要处理原始位置数据。然而，编译器不允许我们使用它(如果使用了，那就违背了本文的全部目的)，所以我们必须创建一个函数来转换它。

我将它命名为 force_virtual，因为它让其他程序员知道我已经考虑过这个问题，并且我确实想将 RawPosition 转换为 VirtualPosition。

# 结论

强打字可能不太适合你。如果您使用的语言中结构/类对运行时性能有影响，您可能需要考虑安全性和性能之间的权衡。

强力打字不会适合一切。在我的真实世界的例子中，我必须权衡我在虚拟窗口而不是真实窗口中绘制按钮的频率。但是，如果可以的话，我强烈推荐使用它。它可以帮助避免早期的许多麻烦，并提高可读性。让编译器尽可能多地为您工作。

我对 Rust(或者一般的编程，虽然我猜这是相对的)不是很有经验，但是我会尽我所能回答你们可能有的任何问题。否则，如果你已经做到这一步，感谢阅读！