# PHP 中的依赖注入

> 原文：<https://dev.to/sarfraznawaz2005/dependency-injection-in-php-1ola>

这就是维基百科对依赖注入的看法:

> 在软件工程中，依赖注入是一种实现软件库控制反转的软件设计模式。调用者将发现和导入由调用者指定或“注入”的服务或软件模块的控制流委托给外部框架。

简单来说，依赖注入是一种设计模式，它帮助*避免一些代码或软件的硬编码依赖*。

依赖关系可以在运行时*和编译时*改变。我们可以使用依赖注入来编写模块化、可测试和可维护的代码:

*   模块化:依赖注入有助于创建完全自给自足的类或模块
*   可测试的:它有助于容易地编写可测试的代码，例如单元测试
*   可维护性:由于每个类都变得模块化，管理起来变得更加容易

* * *

## 问题

我们的代码中几乎总是存在依赖关系。考虑下面这个非常常见的程序示例:

```
function getUsers() {
     global $database;
     return $database->getAll('users');
} 
```

这里函数`getUsers`依赖于变量`$database`(紧耦合)。它有一些这样的问题:

函数`getUsers`需要一个**工作**连接到*某个数据库*。是否成功连接到数据库是`getUsers`功能的命运

*   `$database`来自外部作用域，因此它可能会被同一作用域中的其他库或代码覆盖，在这种情况下，函数可能会失败

当然，您可以使用`try-catch`构造，但是它仍然不能解决第二个问题。

让我们考虑另一个类的例子:

```
class User 
{
    private $database = null;

    public function __construct() {
        $this->database = new database('host', 'user', 'pass', 'dbname');
    }

    public function getUsers() {
        return $this->database->getAll('users');
    }
}

$user = new User();
$user->getUsers(); 
```

这段代码也有这些问题:

类`User`对特定的`database`有隐式依赖。所有的依赖关系应该总是显式的而不是隐式的。这挫败了[依赖倒置原则](http://en.wikipedia.org/wiki/Dependency_inversion_principle)

如果我们想改变数据库凭证，我们需要编辑`User`类，这是不好的；每一个类都应该是完全**模块化的**或者黑盒。如果我们需要对它进行进一步的操作，我们实际上应该使用它的公共属性和方法，而不是反复编辑它。这破坏了[开/关原理](http://en.wikipedia.org/wiki/Open/closed_principle)

让我们假设现在班级正在使用 MySQL 作为数据库。如果我们想使用其他类型的数据库呢？你必须修改它。

`User`类不一定需要知道数据库连接，它应该仅限于自己的功能。所以在`User`类中编写数据库连接代码并不能使其模块化。这挫败了[单一责任原则](http://en.wikipedia.org/wiki/Single_responsibility_principle)。想想这个比喻:一只猫知道如何`meow`，一只狗知道如何`woof`；你不能把它们混在一起，也不能指望狗说`meow`。就像真实世界一样，类中的每个对象都应该负责自己的特定任务。

*   为`User`类编写单元测试会变得更加困难，因为我们在它的构造函数中实例化了数据库类，所以不测试数据库类就不可能为`User`类编写单元测试。

## 进入依赖注入！

让我们看看如何通过使用依赖注入来解决上述问题。依赖注入只不过是**显式地注入一个依赖**。让我们重写以上类:

```
class User 
{
    private $database = null;

    public function __construct(Database $database) {
        $this->database = $database;
    }

    public function getUsers() {
        return $this->database->getAll('users');
    }
}

$database = new Database('host', 'user', 'pass', 'dbname');
$user = new User($database);
$user->getUsers(); 
```

由于依赖注入原则，你有了更好的代码。注意，代替硬编码数据库依赖的是:

```
$this->database = new database('host', 'user', 'pass', 'dbname'); 
```

我们现在把它注入到构造函数中，就是这样:

```
public function __construct(Database $database) 
```

还要注意我们现在是如何传递数据库实例的:

```
$database = new Database('host', 'user', 'pass', 'dbname');
$user = new User($database);
$user->getUsers(); 
```

它遵循了好莱坞的原则，即:**“不要打电话给我们，我们会打电话给你。”**

让我们看看这个显式依赖注入现在是否解决了我们上面提到的问题。

* * *

> 类 User 对特定的数据库有隐式的依赖。所有的依赖都应该是显式的，而不是隐式的。这违背了依赖性反转原则

我们已经通过将数据库依赖关系要求到`User`类的构造函数中而使其显式化:

```
public function __construct(Database $database) 
```

这里我们利用**类型提示**,通过指定我们期望的对象类型`Database`,虽然这不是必需的，但是尽可能键入提示总是一个好主意。

> 如果我们想改变数据库凭证，我们需要编辑用户类，这是不好的；每一个类都应该是完全模块化或者黑盒的。如果我们需要对它进行进一步的操作，我们实际上应该使用它的公共属性和方法，而不是反复编辑它。这违背了开放/封闭原则

`User`类现在不需要担心数据库是如何连接的。它所期望的只是`Database`实例。我们不再需要编辑`User`类的依赖关系，我们已经提供了它所需要的。

> 让我们假设现在班级正在使用 MySQL 作为数据库。如果我们想使用其他类型的数据库呢？你必须修改它。

同样，`User`类不需要知道使用的是哪种类型的数据库。对于`Database`，我们现在可以为不同类型的数据库创建不同的适配器，并传递给`User`类。例如，我们可以创建一个`interface`，它将为所有不同类型的数据库类强制执行公共方法，这些方法必须由它们来实现。对于我们的例子，我们假设接口会强制在不同类型的数据库类中有一个`getUser()`方法需求。

> 用户类不一定需要了解数据库连接，它应该仅限于自己的功能。所以在用户类中编写数据库连接代码并不能使其模块化。这违背了单一责任原则。

当然`User`类现在不知道数据库是如何连接的。它只需要一个有效的连接的`Database`实例。

> 为用户类编写单元测试会变得更加困难，因为我们在构造函数中实例化了数据库类，所以不测试数据库类就不可能为用户类编写单元测试。

如果你已经编写过单元测试，你知道现在使用类似于[mock](https://github.com/padraic/mockery)或类似的东西为`Database`创建模拟对象来为`User`类编写测试将是轻而易举的事情。

* * *

## 注入依赖关系的不同方式

现在我们已经看到了依赖注入是多么有用，让我们看看注入依赖的不同方法。有三种方法可以注入依赖关系:

*   构造函数注入
*   定型剂注射
*   界面注入

**构造函数注入**

我们已经在上面的例子中看到了**构造函数注入**的例子。构造函数注入在以下情况下很有用:

依赖项是**必需的**，没有它类就不能工作。通过使用构造函数注入。我们确保它所有必需的依赖项都通过了。

*   由于构造函数只在实例化一个类的时候被调用，我们可以确保它的依赖关系在对象的生命周期内不会被改变。

构造函数注入有一个问题:

*   由于构造函数具有依赖性，在子类中扩展/覆盖它变得相当困难。

**设定器喷射**

不同于构造函数注入使得**需要**来传递它的依赖关系，setter 注入可以用来拥有**可选的依赖关系**。让我们假设我们的`User`类不需要`Database`实例，但是可以选择用于某些任务。在这种情况下，您可以使用 setter 方法将`Database`注入到`User`类中，类似于:

```
class User 
{
    private $database = null;

    public function setDatabase(Database $database) {
        $this->database = $database;
    }

    public function getUsers() {
        return $this->database->getAll('users');
    }
}

$database = new Database('host', 'user', 'pass', 'dbname');
$user = new User();
$user->setDatabase($database);
$user->getUsers(); 
```

如你所见，这里我们使用了`setDatabase()` setter 函数将`Database`依赖注入到`User`类中。如果我们需要一些其他的依赖项，我们可以再创建一个 setter 方法并以类似的方式注入。

因此 Setter 注入在以下情况下很有用:

*   一个类需要可选的依赖项，这样它就可以用默认值来设置自己，或者添加它需要的额外功能。

> 注意，你也可以通过**公共属性**为一个类注入依赖。因此，除了使用 setter 函数`$user->setDatabase($database);`，您还可以使用`$user->database = new Database(...);`

**接口注入**

在这种类型的注入中，一个接口强制实现它的任何类的依赖性，例如:

```
interface someInterface {
    function getUsers(Database $database);
} 
```

现在任何需要实现`someInterface`的类都必须在它们的`getUsers()`方法中提供`Database`依赖。

* * *

## 问题又来了

因此，我们已经看到了将依赖关系注入到一个简单的类中的非常人为的例子，但是在现实世界的应用程序中，一个类可能有许多依赖关系。管理所有这些依赖关系并不容易，因为您需要知道某个类需要哪些依赖关系，以及需要如何实例化它们。让我们以 setter 注入为例:

```
class User 
{
    private $database = null;

    public function setDatabase(Database $database) {
        $this->database = $database;
    }

    public function getUsers() {
        return $this->database->getAll('users');
    }
} 
```

由于在这种情况下依赖项是可选的，我们可能会错误地编写这段代码来获取用户:

```
$user = new User();
$user->getUsers(); 
```

由于我们不知道`getUsers()`方法实际上依赖于`Database`类，这将会产生错误。你可以通过查看`User`类的代码发现这一点，然后意识到在使用`getUsers()`方法之前必须调用`setDatabase()`方法。或者让我们进一步假设，在使用数据库之前，我们需要为`User`类设置某种类型的配置，比如:

```
$user = new User();
$user->setConfig($configArray); 
```

然后我们再次需要记住方法调用的具体顺序:

```
$user = new User();
$user->setConfig($configArray);
$user->setDatabase($database); 
```

所以你一定要记住方法调用的顺序，不先设置配置就不能使用数据库，所以不能:

```
$user = new User();
$user->setDatabase($database);
$user->setConfig($configArray); 
```

这是 setter 注入的例子，但是即使有构造函数注入，如果有许多依赖项，手动管理所有这些依赖项会变得更加困难，并且您很容易在代码中错误地创建多个依赖项实例，这将导致高内存使用率。

您可能想知道依赖注入听起来是件好事，但是这些问题不值得。这不是真的，因为接下来讨论的所有这些问题都有解决方案:)

## 溶液依赖注入容器

当然，手动管理依赖关系是很困难的；这就是为什么你需要一个依赖注入容器。依赖注入容器是自动处理类的依赖关系的东西。如果你使用过 Laravel 或 Symfony，你会知道它们的组件依赖于其他类。他们如何管理所有这些依赖关系？是的，他们使用某种依赖注入容器。

有相当多的 PHP 依赖注入容器可以用于这个目的，或者你也可以写你自己的。每个容器可能有一点不同的语法，但它们在幕后执行相同的事情。

* * *

因此，总之，您必须始终从代码中移除硬编码的依赖项，并使用依赖项注入来注入它们，然后使用一些依赖项注入容器自动管理所有注入的依赖项。

[![](img/b697a63704e4e38bb12245d40be16251.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--umurf28C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/HU4_Yot53Go)