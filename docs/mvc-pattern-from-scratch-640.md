# MVC 模式从零开始

> 原文：<https://dev.to/sebastianr1982/mvc-pattern-from-scratch-640>

在开始之前，读者认为这是我的个人观点，我如何看待这些事情，而不是实现或使用 MVC 模式的唯一方式。

在互联网上，可以找到很多现成的实现和 MVC 框架，我决定从头开始，因为这是深入了解一件事情的最好方法。

经过数小时的理论和实践学习，为了理解什么是*模型*、*视图*、*控制器*以及它们做什么，问题成倍增加。

我经常看到模式组件的工作负载不平衡，*控制器*完成大部分工作:接收和过滤用户输入的数据，操纵*模型*，调用*视图*等。

为什么如果模式有三个组成部分，那么控制器*应该做大部分的工作？*控制器*是指该组件控制其他组件还是模式生命周期？称之为控制器模式不是更好吗？*

在其他人的研究之后，一个理论上的 MVC 模式开始在我的脑海中形成，在这个模式中，组件的责任被平均分配。

我有组件:

*   *模型* - >商业逻辑；
*   *查看* - >显示信息；
*   *控制器* - >接受用户输入；

如何在不使*控制器*过载的情况下创建它们之间的关系？

考虑到*模型*应该接收来自*控制器*的输入数据，对其进行处理，完成所有需要的动作；如果数据被更新以生成新的格式良好的输出，*模型*应通知*视图*；*控制器*应过滤输入数据并激活*模块*；

一个可能的解决方案是...

*   *模式*和*视角*可以通过[观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)说话；
*   *控制器*可以将数据传递给*模型*，直接调用合适的*模型*方法；
*   *控制器*和*模型*也可以通过观察者模式链接，但是这种方式要复杂一点，因为*模型*应该同时是*主体*和*观察者*。

和*视图*相比，*控制器*？

*   *视图*和*控制器*之间的交互可能是不必要的！

是时候了，记住以前的“公理”,编写大量 PHP 代码。:)

## 代码

分为三个父类:*模型*、*视图*、*控制器*，定义了每个组件的角色和关系。后三类为具体例子:*calculator model*， *CalculatorView* ， *CalculatorController* 。它们继承自父类。最后运行 MVC 模式。

### 家长班级

也可能是抽象类，但现在不是了。

#### 型号

包含用于*主题*角色(提醒观察者模式)、通知*观察者*状态变化的方法。

```
<?php

/**
 * Parent class for custom model classes.
 *
 * This class was implemented like part of Observer pattern
 * https://en.wikipedia.org/wiki/Observer_pattern
 * http://php.net/manual/en/class.splsubject.php
 */
class Model implements SplSubject {

    /**
     * @var object List of attached observerer
     */
    private $observers;

    /**
     * @var array Data for notify to observerer
     */
    private $updates = [];

    /**
     * Class Constructor.
     */
    public function __construct() {
        $this->observers = new SplObjectStorage();
    }

    /**
     * Attach an Observer class to this Subject for updates
     * when occour a subject state change.
     *
     * @param SplObserver $observer
     */
    public function attach(SplObserver $observer) {
        if ($observer instanceof View) {
            $this->observers->attach($observer);
        }
    }

    /**
     * Detach an Observer class from this Subject.
     *
     * @param SplObserver $observer
     */
    public function detach(SplObserver $observer) {
        if ($observer instanceof View) {
            $this->observers->detach($observer);
        }
    }

    /**
     * Notify a state change of Subject to all registered Observeres.
     */
    public function notify() {
        foreach ($this->observers as $value) {
            $value->update($this);
        }
    }

    /**
     * Set the data to notify to all registered Observeres.
     *
     * @param array $data
     */
    public function set(array $data) {
        $this->updates = array_merge_recursive($this->updates, $data);
    }

    /**
     * Get the data to notify to all registered Observeres.
     *
     * @return array
     */
    public function get(): array {
        return $this->updates;
    }

} 
```

#### 视图

包含用于*观察者*角色(提醒观察者模式)的方法，从*主题*中检索变更。

```
<?php

/**
 * Parent class for custom view classes.
 *
 * This class was implemented like part of Observer pattern
 * https://en.wikipedia.org/wiki/Observer_pattern
 * http://php.net/manual/en/class.splobserver.php
 */
class View implements SplObserver {

    /**
     * @var array Data for the dynamic view
     */
    protected $data = [];

    /**
     * @var string Output data
     */
    protected $output = '';

    /**
     * @var Model Model for access data
     */
    protected $model;

    /**
     * Class Constructor.
     *
     * @param Model $model
     */
    public function __construct(Model $model) {
        $this->model = $model;
    }

    /**
     * Render a template.
     */
    public function render(): string {
        return $this->output;
    }

    /**
     * Update Observer data.
     *
     * @param SplSubject $subject
     */
    public function update(SplSubject $subject) {
        if ($subject instanceof Model) {
            $this->data = array_merge($this->data, $subject->get());
        }
    }

} 
```

#### 控制器

在控制器上没有太多的发言权。

```
<?php

/**
 * Parent class for custom controller classes.
 */
class Controller {

    /**
     * @var object The model object for current controller
     */
    protected $model = null;

    /**
     * Class Constructor.
     *
     * @param object $model
     */
    public function __construct(Model $model) {
        $this->model = $model;
    }

} 
```

### 具体实例类，一个基本的计算器

我选择了一个基本的计算器作为例子，它提供了加、减、乘、除一个或多个数的可能性。我更喜欢为每一个数学运算写一个方法，因为遵循代码更简单。

将这个具体的例子与文章开头的推理进行比较，我可以说:

*   *模型*:进行数学运算，并将结果通知给*视图*；
*   *视图*:将操作和结果显示为格式良好的输出；
*   *控制器*:过滤用户提供的数字，调用*模型*；

#### 计算器型号

```
<?php

/**
 * Our Custom Model
 */
class CalculatorModel extends Model {

    /**
     * Class Constructor.
     */
    public function __construct() {
        parent::__construct();
    }

    /**
     * Mutiply business logic.
     * 
     * @param array $numbers
     */
    public function multiply(array $numbers) {
        $this->set([
            'result' => $this->operation('*', $numbers),
            'operands' => $numbers,
        ]);
    }

    /**
     * Divide business logic.
     * 
     * @param array $numbers
     */
    public function divide(array $numbers) {
        $this->set([
            'result' => $this->operation('/', $numbers),
            'operands' => $numbers
        ]);
    }

    /**
     * Subtraction business logic.
     * 
     * @param array $numbers
     */
    public function sub(array $numbers) {
        $this->set([
            'result' => $this->operation('-', $numbers),
            'operands' => $numbers
        ]);
    }

    /**
     * Addition business logic.
     * 
     * @param array $numbers
     */
    public function add(array $numbers) {
        $this->set([
            'result' => $this->operation('+', $numbers),
            'operands' => $numbers
        ]);
    }

    /**
     * Do math operation.
     * This is an example, division by 0 and other problematic things not checked.
     * Danger - Very Horrible Code - need refactor.
     *
     * @param string $operator
     * @param array $numbers
     * @return int|float
     */
    private function operation(string $operator, array $numbers) {
        $temp = null;
        foreach ($numbers as $n) {
            if ($temp === null) {
                $temp = $n;
                continue;
            }

            //example, division by 0 and other not checked
            switch ($operator) {
                case '*':
                    $temp = $temp * $n;
                    break;
                case '/':
                    $temp = $temp / $n;
                    break;
                case '-':
                    $temp = $temp - $n;
                    break;
                case '+':
                    $temp = $temp + $n;
                    break;
            }
        }

        return $temp;
    }

} 
```

#### 计算器视图

```
<?php

/**
 * Our Custom Model
 */
class CalculatorView extends View {

    /**
     * Class Constructor.
     * 
     * @param CalculatorModel $model
     */
    public function __construct(CalculatorModel $model) {
        parent::__construct($model);
    }

    /**
     * Build output for multiply.
     */
    public function multiply() {

        $this->output = $this->generateOutput(
                'Multiplication', 
                $this->data['operands'], 
                $this->data['result']
        );
    }

    /**
     * Build output for divide.
     */
    public function divide() {
        $this->output = $this->generateOutput(
                'Division', 
                $this->data['operands'], 
                $this->data['result']
        );
    }

    /**
     * Build output for add.
     */
    public function add() {
        $this->output = $this->generateOutput(
                'Addiction', 
                $this->data['operands'], 
                $this->data['result']
        );
    }

    /**
     * Build output for sub.
     */
    public function sub() {
        $this->output = $this->generateOutput(
                'Subtraction', 
                $this->data['operands'], 
                $this->data['result']
        );
    }

    /**
     * Generate the output.
     * Danger - Very Horrible Code - need refactor.
     *
     * @param string $operation
     * @param array $operand
     * @param type $result
     * 
     * @return string
     */
    private function generateOutput(string $operation, array $operands, $result): string {
        $string = $operation . ': ';

        foreach ($operands as $value) {
            $string .= $value . ' * ';
        }

        $string = substr($string, 0, strlen($string) - 2);
        $string .= '= ' . $result;

        return $string;
    }

} 
```

#### 计算器控制器

```
<?php

/**
 * Our Custom Controller
 */
class CalculatorController extends Controller {

    /**
     * Class Constructor.
     * 
     * @param CalculatorModel $model
     */
    public function __construct(CalculatorModel $model) {
        parent::__construct($model);
    }

    /**
     * Multiply input point.
     * 
     * @param mixed $numbers
     */
    public function multiply(... $numbers) {

        //check user input
        $this->checkOperands($numbers);
        $this->filter($numbers);

        //manipulate the model
        $this->model->multiply($numbers);
    }

    /**
     * Divide input point.
     * 
     * @param mixed $numbers
     */
    public function divide(... $numbers) {

        //check user input
        $this->checkOperands($numbers);
        $this->filter($numbers);

        //manipulate the model
        $this->model->divide($numbers);
    }

    /**
     * Sub input point.
     * 
     * @param mixed $numbers
     */
    public function sub(... $numbers) {

        //check user input
        $this->checkOperands($numbers);
        $this->filter($numbers);

        //manipulate the model
        $this->model->sub($numbers);
    }

    /**
     * Add input point.
     *
     * @param mixed $numbers
     */
    public function add(... $numbers) {

        //check user input
        $this->checkOperands($numbers);
        $this->filter($numbers);

        //manipulate the model
        $this->model->add($numbers);
    }

    /**
     * Filter user supplied numbers.
     * 
     * @param mixed $numbers
     * @throws InvalidArgumentException
     */
    private function filter(&$numbers) {
        foreach ($numbers as $key => $number) {
            switch (gettype($number)) {
                case 'string':
                    $number[$key] = strtonum($number);
                    break;
                case 'integer':
                    break;
                case 'double':
                    break;
                default:
                    throw new InvalidArgumentException('Not a number');
            }
        }
    }

    /**
     * Convert a number given as string in the proper type (int or float).
     * https://secure.php.net/manual/en/language.types.type-juggling.php
     *
     * @param string $string Number as string ex '1.0', '0.9' etc
     * @return int|float
     */
    private function strtonum(string $string) {
        if (fmod((float) $string, 1.0) === 0.0) {
            return (int) $string;
        }

        return (float) $string;
    }

    /**
     * Check minimum numbers required for operations.
     *
     * @param array $numbers
     * @throws ArgumentCountError
     */
    private function checkOperands(array &$numbers) {
        if (count($numbers) < 2) {
            throw new ArgumentCountError('At least two numbers needed for operation');
        }
    }

} 
```

## 跑步

我希望用基本计算器将两个数字(2 和 3)相乘。

```
<?php

//create components
$model = new CalculatorModel();
$view = new CalculatorView($model);
$controller = new CalculatorController($model);

//attach the observer to the subject
$model->attach($view);

//call controller
$controller->multiply(2, 3);

//notify changes to observer
$model->notify();

//call view
$view->multiply();

//get the output
echo $view->render(); 
```

该代码显示为输出:

```
Multiplication: 2 * 3 = 6 
```

## 终于

这并不容易，这个模式看起来非常复杂，而且也不是只有一种实现方式，但是经过一段时间的练习后，一切都变得简单了，它非常灵活，对于代码的组织是一个很好的帮助。值得！

关于运行的最后一个考虑，乍一看，代码应该不起作用，因为在通知之后，*视图*在*模型*之外被调用。

如果对象的范围不同，*视图*(在*模型*之外)如何接收通知？

*模型*在实例内部的局部范围内通知，*视图*在全局范围内调用方法($view- > multiply())。我认为可以在“php 如何将对象传递给函数”中找到解决方案，根据官方文档[默认情况下对象是通过引用传递的](http://php.net/manual/en/language.oop5.references.php)，但我仍然不确定它是否适用于此。

如果你想尝试代码[3v4l.org 可以帮助你](https://3v4l.org/6Rprp)，代码在我的 [Gist 帐户](https://gist.github.com/s3b4stian/09c303576cea9197b547a27684ea4421)上，需要 PHP7 运行。