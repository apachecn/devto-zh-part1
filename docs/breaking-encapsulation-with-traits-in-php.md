# 用 PHP 中的特征打破封装

> 原文：<https://dev.to/purplebooth/breaking-encapsulation-with-traits-in-php>

PHP 中的特征是危险的，因为它们允许意外破坏封装，而没有任何实际的好处。在这个例子中，你可以看到一个 traits 和一个可调用类的例子，你可以在这里使用任何`callable`类型来达到同样的效果。您可以通过小心谨慎并且不在 traits 中设置任何成员变量来避免这种情况，但是每个人都会犯错误，所以当封装免费提供给`callable`类型时，为什么要冒险呢？

```
<?php

declare(strict_types=1);

use PurpleBooth\EncapsulationTraits\ExampleClass;

require_once __DIR__.'/vendor/autoload.php';

$instance = new ExampleClass();

echo sprintf("%s:\t%s\n", 'new', $instance->getName());
$instance->changeNameTrait();
echo sprintf("%s:\t%s\n", 'trait', $instance->getName());

$instance = new ExampleClass();
echo sprintf("%s:\t%s\n", 'new', $instance->getName());
$instance->changeNameInvokable();
echo sprintf("%s:\t%s\n", 'anonf', $instance->getName()); 
```

Enter fullscreen mode Exit fullscreen mode

```
<?php

declare(strict_types=1);

namespace PurpleBooth\EncapsulationTraits;

class ExampleClass
{
    use ExampleTrait;

    private $name = 'Initial';
    private $changeNameInvokable;

    /**
     * ExampleClass constructor.
     */
    public function __construct()
    {
        $this->changeNameInvokable = new ExampleInvokable();
    }

    public function changeNameInvokable(): void
    {
        call_user_func($this->changeNameInvokable);
    }

    public function getName(): string
    {
        return $this->name;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<?php

declare(strict_types=1);

namespace PurpleBooth\EncapsulationTraits;

trait ExampleTrait
{
    public function changeNameTrait(): void
    {
        $this->name = 'I broke encapsulation';
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<?php

declare(strict_types=1);

namespace PurpleBooth\EncapsulationTraits;

class ExampleInvokable
{
    public function __invoke(): void
    {
        $this->name = 'I do nothing';
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ php run.php 
new:    Initial
trait:  I broke encapsulation
new:    Initial
anonf:  Initial 
```

Enter fullscreen mode Exit fullscreen mode

在 github 上编写更容易运行的表单