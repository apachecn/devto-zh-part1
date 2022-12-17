# 程序员用视差推进器芯片

> 原文：<https://dev.to/evantypanski/parallax-propeller-chip-for-programmers-9c0>

最近，我在一个关于基本微控制器的研讨会上给那些几乎没有编程经验的人授课。除了琐碎的例子，没有人见过 C 代码。然而，微控制器对我在 Gizmologists(弗吉尼亚大学的一个俱乐部)做的项目非常有用。现在，我想呼吁那些已经了解如何编程的人。

注意，我只会深入到如何对 Propeller 芯片进行编程的语义及其背后的基础知识。我不会涵盖任何可能出现的电路或布线，因为有许多像 Arduino 这样的微控制器教程涵盖了这一材料-在这里完全相同。此外，为了本教程，我假设阅读本教程的人对编程有所了解。我的目标是教授推进器芯片如何在机电一体化中实现其目标。

## 推进器芯片

视差推进器芯片是一个非常独特的微控制器——不像大多数人求助于中断和多线程来解决问题，推进器芯片实际上使用并行处理。这在微控制器领域非常罕见——你几乎不可能在八个并行处理器中的每一个上找到一个具有像样处理能力(每秒 2000 万条指令运行机器代码)的芯片。

Propeller 芯片有两种你可以使用的主要语言:Spin，这是一种高级解释语言，或者 C，就像许多其他微控制器一样。因为 Spin 是为 Propeller 芯片设计的，所以为了使 Propeller 芯片编程更容易，这种语言有自己的特点。例如，即使在高级解释语言中，寄存器操作也可以通过简单的数组索引来完成:`dira[0]~`相当于将 dira 寄存器的第 0 位设置为 0。反之，`dira[0]~~`相当于设置第 0 位等于 1。这个语法相当于 Spin 中的`dira[0] := 0`或`dira[0] := 1`。

一般来说，寄存器是特殊的硬件位置，用于存储小块数据，比如内存地址。有时，寄存器可能包含特殊信息:例如，程序计数器包含当前指令的地址。微控制器上的寄存器通常在日常编程中有更有用的用途:通常，人们可以通过直接改变寄存器的存储值来设置 GPIO(通用输入输出)引脚的状态。这是一个令人难以置信的快速操作，因此非常适合从微控制器操作硬件。

因此，寄存器对于 Propeller 芯片非常重要。最重要的寄存器是`dira`、`outa`和`ina`。`dira`寄存器用于设置引脚是输入(0)还是输出(1)。阵列索引应该是正在分配的管脚号。`outa`寄存器用于设置引脚的输出状态——0(关)或 1(开)。最后，`ina`寄存器用于从某个引脚读取输入，0 或 1。这很可能是一个简单闭合电路的按钮或开关。

一旦我们进入 C 代码，这些基础就不那么重要了，但是理解幕后发生的事情是有用的。把每一个管脚想象成一个开关:`dira`寄存器告诉 Propeller 芯片它是否会读入一些输入值或者输出一些电流。然后，当该引脚的`outa`设置为 1 时，它通过电路发送电流。当`outa`设置为 0 时，它会阻止电流流过该引脚。同样，当`ina`被访问时，螺旋桨芯片将读取它是否看到电压。如果是，那么`ina`将为该引脚读取 1，否则将读取 0。

## 螺旋桨 C

虽然 Spin 是 Propeller 芯片的原始语言，但使用和教授 C 通常更有用，因为更多的人知道 C，而且它在嵌入式系统领域更通用。因此，尽管 C 更像是对 Propeller 芯片的补充，但我将主要研究 Propeller 芯片上的 C 编程。

如果你想跟着做，名为 SimpleIDE 的 Propeller 代码可以使用本教程中的来安装。

C 和 Spin 的主要区别在于 C 抽象了许多硬件交互。例如，不是直接访问`dira`寄存器来设置引脚的方向，而是通过调用函数`set_direction(int pin, int direction)`来设置引脚的方向。比如`dira[0]~~`，将管脚 0 设置为输出(1)，相当于 c 中的`set_direction(0, 1)`

```
// set_direction function

void set_direction(int pin, int direction)
{
    unsigned int mask = 1 << pin;
    if (direction == 0)
    {
        DIRA &= (~mask);
    }
    else
    {
        DIRA |= mask;
    }
} 
```

查看这个函数的源代码，我们可以看到它只是简单地操作了`dira`寄存器。然而，这应该是抽象的——在 C 中操作寄存器稍微复杂一些，因为必须对所需的值进行位移位，并使用按位 and(设置为输入)或按位 or(设置为输出)来设置正确的引脚。例如，通过直接操作 C: `DIRA |= (1 << 2);`中的`dira`寄存器，可以将引脚 2 设置为输出

同样，操作输出也是抽象的。功能`high(int pin)`和`low(int pin)`用于使某个引脚变为高电平(通过它发送电流)或低电平(停止电流)。同样，这两个函数都以类似于上面例子的方式操作`outa`寄存器。

最后，可以使用`input(int pin)`功能读取输入。

有了寄存器如何操作引脚的基本知识，我们来看一个简单的信号灯模拟示例，假设引脚 0 为绿色，引脚 1 为黄色，引脚 2 为红色。

```
#include "simpletools.h"        // Simpletools contains a lot of useful functions

void main() {
    set_direction(0, 1);        // Sets pin 0 to output
    set_direction(1, 1);        // Sets pin 1 to output
    set_direction(2, 1);        // Sets pin 2 to output
    while (1) {                 // Infinite loop
        high(0);                // Sets pin 0 to high
        pause(1000);            // Pauses one second
        low(0);                 // Sets pin 0 to low
        high(1);                // Sets pin 1 to high
        pause(750);             // Pauses 0.75 seconds
        low(1);                 // Sets pin 1 to low
        high(2);                // Sets pin 2 to high
        pause(1000);            // Pauses one second
        low(2);                 // Sets pin 2 to low
    }
} 
```

现在我们可以看到，微控制器的世界并不是一个有着低级硬件操作的可怕地方——事实上，许多硬件交互都被抽象在函数后面。所有需要知道的是发光二极管连接的引脚，一个简单的停止灯应该是微不足道的。

## 并行处理

既然我们已经掌握了微控制器的基础知识，让我们来看看 Propeller 芯片如何同时处理多个进程。

Propeller 芯片在一个主要方面是独特的:它有八个并行处理器，称为 cogs。每个可以存储 2 KB 的 RAM，存储在 512 个 longs 中，每个都与主系统时钟同步。如需更多信息，请查看数据表。其他微控制器，如 Arduino，专注于多线程(或者，在 Arduino 的情况下，protothreading - [见这篇文章](https://create.arduino.cc/projecthub/reanimationxp/how-to-multithread-an-arduino-protothreading-tutorial-dd2c37)了解其实际工作原理)。然而，这只是通过并发来模拟多个进程。螺旋桨芯片允许我们运行交通信号灯，转动马达，不断检查某些开关是否被按下，以及检查时间是否改变，所有这些都没有干扰。

此外，编程非常简单。这是我在研讨会上举的一个例子:

```
#include "simpletools.h"        // Simpletools contains a lot of useful functions

void blinkLED1();               // Forward declaration 

unsigned int stack[100];        // Stack in shared memory as user defined stack space

int main() {
    cogstart(&blinkLED1, NULL, &stack, sizeof(stack));    // Starts new cog process
    set_direction(0, 1);        // Sets pin 0 to output
    while(1) {                  // Infinite loop
        high(0);                // Sets pin 0 to high
        pause(500);             // Pauses 0.5 seconds
        low(0);                 // Sets pin 0 to low
        pause(500);             // Pauses 0.5 seconds
    }
}

void blinkLED1() {
    set_direction(1, 1);        // Sets pin 1 to output
    while(1) {                  // Infinite loop
        high(1);                // Sets pin 1 to high
        pause(750);             // Pauses 0.75 seconds
        low(1);                 // Sets pin 1 to low
        pause(750);             // Pauses 0.75 seconds
    }
} 
```

这是为了直观地展示并行处理是如何实现的:如果引脚 0 和 1 中有一个 LED 显示此代码，它们将以相互偏移的间隔闪烁。同样，这是微不足道的，但非常重要。

用于 cogstart 的栈并不是非常重要——除非需要空间，否则我建议只放 100 个无符号整数，直到空间不够用为止。所需的最低数量是 40 -越多越好。

### 锁

Propeller 芯片允许每个 cog 访问中央 hub RAM，在那里可以提取和操作全局变量。这可能会产生问题，尤其是当另一个齿轮需要使用这个变量的时候。Propeller 芯片具有相对简单的信号量(或锁)实现，以防止竞争条件等的危险。

推进器芯片有八把锁，其中七把可用。锁由整个系统共享，因此每个 cog 可以在操作任何共享数据之前检查锁是否被使用。这是通过以下方式完成的:

函数`locknew()`首先用于获取一个锁 ID。这应该分配给一些变量，所有可能访问数据的 cogs 都可以访问这些变量。然后，`lockset(int lockid)`将设置锁定。如果已经设置了锁，这个函数将在设置锁之前等待直到锁被清除，如果成功则返回 true。然后，一旦数据操作完成，您可以简单地调用`lockclr(int lockid)`来解除锁定并允许其他人使用它。如果在某个时间点之后不再使用锁，明智的做法是使用`lockret(int lockid)`将锁返回到池中，以便其他人可以通过`locknew()`函数获得它。

## 潜水深入

由于 Parallax 专注于教育，似乎大多数推进器教程都是针对年轻一代的。这抽象了我们可能想要了解的关于螺旋桨芯片的大部分内容，因为它确实是一个与电机和传感器集成的奇妙芯片。因此，了解如何阅读所提供的 C 库的源代码是明智的。

图书馆有不错的文档，在这里找到。您还可以进入 Learn 目录，该目录应该在存储您的项目的 SimpleIDE 目录中(这是 Simple IDE 的默认设置)。这些目录中的 HTML 文档非常好，但是您也可以查看源代码，看看幕后发生了什么——也许还可以对其进行改进以提高效率。

此外，Propeller 芯片还有很多我在这篇文章中没有提到的硬件组件——中心 RAM 如何与齿轮、不同的控制寄存器等交互。为了理解这一切，明智的做法是阅读一下[推进器数据表](https://www.parallax.com/sites/default/files/downloads/P8X32A-Propeller-Datasheet-v1.4.0_0.pdf)——这有助于理解幕后发生了什么样的魔法。

## 结论

Parallax Propeller 芯片是一款非常有用的芯片，具有其他微控制器所不具备的并行处理能力。这使得 Propeller 芯片在微控制器的几乎所有应用中特别有用。

虽然我没有提供任何关于引脚和 led 接线的解释，但通过现有的教程，很容易就能感觉到这是如何工作的。视差有[像样的教程](http://learn.parallax.com/tutorials/propeller-c)来学习这个。我想把重点放在推进器芯片如何做它所做的事情上，而不是解释如何让推进器芯片实现某些目标。换句话说，大多数教程告诉你如何去实现一个目标；我想展示螺旋桨芯片如何实现这些目标。

此外，这些情况可能看起来很琐碎。然而，在机电一体化的大多数应用中，你所做的大部分只是简单地操纵引脚何时提供输出或接收输入。了解了特定设备的工作原理，比如步进电机或模拟数字转换器，就很容易通过我的解释得到你想要的东西。