# 条件句的复杂层次

> 原文：<https://dev.to/clayshentrup/the-hierarchy-of-complexity-for-conditionals>

[*原贴于极限编程*](https://medium.com/extreme-programming/the-hierarchy-of-complexity-for-conditionals-f6763b23f76e#.dxqegbps1)

当 Ruby on Rails 框架首次发布时，它最大的卖点之一就是[约定胜于配置](http://rubyonrails.org/doctrine/#convention-over-configuration)。将您的 ActiveRecord 模型命名为**用户**，它将在其生成的 SQL 中使用**用户**表。这消除了对详细配置的需求。相反，你只需要知道一些规则，你的代码库将是可预测的，并且始终保持一致。

事实证明，这种技术可以用在您自己的代码中。在她用 Ruby 编写的经典 [*实用面向对象设计中，Sandi Metz 提出了一个遵循明显模式的 case 语句的例子(见顶部的 shock.rb)。*](http://www.poodr.com/) 

```
# Source: https://www.youtube.com/watch?v=f5I1iyso29U&t=23m44s
class Shock
  def cost(type)
    case type
    when :front
      FrontShockCost.new.compute
    when :rear
      RearSHockCost.new.compute
    when :lefty
      LeftyShockCost.new.compute
    else
      ShockCost.new.compute
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
class Shock
  def cost(type)
    "#{type.to_s.titleize}Shock".constantize.new.compute
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

Sandi 的观察是，每当你添加或移除一种类型的电击时，这个代码都必须改变。这就增加了改代码的难度(见[霰弹枪手术](https://sourcemaking.com/refactoring/smells/shotgun-surgery)代码气味)。**震惊**有太多的“元知识”。**震惊**知道这四件事。

1.成本法

2。四种冲击费用类型
3
。四个冲击成本类
4
。哪个类型得到哪个类(从类型到类的映射)

我们可以通过用一个*约定*替换这些规则来减少这些信息，如第二个文件所示。桑迪[在她的一次演讲中展示了这一点，她说:](https://www.youtube.com/watch?v=f5I1iyso29U&t=23m44s)

> 在这个转换中，您可以创建一个类名，并将其转换为对象..是一件非常有用的事情。

我遇到的一些软件开发人员回避这种方法，因为他们担心难以搜索调用代码。虽然我认为这种担心是有价值的，但是这种重构的价值在于你拥有的例子越多和/或代码更改越频繁。我不认为人们应该拒绝这种(或任何)基于*原则*的编程方法。与任何商业决策一样，它应该是关于成本/收益的计算。*什么时候*应用约定值得吗？就可搜索性而言，在抽象值回成本之前，您必须排除多少条件分支(理想情况下只是复制相同的约定)？

也就是说，很高兴知道我们的选择。我为条件句提出了类似于“复杂层次”的东西。它从最简单、最不灵活到最详细、最灵活。

创建一个惯例。
如上所述，无论你有三个独特的案例还是一千个，都有一个规则可以遵循。这无疑是最简单的方法(简单的在[丰富的吻痕意义](https://www.infoq.com/presentations/Simple-Made-Easy))。

**使用地图。**
一个 map(就像下面的 Ruby hash)稍微有点冗长，并且没有强制一个关于命名约定的规则。因此，它可以导致信息的增加——多个一次性的规则，而不是一个总括性的规则形成一个惯例。但是它确实执行了规则的第三部分 T4——界面。每个类都应该提供**计算**方法。这并没有创建一个条件，未来的程序员可能会“懒洋洋地”(或者只是*无意中*)为每个特定的用例铲进额外的代码。(Sandi Metz 甚至指出，你可以用一张地图代替一个 **if/else** ，其中的关键字是 **true** 和 **false** 。)

```
shock_cost_class_map = {
  front: FrontShockCost,
  lefty: LeftyShockCost,
  rear: RearSHockCost,
}
shock_cost_class = shock_cost_class_map.fetch(type, ShockCost)
shock_cost_class.new.compute 
```

Enter fullscreen mode Exit fullscreen mode

使用案例陈述。
回头看看我们上面的例子，我们看到每个条件只是一个类型变量和值之间的匹配。这比一系列任意的布尔表达式更简单(不太灵活)，但它仍然为未来的程序员创造了诱人的机会，将新的行为插入到一个或多个 case 语句中，而不仅仅是遵循每个类的 **compute** 方法做相关工作的规则。作为 OO 实践者，我们尽可能避免差异，寻求相同。

**使用任意布尔表达式(if/else)。**
这是最灵活的，在我看来也是最后的手段。因为这不仅给了我们改变每个条件行为的灵活性(例如，用内联代码替换类)，而且在*比较*中也给了我们无限的灵活性。任何布尔表达式都可以。

每当我需要条件行为时，我就从这个层次结构的顶端开始，只有当我的需求迫使我这样做时，我才向下工作。