# Arduboy 橡胶鸭子

> 原文：<https://dev.to/benji/arduboy-rubber-ducky-2o5a>

[![](img/3d8437d22ca7320b4577f696d5c03c44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HdJTtzCv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Aa-pVLDlmVi925TAobJ2Tjg.gif)

我从 2015 年 5 月开始使用[*ardu boy*T3。当我第一次得到它的时候，我玩了一会儿，但是大部分时间它都在我的抽屉里。](https://www.arduboy.com/)

我关注[*【Hak5】*](https://www.hak5.org/)也有一段时间了，他们的一个项目我一直很喜欢，那就是 [*USB 橡皮鸭*](https://hakshop.com/products/usb-rubber-ducky-deluxe) 。我自己没有，但我一直喜欢自动化 HID 攻击的想法，尤其是 DuckyScript 语言和所有可用的 [*社区有效负载*](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/Payloads) 。

大约一周前，我决定把我的 Arduboy 拿出来，试着为它开发一些小东西。在我意识到它有一个 ATmega32u4 之后，我想我可以使用 [*Dckuino.js*](https://github.com/Thecakeisgit/Dckuino.js) ，一个 DuckyScript 到 Arduino 的转换器，并让它在 Arduboy 上运行。

一旦我有了一个基本的 DuckyScript，我想我可以添加多个 DuckyScript 并编写一个选择菜单，以便利用 Arduboy 上的按钮。

最后，我在初始启动过程后添加了一个闪屏，显示一个橡皮鸭图标。为了进入鸭脚本的实际选择菜单，您必须按下 [*Konami 码*](https://en.wikipedia.org/wiki/Konami_Code) 序列。之后你应该会看到一个菜单，上面有 Arduboy 的所有有效载荷。

#### 设置

首先你需要做的是在你自己的 Arduboy 上安装并运行这个程序，这是一个 Arduboy 橡皮鸭代码 和 Arduino IDE 的副本。

从 Arduino IDE 的板管理器中安装 Arduboy 也是一个好主意。为此，只需转到首选项，并将以下内容添加到“附加公告板管理器 URL”:

[https://ardu boy . github . io/board-support/package _ ardu boy _ index . JSON](https://arduboy.github.io/board-support/package_arduboy_index.json)

然后你可以进入工具>电路板>电路板管理器，搜索 Arduboy 并安装它。

我还建议安装 Arduboy 2T3[*库，因为 Arduboy 库的主要分支目前没有被维护。*](https://github.com/MLXXXp/Arduboy2)

一旦一切都准备好并开始运行，尝试一些例子甚至一些来自 [*Arduboy 团队*](http://team-arg.com/games.html) 的游戏是一个好主意，只是为了习惯上传到板上的过程。

当你准备好上传 Arduboy 橡皮鸭代码到你的 Arduboy 上时，你只需要按下上传按钮就可以了。

我已经在我的代码中包含了基本的“Hello World”有效负载，但是你可以编写自己的 DuckyScript 有效负载，或者从 [*社区有效负载*](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/Payloads) 中获取一些。你可以通过使用 [*Dckuino.js*](http://benji.dog/ducky) 转换器从 DuckyScript 转换到 Arduino，我把所有的东西都放在一个地方。在转换您的 DuckyScript 有效负载之后，您可以复制设置函数的内容，并将其添加到 ducky.h 文件中。

然后向下滚动到 ducky.h 文件的末尾，将刚刚创建的函数添加到列表中，并为菜单添加一个标签。

#### 问题

我在测试中注意到，来自 Arduboy 的有效载荷运行得有点慢。您可能会发现，您必须增加脚本中的延迟，以便命令在适当的时间运行。

根据我使用的有效载荷，我有时也会遇到大小问题，所以这是你选择有效载荷时要记住的事情。

#### 未来的工作

我将尝试在某个时候得到一个真正的 USB 橡皮鸭，这样我就可以比较执行时间。通过这种比较，我应该能够找出什么样的延迟对 Arduboy 更有意义，并且我可以修改 Dckuino.js 来专门为它进行转换。

#### 提问

如果你有任何问题，请随时联系我。

也可以在 Twitter 关注 [*@benjifs*](https://twitter.com/benjifs) 。

#### 链接

*   [*GitHub 回购*](https://github.com/benjifs/arduboy-rubber-ducky)
*   [*Duckyscript 到 Arduino 转换器*](http://benji.dog/ducky)
*   [*阿杜博伊*T3】](https://www.arduboy.com/)
*   [*Arduino IDE*](https://www.arduino.cc/en/Main/Software)
*   [*USB 橡皮鸭*](https://hakshop.com/products/usb-rubber-ducky-deluxe)

* * *