# 与 WebdriverIO 和摩卡一起开车兜风

> 原文：<https://dev.to/klamping/take-a-drive-with-webdriverio-and-mocha-14e>

*原贴于[blog.kevinlamping.com](https://blog.kevinlamping.com/take-a-drive-with-webdriverio-and-mocha/)T3】*

我对自动化测试的第一个兴趣来自于单元测试。

这样，我了解了断言、测试套件、承诺、模拟和大量其他术语。

当用 JavaScript 测试框架 [Mocha](https://mochajs.org/) 配置功能测试工具 [WebdriverIO](http://webdriver.io/) 时，这些知识帮了大忙。

对于那些刚开始的人来说，断言和测试套件的架构可能是一个陌生的领域。

为了对此有所帮助，我想出了一个简单的类比，它将涵盖以下术语以及它们是如何一起工作的:

*   硒
*   浏览器和驱动程序
*   WebdriverIO
*   摩卡
*   断言
*   之前/之前/之后/之后
*   承诺

## 出发出发

这个类比很简单:想象一下，你想要自动上下班，而 WebdriverIO 生态系统就是你如何管理它。有几件事需要考虑:

*   要开的车
*   如何驾驶汽车
*   要走的方向
*   要遵循的速度限制
*   如何安全轮流
*   处理交通延误

## 汽车驾驶-浏览器&司机

在我们到达任何地方之前，我们需要有一辆车来驾驶(或者自行车，对于那些感觉更环保的人来说)。在我们的比喻中，汽车就是浏览器。我们可以选择任何我们喜欢的模式: [Chrome，IE，Safari，Firefox 等。](http://www.seleniumhq.org/about/platforms.jsp)

WebdriverIO 将浏览器设置在[初始配置](http://webdriver.io/guide/getstarted/configuration.html#desiredCapabilities)。你甚至可以通过[遥控](http://webdriver.io/guide/usage/multiremote.html)选择驾驶多辆车去上班。

驱动程序是 Selenium 的特定浏览器集成(把它们想象成你想驾驶的汽车的钥匙)。在去任何地方之前，你需要为你的浏览器选择安装这些。你可以[手动](http://www.seleniumhq.org/about/platforms.jsp)完成这项工作，但是使用像[selenium 独立模块](https://www.npmjs.com/package/selenium-standalone)这样的软件包要容易得多，它会自动为你设置好。

## 如何驾驶汽车——硒

没有人(或东西)驾驶汽车是没有用的。就像浏览器一样，汽车可以通过人类的命令手动操作，但这有什么乐趣呢？

Selenium 是我们购买的机器人，用于控制汽车的输入(方向盘、油门、刹车)和读取汽车的输出(车速表、燃油表)。这是一个友好的机器人，与更邪恶的机器人无关。

你必须做些工作让它运转起来。为了启动机器人，我建议使用[NPM 硒独立模块](https://www.npmjs.com/package/selenium-standalone)来处理安装。你甚至可以从[酱实验室](https://saucelabs.com/)或[浏览器堆栈](https://www.browserstack.com/)这样的服务中借用机器人。

## 要走的方向-网络驱动

现在你已经让你的机器人准备好出发了，你需要指导它向哪里行驶。这就是 WebdriverIO 的用武之地。

WebdriverIO 知道如何获取我们的 JavaScript 指令，并将它们传递给我们的 Selenium 机器人。WebdriverIO 知道如何说/问一大堆事情。

编写脚本就像将一组指令捆绑在一起一样简单。在驾驶方面，也许你想做到以下几点:

1.  退出车道
2.  在停车标志处向左转
3.  在第二个红绿灯右转

直到你开始工作。对于浏览器，您可以执行以下一组任务:

1.  加载网站 URL
2.  点击“联系”链接
3.  在右侧的表单字段中输入电子邮件地址

WebdriverIO 允许我们说我们熟悉的语言(JavaScript)，从机器人那里获得信息。

顺便说一下，我们的机器人会说多种语言。除了 WebdriverIO，还有其他工具可以与 Selenium 接口。如果你不喜欢 JavaScript，你可以用 [Ruby](https://github.com/jnicklas/capybara) 、 [Python](https://selenium-python.readthedocs.org/) ，甚至[小黄瓜语法](https://cucumber.io/)来编写指令。

## 限速跟随-断言

想象一下你的机器人朋友开车送你上下班；它的目标是让你尽可能快地到达那里。这意味着它可能会比规定的速度限制更快，因为它并不知道速度限制是什么。这也许能让你去工作，但我肯定警察会对你的计划说些什么。

为了避免违反法律，我们可以教机器人在限速标志处验证它的速度。在测试语言中，这被称为“断言”。你“断言”一个值等于另一个值。在我们的类比中，我们断言速度计的读数与限速标志上的数字相匹配。

代码可能是这样的:

```
var speedLimit = 35;
expect(mySpeed).to.equal(speedLimit); 
```

Enter fullscreen mode Exit fullscreen mode

有许多断言风格。甚至还有一个名为 Chai 的断言库，允许你选择最适合你的风格，同时保留相同的底层功能。

上面的例子使用了“expect”风格，但是这里还有一些其他的风格:

**应该**T2】

```
mySpeed.should.equal(35); 
```

Enter fullscreen mode Exit fullscreen mode

**断言**

```
assert.equal(mySpeed, 35); 
```

Enter fullscreen mode Exit fullscreen mode

断言风格的选择由你决定；真的是偏好的事情。只要[告诉摩卡你想要什么](https://mochajs.org/#assertions)它就会明白。

## 如何安全轮流——摩卡&前/后挂钩

我们还没有讨论过摩卡是如何融入其中的。如果 Chai 是我们用来定义期望的断言库，那么 Mocha 是做什么的？

实际上，很多。断言对于测试代码是必不可少的，但是需要大量的设置和组织来确保我们的测试/方向不会变得臃肿。

回到我们的驾驶类比；在我们通勤的过程中，我们需要多次转弯才能到达目的地。每次转弯都需要我们减速，转动方向盘，然后加速。我们可以为每一个转弯编写代码，但是如果我们可以在每次重定向之前和之后指定一组步骤呢？

这就是摩卡可以帮忙的地方。Mocha 是一个测试框架，它为我们的断言增加了一些细节。主要的特性是`describe` / `it`块，这有助于组织我们的测试套件。

下面是我们指令的一个伪例子:

```
describe('car driving to work', function() {
    it('should turn left at 32nd', function() {
        slowDown();
        turnWheel(360deg);
        expect(direction).to.equal('north');
        speedUp();
    });
    it('should turn right at 41st', function() {
        slowDown();
        turnWheel(-360deg);
        expect(direction).to.equal('east');
        speedUp();
    });
    it('should turn left at 53rd', function() {
        slowDown();
        turnWheel(360deg);
        expect(direction).to.equal('north');
        speedUp();
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到我们在所有的测试中都调用了`slowDown`和`speedUp`。这使得测试代码膨胀，测试变得冗长。

摩卡还有另一个锦囊妙计来帮忙。每个`describe`块可以有 [a `beforeEach`和`afterEach`钩子](https://mochajs.org/#hooks)，它们将在每个`it`指令之前/之后运行。

下面是更新后的代码示例:

```
describe('car driving to work', function () {
    beforeEach(function() {
        slowDown();
    });

    it('should turn left at 32nd', function() {
        turnWheel(360deg);
        expect(direction).to.equal('north');
    });
    it('should turn right at 41st', function() {
        turnWheel(-360deg);
        expect(direction).to.equal('east');
    });
    it('should turn left at 53rd', function() {
        turnWheel(360deg);
        expect(direction).to.equal('north');
    });

    afterEach(function() {
        speedUp();
    });
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们不必在每次测试中重复自己，并且可以保留特定于特定指令的`it`块。

Mocha 还有`before`和`after`函数，允许我们在整个测试套件之前/之后运行一组一次性的指令:

```
describe('car driving to work', function() {
    before(function() {
        startCar();
    });

    beforeEach(function() {
        slowDown();
    });

    it('should turn left at 32nd', function() {
        turnWheel(360deg);
        expect(direction).to.equal('north');
    });

    // more tests here

    afterEach(function() {
        speedUp();
    });

    after(function() {
        turnCarOff();
    });
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们只需要在测试套件开始时启动汽车。每次测试后都不需要启动/停止它。您可以使用`before`、`beforeEach`、`afterEach`和`after`来帮助您保持测试的整洁和高效。

## 处理交通延误-承诺

最后一个例子来结束我们的类比。即使你已经完全提前写下了你的指示，机器人也不能马上执行。我们也不能依赖于时间，因为到达一条街道所需的时间很大程度上取决于路上的交通状况。

类似地，浏览器会因网络延迟、服务器繁忙等原因而受阻。每个动作都需要不确定的等待时间来执行。

为了解决这个问题，我们使用[承诺](https://promisesaplus.com/)来管理等待。有了承诺，我们可以顺序编写代码，并内置延迟处理。不过我们确实需要传递承诺，所以 Mocha 和 WebdriverIO 是同步的。

WebdriverIO 带有内置的承诺处理功能，所以你真的不需要做任何事情就能让它正常工作。提到这一点很重要，这样你就知道了这个工具的内部工作原理。

## 整理完毕

类比到此结束。总而言之:

*   浏览器是要驾驶的汽车。它们由安装的 Selenium 浏览器驱动程序驱动。
*   Selenium 是知道如何驾驶汽车的机器人。
*   WebdriverIO 用一种我们很容易编写的语言提供了使用 Selenium 的指导。
*   断言有助于验证速度限制是否得到遵守。Chai 是一个常用的断言库。
*   诸如 before/after 之类的挂钩有助于减少代码重复，使安全轮换变得更加容易
*   承诺有助于处理因沿途拥堵造成的延误