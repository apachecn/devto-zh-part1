# Scala 泛型 I: Scala 类型界限

> 原文：<https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38>

## Scala 中的泛型类型

泛型类型、抽象类型、scala 类型界限，所有这些概念对于来自不(或很少)使用泛型的语言的软件开发人员来说都是不寻常的，所以在第一篇文章中，我们将讨论基础知识，并尝试只深入研究类型界限。

让我们使用这一小组类型，我们将不断修改停车类型。

```
trait Thing 
trait Vehicle extends Thing 
class Car extends Vehicle 
class Jeep extends Car 
class Coupe extends Car 
class Motorcycle extends Vehicle 
class Vegetable 

class Parking[A](val place: A) 
```

命名法:如果我们考虑事物[A]，事物是类属类型，而(A)是抽象类型。

在开始之前，我们应该了解泛型类型是如何工作的，以及 Scala 中的类型推断。

Parking[A]表示我们要传递给“place”的值必须是类型 A，因此，如果我们创建 Parking[Motorcycle],我们将使用一个 Motorcycle 实例来创建它:

```
new Parking[Motorcycle](new Motorcycle) 
```

如果我们试试这个:

```
new Parking[Motorcycle](new Car) 
```

显然，这不会编译，因为汽车和摩托车不是同一类型。那么，当我们试图把一辆吉普车停在“专用停车场”时会发生什么？

```
new Parking[Car](new Jeep) 
```

Jeep 是车的亚型，当然管用！

正如我之前提到的，让我们讨论类型的推断。

首先，让我们稍微改变一下停车:

```
class Parking[A](val place1: A, val place2: A) 
```

如果我们遵循上一个例子，停车[汽车]应该收到两辆汽车，两辆吉普车，汽车和一辆吉普车，或类似的东西。但是如果我们不指定类型会发生什么呢？

```
new Parking(new Car, new Motorcycle) 
```

它进行编译，是编译器给类型 A 赋值，类型推理使我们的任务更容易，但是，A 是什么类型，我们还没有指出它呢？

编译器将评估两个参数的类型，如果它们是相同的类型，这很容易，但是因为它们不是，所以它搜索最近的公共超类型。检查这一点的一个简单方法是在 Scala REPL 上创建这个停车场。印刷清晰可见:

```
val a = new Parking(new Car, new Motorcycle)
 *a: Parking[Vehicle] = Parking@278e1674*

Â 
```

它理解为“停车必须是‘车辆停车’”。

## Scala 类型界限

一旦介绍完毕，让我们把有趣的东西摆上桌面。

在前面的例子中没有限制，我们可以创造停车[蔬菜]和世界将继续旋转，但是在现实生活中，我们想要强加某些规则。

如何才能创建类型参数限制？为了创建它们，我们使用 scala 类型边界。

### 上限类型界限

```
class Parking[A <: Vehicle]

Â 
```

更容易理解的类型绑定是上类型绑定 val a: Parking 意味着“a 必须是 Parking 的实例或 Parking 的子类型”。

在类型场景中，停车【A <:车辆】表示 A 类型必须是车辆的类型或子类型。

正因为如此，如果我们创造一个汽车，轿车，吉普车或摩托车停车场，一切都可以工作。

可以在测试中添加以下行，如果结果是成功的话:

```
new Parking[Vehicle] shouldBe a[Parking[_]]
 new Parking[Car] shouldBe a[Parking[_]]
 new Parking[Jeep] shouldBe a[Parking[_]]
 new Parking[Motorcycle] shouldBe a[Parking[_]]

Â 
```

但是，如果我们试图创建一个蔬菜或东西停车场，它不会编译。我们的编译器保护我们:

如果我们在测试中添加这一行，它甚至不会编译:

```
new Parking[Vegetable] should be(a[Parking[_]])
new Parking[Thing] should be(a[Parking[_]])

* _ refers to existential types: https://typelevel.org/blog/2016/01/28/existential-inside.html 
```

### 下界类型

另一方面，我们有一个较低的类型界限\\>:\ '，这表明与\\<:/>\
[A>:Vehicle]相反，它会将 A 限制为 Vehicle 的超类型，包括 Vehicle。

它的用途主要与 co 和逆变有关尽管这个话题涉及到另一篇文章，我们将分解下类型界限的概念:

让我们开始了解哪种类型的关系代表下类型界限。A > : B 表示 A 一定是 B 或者比 B 高，B 是边界(bound)。

```
class Parking[A >: Jeep](val place: A) 
```

在这个停车场里，我们可以停放任何超级类型的吉普车，也就是说，汽车、车辆、东西……任何东西..嗯，这似乎有点太普通了，不是吗？

```
class Parking[A >: Jeep <: Vehicle](val plaza: A) 
```

如果我们从样式中想象一个类型树，这是一种布局方式:

```
trait Thing
class Vehicle extends Thing
class Car extends Vehicle
class Jeep extends Car
class Coupe extends Car
class Motorcycle extends Vehicle
class Bicycle extends Vehicle
class Tricycle extends Bicycle 
```

我们能否将停车限制在三轮车以上的所有车辆类型？

```
class Parking[A >: Bicycle <: Vehicle](val plaza: A) 
```

看起来我们确实可以，让我们来测试一下:

```
new Parking(new AnyRef) 
```

**<控制台> :12:错误:推断的类型实参【Object】不符合类 Parking 的类型形参界限【A >:自行车<:车辆**

**新停车(new any ref)**

**【^**

**<控制台> :12:错误:类型不匹配；**

**发现:对象**

**必需:一个**

**新停车(new any ref)**

**^**

AnyRef 给了我们一个编译错误，完美，我们看到类型上限有效。

```
new Parking(new Bicycle)

res5: Parking[Bicycle] = Parking@1f3f425b 
```

```
new Parking(new Vehicle)
 res6: Parking[Vehicle] = Parking@61fb801b 
```

```
new Parking(new Coupe)
 res7: Parking[Vehicle] = Parking@e959286 
```

我们有了一个大问题，三轮车会发生什么？

```
new Parking(new Tricycle)
res8: Parking[Bicycle] = Parking@53c20385 
```

可能你们中的一些人没有预料到这一点，但它确实有效。

但是，编译器总是背对着你，现在停车的类型是停车【自行车】。

作为三轮车自行车的一个子类型，该示例搜索了一个与 frontiers 匹配的超类型，并找到了它。因为里斯科夫那里你可以用自行车，你也应该可以用三轮车。

然而，这并不是类型下限最常见的用法，

它们用于将协变类型置于逆变位置(在下一篇文章中，我将提出这个概念)。你要清楚，如果你创建了一个协变的东西[+A]，有些规则会阻止你在某些位置使用它，比如:

```
class Parking[+A] {
 def parkIt(element: A): Parking[A] = new Parking(element)
}

Â 
```

即使它看起来是正确的，但它不是:我们在逆变位置使用了，即使它是协变类型。说真的，如果不理解它的意思，不要担心，它将在另一篇文章中解释，只需关注编译器的禁止。

那么，我们怎样才能有一个方法，让我们根据参数类型来创建停车场呢？

下界类型:

```
class Parking[+A] {
 def parkIt[B >: A](element: B): Parking[B] = new Parking(element)
 }

Â 
```

因为较低的类型界限包括边界，添加一个较低的类型界限，我们可以使用协变的 A 类型来从 B 生成较低的类型界限的极限，并让 B 是对一切进行类型化的人。Tada！！！

## 使用-地点差异

scala 类型界限的另一个用途是使用站点差异。这是 Java 中使用的方差形式。

```
class Parking[A](val place: A){
 def dosomething(p1: Parking[_ <: Vehicle]) {}
} 
```

即使我们的停车没有界限，也没有协方差，但它接受任何类型为 A，在我们的方法中，我们已经创建了一个限制，现在我们可以这样做:

```
dosomething(new Parking(new Car)) 
```

但是我们不能这样做:

```
dosomething(new Parking(new AnyRef)) 
```

**<控制台> :12:错误:类型不匹配；**

**发现:对象**

**必需:车辆**

**do something(new parking(new any ref))**

**^**

如果我们使用 Thing，也会发生同样的情况，因为 AnyRef 是我们可以定义的 anytype 的超类型。

如果我们不进行使用场所变更，就会出现这种情况:

```
def dosomething(p1: Parking[Vehicle]) {}
 var p1 = new Parking(new Car) 
```

**dosomething(p1)**

**<控制台> :14:错误:类型不匹配；**

**发现:停车【汽车】**

**必选:停车【车辆】**

**注:汽车<:车辆，但类停车在类型 A 中不变**

**你不妨将 A 定义为+A 来代替。(SLS 4.5)**

**do something(P1)**

它只接受停车[车辆]

我想提到的最后一点是:注意类型推断

如果我们不明确类型，奇怪的事情可能会发生。我们刚刚看到 dosomething 不接受 p1，因为它不是预期的停放类型，但是如果我们这样做将会发生什么:

```
dosomething(new Parking(new Car))

Â 
```

类型推断将寻找匹配的类型:它将汽车的类型提升到车辆，创建一个停车场[车辆]。让一切恢复正常。

有了这个工具，你应该能够理解大部分使用泛型的代码，甚至开始自己编写代码。

在下面的 scala 文章中，我会继续讨论关于泛型、方差和类型约束。以及为什么使用泛型而不是多态的原因。

如果你对 Scala 类型界限或者软件开发最佳实践感兴趣，我推荐你订阅我们的每月简讯来获得最新的技巧。

## 如果你觉得这篇关于 Scala 类型界限的文章很有趣，你可能会喜欢…

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

帖子[Scala Generics I:Scala type bounds](https://apiumhub.com/tech-blog-barcelona/scala-type-bounds/)首先出现在 [Apiumhub](https://apiumhub.com) 上。