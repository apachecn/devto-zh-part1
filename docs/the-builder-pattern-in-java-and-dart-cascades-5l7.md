# Java 中的构建器模式和 Dart 级联

> 原文：<https://dev.to/jvarness/the-builder-pattern-in-java-and-dart-cascades-5l7>

在具有面向对象范例的语言中，对象构造是每个人都必须做的事情。然而，当您的对象有很多成员并且会发生变化时，您如何创建一个健壮的 API 来保护您的消费者免受非被动变化的影响呢？你如何避免拥有多个允许你的用户以不同方式构造对象的构造函数(我称之为*构造函数地狱*)？

## 举个例子

假设您正在开发自己的比萨饼连锁店，并且您想编写一个 Java 应用程序，允许用户创建自己的比萨饼。

对您来说，最合理的做法是创建一个`Pizza`类，它允许您封装比萨饼的概念:

```
public class Pizza {
    private Collection<String> toppings;
    private String sauce;
    private boolean hasExtraCheese;
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来不错，对吗？我们可以为这些成员创建 getter 和 setter 方法，允许我们改变对象的状态:

```
//... other code

public Collection<String> getToppings {
    return this.toppings;
}

public void setToppings(Collection<String> toppings) {
    this.toppings = toppings;
}

// ... other setters and getters 
```

Enter fullscreen mode Exit fullscreen mode

不太坏...但是我们如何建造这些东西呢？？嗯，最简单的答案是手动设置一切:

```
final Pizza pizza = new Pizza();
pizza.hasExtraCheese(true);
pizza.setSauce("garlic");
List<String> toppings = new ArrayList<String>();
toppings.add("pepperoni");
pizza.setToppings(toppings); 
```

Enter fullscreen mode Exit fullscreen mode

这还不算太糟...但是那有很多代码...我们可以创建一个构造函数:

```
public Pizza(Collection<String> toppings, String sauce, boolean hasExtraCheese) {
    // and then you set stuff...
} 
```

Enter fullscreen mode Exit fullscreen mode

这使得代码看起来更像这样:

```
List<String> toppings = new ArrayList<String>();
toppings.add("pepperoni");
final Pizza pizza = new Pizza(toppings, "marinara", false); 
```

Enter fullscreen mode Exit fullscreen mode

这还不算太糟...但是如果我不在乎指定我是否需要额外的奶酪呢？也许提供一种用默认调味汁制作比萨饼的方法会很方便。此时，您可能想做以下事情:

```
public Pizza(Collection<String> toppings) {
    // default the sauce and extra cheese
}

public Pizza(String sauce, boolean hasExtraCheese) {
    // default toppings as empty
} 

//... potentially many more constructors 
```

Enter fullscreen mode Exit fullscreen mode

为了方便起见，您可以创建许多不同的构造函数(任何编写 [Swift](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Initialization.html) 的人都可以得到那个构造函数吗？).

想知道是什么让这个构造函数不那么好吗？当人们开始想要定制他们的比萨饼皮时。

```
public class Pizza {
    private Collection<String> toppings;
    private String sauce;
    private boolean hasExtraCheese;
    private String crust; // OH NO, NEW THING I DIDN'T PLAN FOR!!! WE'RE DOOMED!
} 
```

Enter fullscreen mode Exit fullscreen mode

谁想去写一打构造函数来支持用可选外壳初始化一个`Pizza`？在市场营销告诉你人们想要用酱毛毛雨和面包屑定制他们的披萨作为与必胜客竞争的手段后，谁还想去创造指数级的更多呢？

...

没人吗？酷，让我们写一个生成器。

## 构建器模式

构建器模式允许你*构建*对象，而不是*构建*它们。您在构建器中提供了一个 API，允许您设置一个`Pizza`的所有属性，然后构建器将为您构建对象:

```
public class PizzaBuilder() {
    private Collection<String> toppings;
    private String sauce;
    private boolean hasExtraCheese;
    private String crust;

    public PizzaBuilder withToppings(Collection<String> toppings) {
        this.toppings = toppings;
        return this;
    }

    // ... create a "with" method for each member you want to set

    public Pizza build() {
        final Pizza pizza = new Pizza();
        // set the pizza properties
        return pizza;    
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这使得你的`Pizza`创造**更容易**，它结束**看起来更干净**，它可以帮助你的`Pizza` s **不可变**，并且你的代码现在对变化 :
更加**被动**

```
final Pizza pizza = new PizzaBuilder()
    .withHasExtraCheese(true)
    .withSauce("marinara")
    .withCrust("pan")
    .withToppings(new ArrayList<String>())
    .build(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，当人们使用你的制作 API 时，如果你添加了更多的功能，那么你就不需要创建更多的构造函数，如果其他人不需要的话，他们也不需要关心新功能的实现。

## Dart 如何解决这个问题

Dart 有一些优秀的语法，允许我们跳过构建器的创建，并防止我们进入构造器地狱。再来看 Dart 中同样的`Pizza`类:

```
class Pizza {
    List<String> toppings;
    String sauce;
    bool hasExtraCheese;
} 
```

Enter fullscreen mode Exit fullscreen mode

Dart 很酷的一点是[实例变量](https://www.dartlang.org/guides/language/language-tour#instance-variables)实现了隐式 getters 和 setters。如果实例是最终的，就不会生成 setters。

我们完事了。我们的消费者可以创建`Pizza`实例，并且已经防范了非被动更改！

...

不，我是认真的。你的任务完成了。你做了该做的。你可以回家了。

Dart 有一个优秀的特性叫做[级联符号](https://www.dartlang.org/guides/language/language-tour#cascade-notation-)，它允许你调用对象实例上的 getters、setters 和方法来实例化它们:

```
// don't mind me, just constructing a pizza...
var pizza = new Pizza()
    ..toppings = ['pepperoni', 'mushrooms']
    ..sauce = 'spaghetti'
    ..hasExtraCheese = true; 
```

Enter fullscreen mode Exit fullscreen mode

看起来很像建筑商，但其实不是。现在，如果我们添加更多的实例变量，上面的代码仍然可以正常工作。如果需要，我们的消费者可以稍后添加外壳，*无需重新组装*。

我希望您喜欢查看构建器模式，并且我希望这激发了您对 Dart 的兴趣！