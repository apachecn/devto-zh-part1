# PHP 中的后期静态绑定

> 原文：<https://dev.to/sarfraznawaz2005/late-static-binding-in-php-2g4b>

让我们先看一个例子来理解这个概念。让我们创建两个类 parent 和 child，它们将使用`getName()`方法告诉我们对象的名称:

```
class Animal {
    protected $name = 'Animal';

    public function getName() {
        return $this->name;
    }
} 
```

让我们扩展`Animal`类，这样我们就可以使用它的`getName()`方法，而不用在子类中重复它。我们只需要子类中的`$name`变量就可以得到它的名字:

```
class Cat extends Animal {
    protected $name = 'Cat';
} 
```

现在我们期望得到每个对象的名字，让我们这样做:

```
$animal = new Animal;
$cat = new Cat;

echo $animal->getName(); // Animal
echo $cat->getName(); // Cat 
```

我们成功地将`Animal`和`Cat`回显出来。但是现在让我们稍微修改一下代码，这样我们就可以使用这些类，而不需要借助`static`关键字(例如全局状态):
来创建它们的实例

```
class Animal {
    protected static $name = 'Animal';

    public static function getName() {
        return self::$name;
    }
}

class Cat extends Animal {
    protected static $name = 'Cat';
}

echo Animal::getName(); // Animal
echo Cat::getName(); // Animal 
```

注意到问题了吗？我们希望看到`Animal`和`Cat`像前面的例子一样被回显，但是在两种情况下，它都显示`Animal`而不是`Cat`。

在前面的例子中，事情按照预期的方式工作的原因是因为我们使用`new`关键字显式地创建了两个类的新实例，并且 PHP 知道使用哪个类和方法。在第二个静态示例中，情况并非如此。在第二个例子中，我们使用了关键字`self`，这个关键字*总是被解析为当前的类*，在那里它被调用。这就是为什么`Cat`类的名字没有出现的原因。

那么猫的名字是怎么来的呢？以下是几种方法。

**通过在子类**
中重复相同的代码

```
class Animal {
    protected static $name = 'Animal';

    public static function getName() {
        return self::$name;
    }
}

class Cat extends Animal {
    protected static $name = 'Cat';

    public static function getName() {
        return self::$name;
    }    
}

echo Animal::getName(); // Animal
echo Cat::getName(); // Cat 
```

这是可行的，但是它违背了继承的目的。当我们需要在子类中重复相同的代码时，扩展`Animal`类有什么意义？这并不理想。

**通过使用`get_called_class()`**

```
class Animal {
    protected static $name = 'Animal';

    public static function getName() {
        $class = get_called_class();
        return $class::$name;
    }
}

class Cat extends Animal {
    protected static $name = 'Cat';
}

echo Animal::getName(); // Animal
echo Cat::getName(); // Cat 
```

这更好，并且符合我们的目的。

**通过使用`static`关键字**

PHP 5.3 引入了`static`关键字来帮助处理这个问题。在此之前，使用的是`get_called_class()`。让我们使用`static`关键字:
得到预期的结果

```
class Animal {
    protected static $name = 'Animal';

    public static function getName() {
        return static::$name;
    }
}

class Cat extends Animal {
    protected static $name = 'Cat';
}

echo Animal::getName(); // Animal
echo Cat::getName(); // Cat 
```

这也很好。这些天来，使用`static`关键字代替`get_called_class()`来处理这个问题似乎是一种常见的做法，尽管`get_called_class()`还有许多其他用途。

* * *

所以简单地说，**后期静态绑定**是帮助我们在运行时正确解析**静态**类的东西。因此，当我们使用`self`关键字时，PHP 会在编译时检查它将方法调用绑定到哪个类，但是当我们使用`static`关键字时，PHP 会在**后**检查它，例如，它会在运行时确定使用哪个类并将方法调用绑定到哪个类。在运行时这样做有助于 PHP 确定哪个类是正确的。

[![](img/1ddb8145f598f41302fedbf4db098a03.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F2MraTTn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/SjpISpV1Dj0)