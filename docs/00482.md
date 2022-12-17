# 为什么你必须总是遵循测试来打破原则

> 原文：<https://dev.to/robdwaller/why-you-must-always-follow-test-to-break-principles-55nd>

在构建应用程序和编写代码时，有两种类型的测试可以运行来检查一切是否正常。第一个是“测试工作”，这些测试在正常情况下检查你的代码工作。

以汽车为例，您可以创建一个测试来检查您何时将钥匙插入点火装置，然后转动钥匙，发动机启动。

```
Given there is a car with an ignition
When I insert a key
And turn the key
Then the car's engine will start 
```

类似地，使用代码您将编写一个单元测试，当您将一个预期的参数传递给一个方法时，它会检查您是否返回了预期的值。您编写并执行这些测试来证明您的应用程序或汽车工作正常。

虽然“测试到工作”很重要，而且你应该总是写测试来证明你的代码工作，你也必须“测试到崩溃”。这是你对你的代码和应用提出困难和意想不到的问题的地方。这很重要，因为你可以通过编写“测试到工作”的测试来实现 100%的代码覆盖率和非常高的路径覆盖率。然而,“测试工作”并不能保证你的代码在所有情况下都能工作。

回到我们的汽车例子,“断裂测试”可能如下所示。将一把螺丝刀插入点火装置，扭动它，发动机不能启动。另一组需要突破的测试可能会问，如果一辆车以 20 英里/小时、40 英里/小时、60 英里/小时和 80 英里/小时的速度撞上一堵墙会发生什么。

```
Given a moving car is traveling at 20mph
When it is guided into a brick wall
Then the seat belt tightens
And the airbag deploys 
```

这些测试中的每一项都检查某些东西没有损坏，在这两种情况下，我们的测试都检查在异常情况下会发生什么。不太可能有人会以每小时 60 英里的速度开车撞到墙上，但是你仍然应该测试一下会发生什么。

代码应该以完全相同的方式进行测试。让我们看一个例子，假设我们有一些神奇的代码，为汽车订购系统构建汽车对象。像福特汽车公司一样，我们喜欢黑色汽车，所以用户只能订购黑色汽车。还有另外两个规则，所以我们的应用规则如下。

*   汽车必须是黑色的
*   汽车必须有四个轮子
*   汽车必须有三或五扇门

首先，我们编写一个测试来构建一个接受三个参数`$colour`、`$wheels`和`$doors`的汽车对象。然后有三个 getter 方法来检索我们将对其运行断言的信息。

```
public function testMakeCar()
{
    $car = new Car('black', 4, 5);

    $this->assertEquals('black', $car->getColour());

    $this->assertEquals(4, $car->getWheelCount());

    $this->assertEquals(5, $car->getDoorCount());
} 
```

然后，我们当然要编写我们的汽车类来完成测试...

```
 namespace App;

class Car
{
    private $colour;

    private $wheels;

    private $doors;

    public function __construct(string $colour, int $wheels, int $doors)
    {
        $this->colour = $colour;

        $this->wheels = $wheels;

        $this->doors = $doors;
    }

    public function getColour(): string
    {
        return $this->colour;
    }

    public function getWheelCount(): int
    {
        return $this->wheels;
    }

    public function getDoorCount(): int
    {
        return $this->doors;
    }
} 
```

非常简单，下一步是获取这个汽车对象，并将其传递给一个订单对象，该对象将处理汽车订单。在我们的示例中，order 类将接受一个 Car 对象，并且将有一个输出 Order 语句的方法...

```
You have ordered a black car with 4 wheels and 5 doors 
```

我们还想按照上面的规则测试三门和五门。所以我们编写了两个测试...

```
public function testOrderFiveDoorCar()
{
    $car = new Car('black', 4, 5);

    $order = new Order($car);

    $this->assertEquals(
        'You have ordered a black car with 4 wheels and 5 doors',
        $order->getOrderDetails()
    );
}

public function testOrderThreeDoorCar()
{
    $car = new Car('black', 4, 3);

    $order = new Order($car);

    $this->assertEquals(
        'You have ordered a black car with 4 wheels and 3 doors',
        $order->getOrderDetails()
    );
} 
```

然后，我们编写订单类代码来完成测试...

```
 namespace App;

use App\Car;

class Order
{
    private $car;

    public function __construct(Car $car)
    {
        $this->car = $car;
    }

    public function getOrderDetails()
    {
        return 'You have ordered a ' . $this->car->getColour() . ' car with ' . $this->car->getWheelCount() . ' wheels and ' . $this->car->getDoorCount() . ' doors';
    }
} 
```

它工作，我们的测试通过，我们有 100%的代码覆盖率，太棒了！！我们还应该达到 100%的路径覆盖率，因为我们的代码中没有控制结构。本质上，我们已经证明了我们的代码是有效的，满足了定义的规则，如果我们以上面定义的方式使用代码，它将会很好地工作。

我们还没有完全测试我们的代码，因为我们还没有写任何“测试到崩溃”的测试。例如，如果我们传递蓝色作为颜色，或者传递 6 个门，或者 8 个轮子到我们的汽车对象，会发生什么？

答案是什么都不用做，我们的代码会很好地执行，我们会从我们的`Order::getOrderDetails()`方法得到如下所示的输出...

```
You have ordered a blue car with 8 wheels and 6 doors 
```

这当然会打破我们所有的申请规则。在现实世界中，另一个开发人员或用户将来可能会意外地使用这些代码。为了把事情做好，我们需要编写一些“测试到崩溃”的测试，检查如果我们的代码被误用会发生什么。

我们将编写三个新的测试，一个检查黑色，一个检查四个轮子，最后一个检查三到五扇门...

```
/**
 * @expectedException App\OrderException
 * @expectedExceptionMessage You must order a black car
 */
public function testOrderBlueCar()
{
    $car = new Car('blue', 4, 3);

    $order = new Order($car);

    $order->getOrderDetails();
}

/**
 * @expectedException App\OrderException
 * @expectedExceptionMessage You must order a car with 4 wheels
 */
public function testOrderEightWheelCar()
{
    $car = new Car('black', 8, 3);

    $order = new Order($car);

    $order->getOrderDetails();
}

/**
 * @expectedException App\OrderException
 * @expectedExceptionMessage You must order a car with 5 or 3 doors
 */
public function testOrderSixDoorCar()
{
    $car = new Car('black', 4, 6);

    $order = new Order($car);

    $order->getOrderDetails();
} 
```

新的测试将使用 [PHPUnit 注释](https://phpunit.de/manual/current/en/appendixes.annotations.html#appendixes.annotations.expectedException)在`Order::getOrderDetails()`方法上强制异常。你可以选择只返回一个错误消息或者一个布尔值`false`，虽然我不喜欢混合的返回类型，也不喜欢返回错误字符串，这是个人喜好...你也可以在 Car 对象构造函数中强加异常，尽管我不喜欢这么做。

然而，现在有了这三个“测试中断”测试，我们就可以明确地应用我们的应用程序规则了。如果收到包含错误数据的 Car 对象，我们的订单流程将会失败，这正是我们想要的。

为了完成这些测试，我简单地将一个私有的`validate()`方法添加到我的 Order 类中，我在`getOrderDetails()`方法中调用这个方法。此外，我添加了一个 OrderException 类，它扩展了 PHP 异常类，我也喜欢显式异常...

```
private function validate(): void
{
    if ($this->car->getColour() !== 'black') {
        throw new OrderException('You must order a black car');
    }

    if ($this->car->getWheelCount() !== 4) {
        throw new OrderException('You must order a car with 4 wheels');
    }

    if ($this->car->getDoorCount() !== 5 && $this->car->getDoorCount() !== 3) {
        throw new OrderException('You must order a car with 5 or 3 doors');
    }
} 
```

我们的代码现在通过了测试，它只能以一种方式使用。开发人员不能在不遵守我们的应用程序规则的情况下向订单对象抛出汽车对象。这意味着我们的代码和应用程序现在更加健壮，并且经过了适当的测试。

我希望我在这篇文章中陈述的一切都是显而易见的，因为这个例子非常简单。然而，在更复杂的应用程序中，事情不太明显，更容易出错，尤其是在集成时。这就是为什么必须遵循“测试破坏”原则，并且总是寻找代码可能被误用的方法。这将有许多好处，您的应用程序和代码将更加健壮，这意味着您将在晚上睡得更好，更重要的是，您将在发布后花费更少的时间来修复 bug。

如果你想查看这篇文章中讨论的代码的工作版本，请查看相关的库。当然，如果你有任何问题或想法，请在 Twitter 上给我发消息 [@RobDWaller](https://twitter.com/RobDWaller)