# 为什么我对围棋如此失望

> 原文：<https://dev.to/klnusbaum/why-im-so-frustrated-withgo>

从开始用 Go 到现在大概一年了。我用它写了很多代码，包括一整个微服务和对 glide 的贡献。我想强调的是，在很大程度上，一切都很好。但是大约一个月一次，我的眼睛开始抽搐，我有冲动开始做一个头对桌子的动作。其中的原因很简单。因为我似乎经常进行这样的对话，所以我想我应该把我的想法记录下来，这样下次它出现时我就可以简单地引用它了。

# 简单的数据结构

我需要一个数据结构。它有以下要求:

1.  它必须存储一个动物的名字和一个包含该动物信息的相关结构。
2.  它必须有 O(1)个查找时间。
3.  我必须能够以某种预定义的顺序迭代它的元素。
4.  它一定是永恒的。

乍一看，我们可能想简单地使用这样的地图:

```
type AnimalMap map[string]Animal 
```

Enter fullscreen mode Exit fullscreen mode

这挺好的。事实上，它满足了我们的数据结构的前两个要求。但是从 Go 的早期开始，[顺序的对象被随机存储在一个 map 中](https://blog.golang.org/go-maps-in-action#TOC_7.)(有充分的理由)。所以我不能做一些简单的事情，比如将所有元素按照我希望它们迭代的顺序插入到地图中。

好吧，没什么大不了的。我们可以通过多写一点代码来解决这个问题，使用一个数组来跟踪顺序。

```
type AnimalMap struct {
  Mapping map[string]Animal
  Order []string
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当我们想要迭代地图时，我们需要迭代`Order`切片，使用我们提取的字符串作为地图的键。不是最棒的，但很管用。

# 不变性呢？

这就是事情变得非常棘手的地方。Go 没有[运行时常数](https://www.quora.com/What-is-the-compile-time-constant-and-run-time-constant-in-the-C-programming-language)的概念。如果我们希望事物在创建后是不可变的，我们将不得不完全，我的意思是完全，在我们的结构中封装切片和映射。这是因为地图和切片具有[按引用传递语义](https://stackoverflow.com/questions/40680981/are-maps-passed-by-value-or-by-reference-in-go)。换句话说，如果我在任何时候给你一个对它们的引用，你都可以修改它们。我能想到的最好的就是这段代码:

```
type Animals interface {
    Len() int
    At(i int) string
}

type animals []string
func (a animals) Len() int {return len(a)}
func (a animals) At(i int) string {return a[i]}

type AnimalMap struct {
    mapping map[string]Animal
    order   animals
}

func NewMap(order []string, mapping map[string]Animal) AnimalMap {
    newMap := AnimalMap{
        mapping: make(map[string]Animal, len(mapping)),
        order:   make(animals, len(order)),
    }

    for i, name := range order {
        newMap.mapping[name] = mapping[name]
        newMap.order[i] = name
    }

    return newMap
}

func (am AnimalMap) Order() Animals {return am.order}
func (am AnimalMap) Animal(name string) Animal {return am.mapping[name]} 
```

Enter fullscreen mode Exit fullscreen mode

刚刚发生了什么？这是一个令人愤怒的代码量。你知道如果这是 [C++](https://stackoverflow.com/questions/2636303/how-to-initialize-a-private-static-const-map-in-c) 、 [C#](https://msdn.microsoft.com/en-us/library/dn467194(v=vs.111).aspx) 或 [Java](https://google.github.io/guava/releases/snapshot/api/docs/com/google/common/collect/ImmutableMap.html) 我要写多少代码吗？没有。他们都有不可变的、有序的映射的可重用概念。然而在围棋中，每当我需要一个程序时，我都要写 30 行代码。我觉得我必须奋斗才能走到这一步。我感到悲伤。

# 结论

围棋有一些非常好的概念。我喜欢它加强了继承的成分。Goroutines 相当漂亮。总的来说，我对它相当满意。但一次又一次，我发现自己在使用 Go 时一遍又一遍地解决同样的问题。我发现自己完全被堵住了，没有办法写干代码。

我很乐意看到 Go 社区解决这样的问题。IMHO 泛型可以解决上述所有问题，但是我知道这对于 Go 社区来说是一个棘手的话题。也许是像通用数据结构和算法的简单形式的代码？

* * *

Kurtis Nusbaum 是优步的一名移动和后端开发人员。他是一个狂热的长跑运动员和多样性倡导者。这是他的[邮件列表](http://tinyletter.com/kurtis)。这是他的[推特](https://twitter.com/klnusbaum)，和 [LinkedIn](https://www.linkedin.com/in/knusbaum) 。这里是他的 [github](https://github.com/klnusbaum) 。如果 Kurtis 看起来是那种你想与之共事的人，给他发一封电子邮件到 kcommiter@gmail.com。

* * *

这里有[为什么你可能想要不变性](https://softwareengineering.stackexchange.com/questions/151733/if-immutable-objects-are-good-why-do-people-keep-creating-mutable-objects)
比如，试着写一个可重用的指数补偿算法或者一个可重用的二叉树。哦，还有就是不能用界面{}。我们用静态类型语言编程是有原因的。