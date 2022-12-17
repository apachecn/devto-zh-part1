# Scala 泛型 II:泛型中的协方差和逆变

> 原文：<https://dev.to/apium_hub/scala-generics-ii-covariance-and-contravariance-in-generics-5dib>

在上一篇文章中，我们讨论了 [Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)，今天我们将继续讨论 Scala 泛型，并讨论泛型中的**协方差和逆变**。

Liskov 替换原则( [S.O.L.I.D.](https://apiumhub.com/tech-blog-barcelona/solid-principles/) 的 l)规定，在继承关系中，被定义为超类型的类型必须在任何情况下都允许被它的任何派生类替换的上下文中。

为了说明这一点，我们将使用这组类

```
sealed abstract class Vehicle
case class Car() extends Vehicle
case class Motorcycle() extends Vehicle val 
```

在任何时候，鉴于我们定义:

```
val vehicleIdentity = (vehicle:Vehicle) => vehicle 
```

必须正确地调用:

```
vehicleIdentity(Car()) 
```

以及:

```
vehicleIdentity(Motorcycle()) 
```

你可以在这里找到更多细节[。](https://es.wikipedia.org/wiki/Principio_de_sustituci%C3%B3n_de_Liskov)

在泛型类型中，差异是抽象类型的继承关系和它如何“传递”到泛型类中的继承之间的相关性。

换句话说，给定一个类 Thing [A]，如果 A 继承了 B (A < : B)，那么 Thing[A】<:Thing[B]？

方差对这种相关性进行建模，并允许我们创建更多可重用的通用类。

方差有四种:协方差、逆方差、不变性和双方差，其中三种用 Scala 表示:

## >不变性:类停车[A]

抽象类型上的泛型类不变量只能接收该类型的参数类型。

```
case class Parking[A](value: A) val p1: Parking[Vehicle] = Parking[Car](new Car) 
 <console>:12: error: type mismatch;
 found : Parking[Car]
 required: Parking[Vehicle]
 Note: Car <: Vehicle, but class Parking is invariant in type A.
 You may wish to define A as +A instead. (SLS 4.5)
 val p1: Parking[Vehicle] = Parking[Car](new Car) 
```

这个错误清楚地表明，尽管 **Car < : Vehicle，**由于在 A， **Parking [Car]上是不变的！<:停车【车辆】**。

然而，一旦抽象类型被定义，它就可以在类内自由使用，应用利斯科夫替换原则:

```
val p1 = Parking[Vehicle](new Motorcycle)
 res8: Parking[Vehicle] = Parking(Motorcycle())
p1.value.getClass
 res9: Class[_ <: Vehicle] = class Motorcycle 
```

## >协方差:类停车[+A]

在其抽象类型上协变的泛型类可以接收该类型的参数类型或该类型的子类型。

```
case class Parking[+A](value: A) 
val p1: Parking[Vehicle] = Parking[Car](new Car)
p1: Parking[Vehicle] = Parking(Car()) 
```

协方差允许您将 **p1** 键入为**停车场【车辆】**，并将其分配为**停车场【汽车】**。

但是不要忘记，虽然 p1 的类型是 Parking[Vehicle]，**它实际上是一辆 Parking[Car]** ，

这可能会令人困惑，但是下面我解释一下，它们是共变和逆变位置，你最终会明白的。

总结一下:

对于停车[+A]

如果汽车<:车辆

那么停车【汽车】<:停车【车辆】

>逆变:类停车[-A]

抽象类型上的泛型类逆变可以接收该类型的参数类型或该类型的超类型。

```
case class Parking[-A] 
val p1: Parking[Car] = Parking[Vehicle]
p1: Parking[Car] = Parking() 
```

对比允许我们将 p1 输入为 Parking [Car]并将其指定为 Parking [Vehicle]

，总结如下:

用于停车[-A]

如果车辆>:(超类型)汽车

那么停车【车辆】<:停车【汽车】

## 共变和逆变位置

根据指定的位置，类型可以在 co 或逆变位置。一些好的例子如下:

```
class Pets[+A](val pet:A) {
def add(pet2: A): String = "done"
}
<console>:9: error: covariant type A occurs in contravariant position in type A of value pet2 
```

发生了什么事？我们已经将 add()方法的输入参数输入为 A(因为 Pets [+ A]，所以是协变类型)。

编译器抱怨过，它说 add 输入参数在逆变位置，它说 A 无效。但是为什么呢？

因为如果我这么做了:

```
val pets: Pets[Animal] = Pets[Cat](new Cat) 
```

虽然宠物被类型化为 Pets [Animal]，但它实际上是一只 Pets [Cat]，因此，因为宠物被类型化为 Pets [Animal]，pets.add()将接受 Animal 或 Animal 的任何子类型。但这没有意义，因为事实上 pets 是 Pets [Cat]并且 add()只能接受 Cats 或 Cat 子类型。

编译器防止我们陷入调用 pets.add (Dog())的荒谬，既然是一组 Cat。

另一个例子，在逆变的情况下是下一个:

```
class Pets[-A](val pet:A)
 <console>:7: error: contravariant type A occurs in covariant position in type => A of value pet
 class Pets[-A](val pet:A) 
```

发生了什么事？我们已经将输入参数键入为 A(这是逆变的，因为 Pets[-A])

并且编译器已经告诉我们这个参数处于协变位置，我们不能将其键入为 A。但是为什么呢？

因为如果我这么做了:

```
val pets: Pets[Cat] = Pets[Animal](new Animal) 
```

编译器希望 pets.pet 是 Cat，一个能够执行 pets.pet.meow()的对象，但是 pets.pet 不是 Cat，它是一种动物。

虽然 Pets[-A]是 A 的逆变，但值 pet: A 不是，一旦定义了它的类型(pets val: Pets [Cat]暗示 pets.pet 将是 Cat)，这个类型就是确定的。

如果 Pets 在 A (Pets [+ A])上是协变的，这就不会发生，因为如果我们这样做:

val*Pets:Pets[Animal]= Pets[Cat](new%20Cat)*

编译器会等待 pets.pet 成为动物，因为 Cat < : Animal，它就是。

另一个例子，

```
abstract class Pets[-A] {
 def show(): A
}
 <console>:8: error: contravariant type A occurs in covariant position in type ()A of method show
 def show(): A 
```

出于和前面一样的原因，编译器说方法的返回类型是协变的，A 是逆变的。

如果我做了:

```
val pets: Pets[Cat] = Pets[Animal](new Animal) 
```

我希望能够制作 pets.show.meow()，因为 pets 是一只 Pets[Cat]，show()将返回一只猫。

但是正如我们之前已经发现的，它实际上是一个宠物【动物】，show()将返回一个动物。

最后，我想展示一下 scala 中 Function1(接受一个输入参数的函数)的定义:

```
trait Function1[-T, +R] extends AnyRef {
def apply(v1: T): R
} 
```

当创建一个函数时，我们已经被告知它是 Function1:

类型，如果我们有类:动物类，狗类扩展动物，牛头犬类扩展狗，猫类扩展动物

```
val doSomething:(Bulldog=>Animal) = (bulldoggy) => new Animal 
 doSomething: Bulldog => Animal = <function1> 
```

我们已经创建了 Function1 [Bulldog，Animal],但是请记住 Function1 具有的方差，R 是协变的，这意味着尽管我们将其声明为 Animal，但我们可以返回任何子类型:

```
val doSomething:(Bulldog=>Animal) = (bulldoggy) => new Cat 
 doSomething: Bulldog => Animal = <function1>

val doSomething:(Bulldog=>Animal) = (bulldoggy) => new Dog
 doSomething: Bulldog => Animal = <function1>

val doSomething:(Bulldog=>Animal) = (bulldoggy) => new Bulldog
 doSomething: Bulldog => Animal = <function1>

val doSomething:(Bulldog=>Animal) = (bulldoggy) => new Animal
 doSomething: Bulldog => Animal = <function1> 
```

这是因为通过利斯科夫，我们可以用动物来代替它的任何亚型。

但是 T 是逆变的，因此:

```
val doSomething:(Bulldog=>Animal) = (doggy: Dog) => new Animal
 doSomething: Bulldog => Animal = <function1>

val doSomething:(Bulldog=>Animal) = (animal: Animal) => new Animal
 doSomething: Bulldog => Animal = <function1>

val doSomething:(Bulldog=>Animal) = (kitty: Cat) => new Animal
 /* <console>:11: error: type mismatch;
 found : Cat => Animal
 required: Bulldog => Animal
 val doSomething:(Bulldog=>Animal) = (kitty: Cat) => new Animal
 ^ /* 
```

在 T (Bulldog)的位置，我们可以将输入参数键入为任何 Bulldog 超类型，

，因为 Bulldog 将始终遵循继承(Bulldog 是狗，也是动物)。又是利斯科夫。

这就是目前所有的协变性，逆变性和不变性！在接下来的文章中， **Scala 泛型 III:类型约束**我们将沉浸在类型约束的世界中，我们将完成 Scala 泛型的介绍！

## 如果你觉得这篇关于泛型中协方差和逆变的文章很有趣，你可能会喜欢…

[Scala 泛型 I: Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限”的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

帖子 [Scala Generics II:泛型中的协方差和逆变](https://apiumhub.com/tech-blog-barcelona/scala-generics-covariance-contravariance/)首先出现在 [Apiumhub](https://apiumhub.com) 上。