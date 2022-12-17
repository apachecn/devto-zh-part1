# 稍微好一点的 C++

> 原文：<https://dev.to/nathanfenner/a-slightly-better-c>

C++是一门庞大的语言，有很多值得喜欢的地方，也有很多(更多)不喜欢的地方。下面是一些随笔，讲述了如何让 C++成为(我认为的)稍微好一点的语言。主要目标可归纳如下:

*   代码应该具有与等价的 C++相同的，或者可机械翻译的语义
*   现有的 C/C++/ALGOL 系列开发人员应该非常熟悉代码
*   代码应该有类似于 C++的性能
    *   如果它大大简化了语义，我愿意接受一些性能下降(下面有一个例子)。
*   代码应该与 C++完全兼容(作为库和作为 ABI)。理想情况下，计算机不应该能够区分它们。
*   应该有可能将现有的 C++(假设它遵循合理的编码标准)机械地翻译成这种新语言。

还值得加上*非目标*:

*   变成 Rust 和/或 Haskell，或以其他方式广泛关注安全
    *   让事情逐渐变得更好的小变化是好的，但是如果我真的想有哪怕是一点点的机会来改进现实世界的 C++，它绝对不需要这些复杂的静态检查。

所以有了这些目标，我提议用什么语言呢？整篇文章有点乱，但它包含了我为了让 C++更好用而提出的许多小想法。许多东西都是基于我在密歇根大学教授 EECS 281 课程和我自己在 C++上编程的经历，但我希望它们能引起你的共鸣，不管你如何使用 C++。

首先，一些语法层面的东西。对于这里的大多数想法来说，这并不是真正的核心，但是 C++迫切需要在一些关键领域进行改造。

```
// C++
#include <vector>
#include <iostream>
std::vector<int> global_vector(6, -1);
int sum() {
    int sum = 0;
    for (int x : global_vector)
        sum += x;
    return sum;
}
void print_sum() {
    std::cout << "the sum of the global vector is " << sum() << std::endl;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// SBC++ (slightly better C++)
module main;
#include <vector>
#include <iostream>
var global_vector : std::vector<int> = vector<int>::make(6, -1);
func sum() -> int {
    var sum : int = 0;
    for var x : int in global_vector {
        sum += x;
    }
    return sum;
}
func print_sum() {
    std::cout << "the sum of the global vector is " << sum() << std::endl;
} 
```

Enter fullscreen mode Exit fullscreen mode

最显著的区别可能是添加了关键字`var`，并将类型移到了`:`的右侧。您可能不在乎这种变化，这没什么，但我通常发现，能够找出变量定义的位置比省略变量声明关键字节省的少量空间更有帮助。

(还有，我们现在可以去掉`auto`:只要不指定变量的类型就行。尽管如此，仍然不能进行完整的类型推断。)

下一个明显的变化是切换到使用`func`而不是用前面的返回类型声明函数。我对此的推理与`var`非常相似:在大多数情况下，我宁愿能够挑出一个函数正在被定义的事实，以及它的名字，而不是它的返回类型。

在文件的顶部还有一个`module`语句。或许不出所料，这些内容也不是文本。这些变化可能远不如以前的变化有争议(尽管实现和行为的细节肯定会引起争论！).

enhanced- `for`循环需要一个关键字(这里是`in`)来代替现在用于类型定义的`:`。

最后，在 for 循环、while 循环以及 if 和 else 块中，大括号是必需的(尽管允许`else if`中间没有大括号)。因为大括号是强制的，圆括号就不用了。

# 变更分配、施工和销毁

C++的构造和析构对象的模型非常有效，可以对资源进行细粒度的控制。然而，这个系统有一些(错误的)特性经常碍事，坦率地说，这些特性并不是必需的。

所以，首先，构造物体。如果我们首先没有任何工作，就很难取得任何进展。

## 构造一个物体

C++中的构造函数有点神奇。我想在 SBC++中淡化这一点。最大的区别是，在 SBC++中，构造函数*有名字*。这里有一个例子:

```
// SBC++
class Deck {
    var cards : std::vector<Card>;
public:
    new func empty() {
        cards = std::vector<Card>::empty();
    }
    new func full() {
        cards = std::vector<Card>::empty();
        for var suit in suites {
            for var rank in ranks {
                cards.push_back(Card::make(suit, rank));
            }
        }
        shuffle(mut cards);
    }
    func draw() mut -> Card {
        return cards.pop_back();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

A `new func`就是 C++所说的构造函数。在一个`new func`中，所有的成员变量都没有初始化，初始化它们是`new func`的工作。在 SBC++中，构造函数并不特别，所以我们根本不需要使用初始化列表。

上面的代码为`Deck`类定义了两个`new func`。我们有一种建造空甲板和满甲板的方法。

注意，SBC++没有“默认”构造函数的概念。如果一个类型想要支持默认实例的创建，那么它可以显式地这样做。特别是，SBC++不需要默认的构造函数就可以将一堆项目放入一个集合中！以类似的方式，我们将看到 SBC++如何不像 C++那样需要复制构造函数(尽管它也可以使用它们)。

这对你来说可能不太好！特别是，我故意牺牲能够创建空集合的便利性。只需声明适当的值。换句话说，

```
// C++
std::vector<int> empty_vector; 
```

Enter fullscreen mode Exit fullscreen mode

不对应于

```
// SBC++ (doesn't create empty vector)
var empty_vector : std::vector<int>; 
```

Enter fullscreen mode Exit fullscreen mode

而是改为

```
// SBC++
var empty_vector : std::vector<int> = vector<int>::empty(); 
```

Enter fullscreen mode Exit fullscreen mode

简洁中失去的是*希望*在清晰中得到弥补。特别是，如果你*不想*把它初始化成任何东西，编译器会很容易地告诉你。

## 赋值

在 C++中赋值是一件肮脏的事情。从概念上讲，赋值可以分为复制赋值和移动赋值。复制值会使目标成为右侧值的副本，移动赋值会将值从一个位置“移动”到另一个位置。

SBC++采用了另一种方法。首先，我们必须对值如何假设它们的位置可以改变施加限制。特别是，*如果没有从外部创建对一个对象的显式引用，编译器可以随意地在内存中移动它*(注意:显然有时值必须被固定，所以专用类型可以选择消除这种行为；但是之后它们根本不能被分配)。特别是，这意味着对象不能持有指向它们自己或它们自己的字段的指针，因为如果对象移动，它们将变得无效。

下面是稍微好一点的 C++做赋值的方式:

*   调用 LHS 的析构函数
*   LHS 所在的 RHS
*   将 RHS 标记为无效(如果它是临时的，它可以被遗忘；如果它是一个变量，它需要一个“丢弃标志”来表明它被移走并且无效，除非它是“普通的可复制的”,因为它不拥有任何资源。

这大大简化了模型。我们确实在优化方面有所损失；C++实现可以重用 LHS 中的缓冲区或其他自有内存来进行赋值；但是，我怀疑大多数应用程序(除了性能非常高的应用程序之外)都想要这种行为。如果需要，可以达到同样的效果，只是不使用赋值。

## 毁灭

销毁与 C++中的非常相似。唯一的区别是，现在每个变量都需要一个“丢弃标志”来指示它们是否应该被丢弃。本质上，SBC++知道变量未初始化和实际上没有值(在 C++中，被移出)之间的区别。实际上，大多数变量实际上不需要 drop 标志，因为它们的析构函数何时以及是否需要运行是显而易见的(静态的)。

# 静态检查

有一些静态检查比 C++更容易添加到 SBC++中，编译器需要强制执行它们。

首先是使用未初始化的值。由于 SBC++在初始化对象时不运行“默认构造函数”,所以不能使用没有给定值的变量！特别是，如果有任何到当前执行点的代码路径，其中变量没有被赋值，那么它就不能被使用。由于 C++完全是零成本的抽象，我们希望提供一种方式向编译器断言它实际上已经是零成本的了(我会选择类似于`assert_initialized variable_name;`)；在这种情况下，没有性能损失。

# 引用和常量

引用被认为是更安全的指针，但是在 C++中有一些方法是不安全的。最明显的问题是它们必须在声明时赋值。C++需要这一点，因为否则它们不是特别安全；如果它们没有被指向某个地方，你就不能使用它们，但是 C++没有这种赋值检查来阻止你这样做！幸运的是，SBC++有。

遗憾的是，SBC++中的引用不如 C++中的方便。幸运的是，他们笨拙的方式无关紧要(后面会看到)。

C++引用的另一个不幸之处是它们鼓励非`const`性。默认情况下，Constant 往往更好(因为它是一个更安全的默认设置，同时仍然不会阻止一个好的编译器应该执行的任何优化)，所以 SBC++将它用于引用(尽管不用于变量)。

```
// C++
int x = 4;
int& y = x;
y = 2;
std::cout << x << std::endl; 
```

Enter fullscreen mode Exit fullscreen mode

```
// SBC++
var y : &mut int; // can declare y first!

var x : int = 4;

y = &mut x; // need to use '&' to point y at x 
*y = 2; // need to dereference y explicitly 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，当以这种方式使用时，引用就像指针一样！在 SBC++中，`&expression`的类型现在是`&T`而不是`*T`，但是允许引用衰变为指针(当然，引用仍然不能是`NULL`)。

注意，这意味着将引用指向某个新的地方没有问题。写作

```
y = &mut x;
y = &mut z;
y = &mut w; 
```

Enter fullscreen mode Exit fullscreen mode

会非常好，并且每次都指向不同的地方。这意味着我们可以用一个引用字段来赋值给一个结构或类。(耶！)

然而，这相当不方便。因此，有一些方便之处。访问字段和方法是用点而不是箭头来完成的，函数需要一个

```
// SBC++
func print_vec(list : &std::vector<int>) {
    for var x : int in list {
        std::cout << x << " ";
    }
}

func double_up(list : &mut std::vector<int>) {
    for var i in std::range(list.size()) {
        list.push_back(list[i]);
    }
}

var my_list : std::vector<int> = {1, 2, 3};
double_up(mut my_list);
print_vec(my_list); // 1 2 3 1 2 3 
```

Enter fullscreen mode Exit fullscreen mode

如果一个函数或方法将一个可变的引用作为参数，调用者必须在参数前说`mut`或`&mut`来表明这一点(但是`mut`是首选)。

## 常数

田地也焕然一新！C++使得有效使用`const`字段变得非常烦人(或者完全不可能)。如果你想创建一个具有常量字段的对象，它不能放在向量中，至少如果你想能够`push_back`进去的话。

SBC++简化了事情。当一个字段被声明为`const`时，很明显这实际上意味着它应该相对于对象被改变*。换句话说，这意味着对象的一个*突变不能改变它的*。*

然而，一个对象的批发*替换*仍然是完美的，即使有`const`字段！

```
// SBC++

struct TwoOfTheSame {
    const x : int;
    const y : int;
    new func make(v : int) {
        x = v; // x is not const for the body of a new-func
        y = v;
    }
}

var p : TwoOfTheSame = TwoOfTheSame::make(7);

p = TwoOfTheSame(3); // perfectly valid 
```

Enter fullscreen mode Exit fullscreen mode

通过允许`const`引用对象完整性，而不是绝对内存常量，可以更频繁地使用它，从而更有效。

# 安全枚举和标记联合

是的，这是注定要来的。尽管我希望在我使用的任何语言中都有[通用的代数数据类型](https://en.wikibooks.org/wiki/Haskell/GADT)，但是当试图对语言进行保守的改进时，将它们添加到 C++中有点多。

基本上，enum values should `enum class`相对于 C 的`enum`是一个巨大的改进，我想对它做一个类似的扩展。一个`enum union`用一个标签字段扩展了 C 风格的`union`，这个标签字段允许在它的案例中进行区分。**此外，它为基于标签的类型创建了一个析构函数，这样它们仍然可以被清除**。这些变化(以及某种类型引导的`switch`来实际操作它们)满足了我所寻找的。对于可选类型来说，结果可能是这样的:

```
template<T>
enum union Optional {
    None;
    Some(T);
} 
```

Enter fullscreen mode Exit fullscreen mode

# 模板改进

不知何故，语法需要改进一点。我不知道怎么做。相反，我想谈谈完全不同的东西。(这里是我提出广义代数数据类型的地方。)

有一个方便的概念(主要)来自函数式语言，称为*。实质上，幻影类型是一种没有相应运行时组件的类型参数。此外，我想支持[多态递归](https://en.wikipedia.org/wiki/Polymorphic_recursion) *，但仅限于幻影类型。这意味着我们仍然可以通过模板化来编译它们，因为幻影类型不需要被实例化！** 

```
 struct Z{}
template<T> struct S{}

template<T, phantom L>
enum union AVLNode {
     Empty : AVL<T, Empty>;
     Balanced(T, *AVL<T, Height>, *AVL<T, Height>) : AVL<T, S<Height>>;
     LeftHeavy(T, *AVL<T, S<Height>>, *AVL<T, Height>>) : AVL<T, S<S<Height>>>;
     RightHeavy(T, *AVL<T, Height>, *AVL<T, S<Height>>>) : AVL<T, S<S<Height>>>;
} 
```

Enter fullscreen mode Exit fullscreen mode

该类型描述了一个*平衡的* AVL 树；不可能构造一个不平衡的树的实例，但是它的运行时表示只为每个节点对象付出 1 字节的代价来确保这是可能的！特别是，这不超过 AVL 树的标准实现。

# 结论

这里有很多我没有涉及的内容，其中一些细节非常重要。值得注意的是，需要更详细地研究将某物移出引用的语义(出于正确性的原因，这是不允许的，但是如果我们根本不能这样做，这就有问题了)。继承如何处理这些事情也需要一些思考，特别是 w.r.t 基类构造函数和虚方法。

我发表这篇文章主要是给自己一个随意的提示，但我希望它至少能让偶然发现它的人感兴趣。*