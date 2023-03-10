# 如何通过单元测试来测试特征

> 原文：<https://dev.to/suin/how-to-test-traits-by-unit-testing-a2l>

由于 PHP 的特性不能被自身实例化，单元测试似乎是不可能的。然而，通过创建使用特征的普通类，您可以为特征编写测试代码。

```
<?php

trait Greetable {
    public function hello(int $repeat): string {
        return str_repeat('Hello!', $repeat);
    }
}

class GreetableBeings {
    use Greetable;
}

class GreetableTest {
    public function testHello() {
        $g = new GreetableBeings();
        assert($g->hello(1) === 'Hello!');
        assert($g->hello(2) === 'Hello!Hello!');
        assert($g->hello(3) === 'Hello!Hello!Hello!');
    }
}

(new GreetableTest)->testHello(); 
```

Enter fullscreen mode Exit fullscreen mode

由于在 PHP 7 中可以使用[匿名类](http://php.net/manual/en/language.oop5.anonymous.php),我们不再需要为测试创建一个类。

```
<?php

trait Greetable {
    public function hello(int $repeat): string {
        return str_repeat('Hello!', $repeat);
    }
}

class GreetableTest {
    public function testHello() {
        $g = new class { use Greetable; }; // anonymous class
        assert($g->hello(1) === 'Hello!');
        assert($g->hello(2) === 'Hello!Hello!');
        assert($g->hello(3) === 'Hello!Hello!Hello!');
    }
}

(new GreetableTest)->testHello(); 
```

Enter fullscreen mode Exit fullscreen mode