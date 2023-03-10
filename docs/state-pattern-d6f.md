# PHP 中的状态模式示例

> 原文：<https://dev.to/mnlwldr/state-pattern-d6f>

状态模式非常适合使对象的行为依赖于其状态。在我的例子中，电梯可以有以下状态:

*   开放(开放)
*   关闭(关闭)
*   运动中
*   站立(停止)

由此可以导出下面的界面

```
<?php

namespace Elevator;

interface ElevatorStateInterface
{
    public function open();
    public function close();
    public function move();
    public function stop();
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要一个实现接口
的类 *ElevatorState*

```
<?php

namespace Elevator;

class ElevatorState implements ElevatorStateInterface
{
    public function close()
    {
        throw new \Elevator\Exception\IllegalStateTransitionException();
    }

    public function move()
    {
        throw new \Elevator\Exception\IllegalStateTransitionException();
    }

    public function open()
    {
        throw new \Elevator\Exception\IllegalStateTransitionException();
    }

    public function stop()
    {
        throw new \Elevator\Exception\IllegalStateTransitionException();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，所有方法都会引发异常。在我的例子中，它是一个继承自*逻辑异常*的*IllegalStateTransitionException*。

现在我们可以实现单独的状态。在这个例子中，*移动到*状态。

```
<?php

namespace Elevator\State;

class Move extends \Elevator\ElevatorState
{
    public function move()
    {
        return new Move();
    }

    public function stop()
    {
        return new Stop();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，并不是所有的方法都是从 *ElevatorState* 开始实现的。
正是这一点，为目前的状态所不允许。

*电梯*T2【下课】

```
<?php

namespace Elevator;

class Elevator
{
    private $state;

    function getState()
    {
        return $this->state;
    }

    function setState(ElevatorStateInterface $state)
    {
        $this->state = $state;
        print "set state to : " . get_class($state) . PHP_EOL;
    }

    public function __construct()
    {
        $this->setState(new \Elevator\State\Stop());
    }

    public function isOpen()
    {
        return $this->state instanceof \Elevator\State\Open;
    }

    public function open()
    {
        $this->setState($this->state->open());
    }

    public function close()
    {
        $this->setState($this->state->close());
    }

    public function move()
    {
        $this->setState($this->state->move());
    }

    public function stop()
    {
        $this->setState($this->state->stop());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你用
调用这个类

```
<?php
$elevator = new Elevator\Elevator(); 
```

Enter fullscreen mode Exit fullscreen mode

它通过构造函数得到状态 *Stop* 。这意味着我们可以从这个状态切换到*打开*状态。

```
<?php
$elevator->open(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我试试这个

```
<?php
$elevator->move(); 
```

Enter fullscreen mode Exit fullscreen mode

将会发生错误

```
PHP Fatal error: Uncaught Elevator\Exception\IllegalStateTransitionException 
```

Enter fullscreen mode Exit fullscreen mode

因为门必须先关上。

```
<?php
$elevator->close(); 
```

Enter fullscreen mode Exit fullscreen mode

这个例子的完整源代码可以在 [GitHub](https://github.com/mnlwldr/State-Pattern) 上找到。