# Rust 模块基础

> 原文：<https://dev.to/hertz4/rust-module-essentials-12oi>

你还记得你第一次用一个以上的源文件做一个项目吗？是有标题的，还是有类的...或者，用模块？

模块具有某些特征。它们允许不同的项目共存于一个单元中，而不需要绑定到一个类。模块文件通常把它的接口(面向外部的公共部分)放在顶部，把它的实现(肮脏的内部细节)放在底部。

理解 Rust 的模块来源于理解关键词`mod`、`pub`和`use`。简单地说:

*   `mod`声明一个模块。
*   `pub`暴露。
*   把东西拉进来。

让我们从一个例子开始。模块有两种定义方式。第一个到位:

```
// main.rs

mod food {
    pub fn eat() {
        println!("Good.");
    }
}
fn main() {
    food::eat();
} 
```

Enter fullscreen mode Exit fullscreen mode

二、作为单独的文件:

```
// main.rs

mod food;

fn main() {
    food::eat();
}

// food.rs or food/mod.rs

pub fn eat() {
    println!("Yum.");
} 
```

Enter fullscreen mode Exit fullscreen mode

更具体地说:

> `mod X`的意思是:假设有一个模块 X，定义在这里，在括号中，或者在一个名为`X.rs`或`X/mod.rs`的单独文件中。

`pub fn eat`使`eat`对 main 可见。没有`pub`，main 就无法调用`eat`。

请注意，如果 main 中没有`mod food`，Rust 将完全忽略`food.rs`。

接下来，让我们将上面的内容扩展到更深层次的内容:

```
// main.rs

mod food {
    pub mod lunch {
        pub fn eat() {
             println!("Take a break.");
        }
    }
}
fn main() {
    food::lunch::eat();
} 
```

Enter fullscreen mode Exit fullscreen mode

或者作为多个文件。注意`mod.rs`的使用，一个特殊的文件名:

```
// main.rs

mod food;

fn main() {
    food::lunch::eat();
}

// food/mod.rs

pub mod lunch;

// food/lunch.rs

pub fn eat() {
    println!("Hamburger.");
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来像你期望的吗？请注意`fn eat`和`mod lunch`是如何被做成`pub`的。它们必须以这种方式一层一层地往上搬。

当然，任何没有`pub`的东西在它的模块之外都是不可见的。

也许令人惊讶的是，`pub`在个体的基础上对待结构成员和方法。

```
// food.rs

struct Meal {
    pub taste: String,
    price: u32, // cannot see price
}

impl Meal {
    pub fn eat(&self) {
        println!("I taste {}.", self.taste);
    }
    fn purchase(&self) { // cannot call purchase directly
        println!("Spent ${}. Ouch.", self.price);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

拥有一个私有变量`price`意味着你不能在`food`之外制造一个`Meal`。构造函数方法可以帮助解决这个问题。

```
// food.rs

impl Meal {
    pub fn fancy() -> Self {
        Meal {
            taste: String::from("miracles on plate"),
            price: 44100,
        }
    }
}

// main.rs

mod food;

fn main() {
    let lunch = food::Meal::fancy();
    lunch.eat();
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，概括一下:

> 将模块或结构级别的项目暴露给其周围的级别。

请注意，`Meal::fancy`也可以被命名为`Meal::new`，甚至其他名称。

接下来，让我们检查`use`来完成我们的三个。`use`是一个拉入文字的工具，可以减少视觉混乱和手指工作。

```
mod food;
use food::Meal;

fn main() {
    let lunch = Meal::fancy();
    lunch.eat();
} 
```

Enter fullscreen mode Exit fullscreen mode

甚至:

```
mod food;
use food::Meal::fancy;

fn main() {
    let lunch = fancy();
    lunch.eat();
} 
```

Enter fullscreen mode Exit fullscreen mode

直接拉进`fancy`是一个极端的例子，但是确实有效。就像`pub`可以应用于结构一样，`use`也可以。

`use`的一个小问题是它有绝对路径。为了理解路径，考虑这个不工作的例子:

```
mod food {
    fn cheaper(a: &Meal, b: &Meal) -> u32 {
        std::cmp::min(a.price, b.price)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这失败了，因为`std::cmp`和写`self::std::cmp`是一样的，`self`在这里是`food`。这是一个相对的路径。所以，让我们使用一个绝对路径，在开头添加`::`。

```
mod food {
    fn cheaper(a: &Meal, b: &Meal) -> u32 {
        ::std::cmp::min(a.price, b.price)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但是`use`关键字也是一个绝对路径，从长远来看可以减少输入。

```
mod food {
    use std;
    fn cheaper(a: &Meal, b: &Meal) -> u32 {
        std::cmp::min(a.cost, b.cost)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是做这件事的另一种方法。是的，`use`确实尊重它的范围。

```
mod food {
    fn cheaper(a: &Meal, b: &Meal) -> u32 {
        use std::cmp::min;
        min(a.cost, b.cost)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了引入多种内容，`use`也有一些简单的替代语法。

```
mod food {
   pub mod bread {
       pub struct Slice {}
       pub struct Loaf {}
   }
   pub struct Plate {}
   pub struct Napkin {}
}

use food::bread::*; // pulls in Slice and Loaf
use food::{Plate, Napkin}; 
```

Enter fullscreen mode Exit fullscreen mode

具体来说:

> 从绝对路径将模块或结构项带入当前范围。

还要注意的是`pub use`可以作为展平方法。

```
mod food {
    pub mod breakfast {
        pub mod cereal {
            pub fn eat() {
                println!("Snip, crankle, porp.");
            }
        }
    }
    pub use self::breakfast::cereal;
}
fn main() {
    food::cereal::eat();
} 
```

Enter fullscreen mode Exit fullscreen mode

这使您能够划分成更多的模块进行组织，而不会给外界增加负担。

总的来说，还是更明确一点(避免过多的`*`)和`use`明智一些比较好。当你以后看到东西时，最好知道它们是从哪里来的，但是增加视觉混乱和打字就没有意思了。

概括一下:

*   `mod`声明一个模块。
*   `pub`按单个级别显示项目。
*   `use`将事物从绝对路径带到当前范围。

还有更多，但我希望我已经帮助事情开始。如果你对第二部分感兴趣，请告诉我。