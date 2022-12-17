# 接口编码

> 原文：<https://dev.to/sarfraznawaz2005/coding-to-interface-3n5g>

你可以添加到编程技能中的最好的事情之一是**编写接口**的代码。S.O.L.I.D 的[五项原则之一是](https://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29)[依赖性倒置原则](https://en.wikipedia.org/wiki/Dependency_inversion_principle)，该原则规定:

> 在面向对象编程中，依赖倒置原则指的是一种特定形式的解耦软件模块。当遵循这一原则时，从高级策略设置模块到低级依赖模块建立的传统依赖关系被颠倒，从而使得高级模块独立于低级模块实现细节。原理陈述: [1](https://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29)
> 
> A.高层模块不应该依赖低层模块。两者都应该依赖于抽象。
> 
> B.抽象不应该依赖于细节。细节应该依赖于抽象。

漂亮*正式*定义 hun？实际上让我们通过例子来理解。

假设我们有一个基本的 MySQL 包装类:

```
class Mysql
{
    protected $db = null;

    public function connect($dsn, $user = '', $pass = '')
    {
        $this->db = new PDO($dsn, $user, $pass);
    }

    public function query($query)
    {
        return $this->db->query($query);
    }
} 
```

非常常见和有效的代码在过去，我们曾经为编写这样的类而自豪。但是，有几个相关的问题:

*   类`Mysql`对`PDO`类有隐式依赖(紧耦合)。所有的依赖都应该是显式的，而不是隐式的。
*   由于隐含的依赖性，很难进行单元测试

让我们使用上面的类:

```
class User
{
    private $database = null;

    public function __construct()
    {
        $this->database = new Mysql();
        $this->database->connect('mysql:host=localhost;dbname=test', 'root', '');
    }

    public function getUsers()
    {
        $users = $this->database->query('SELECT * FROM users ORDER BY id DESC');
        print_r($users);
    }
}

$user = new User();
$user->getUsers(); 
```

这又是一段非常普通的代码，但是这一段有更多的问题:

*   类`User`对`Mysql`类有隐式依赖(紧耦合)。所有的依赖都应该是显式的，而不是隐式的。
*   由于隐含的依赖性，很难进行单元测试
*   如果我们想改变数据库凭证，我们需要编辑`User`类，这是不好的；每一个类都应该是完全**模块化的**或者黑盒。如果我们需要对它进行进一步的操作，我们实际上应该使用它的公共属性和方法，而不是反复编辑它。
*   让我们假设现在班级正在使用 MySQL 作为数据库。如果我们想使用其他类型的数据库呢？你必须修改类。
*   `User`类不一定需要知道数据库连接，它应该仅限于自己的功能。所以在`User`类中编写数据库连接代码并不能使其模块化。这挫败了[单一责任原则](http://en.wikipedia.org/wiki/Single_responsibility_principle)。

你可能想知道上面的代码有问题，但是这些天我们基于 MVC 设计模式编写代码，遵循最佳实践并编写*现代代码*但是我确信下面的代码即使在今天使用你所使用的任何框架都是非常普通的:

```
class UserController extends Controller
{
    public function actionListAllUsers()
    {          
       // fetch all users from database
        $users = User::model()->findAll();

       // show them on screen
        print_r($users);          
    }
} 
```

如果你分析一下，就会发现同样的问题，因为类`UserController`与`User`模型类紧密耦合(隐式依赖),这也使得单元测试变得困难。

这意味着:

> 耦合代码是邪恶的大圆点

## 那么有什么解决办法呢？

解决方案是[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection)(见我之前的帖子[PHP 中的依赖注入](http://codeinphp.github.io/post/dependency-injection-in-php/))。依赖注入背后的思想是，我们应该通过构造函数或 setter 方法甚至接口来传递/提供那些依赖，而不是编写隐式依赖。

让我们重构`User`类代码，并通过构造函数传递我们的依赖，而不是硬编码在`User`类方法:
中

```
class User
{
    private $database = null;

    public function __construct(Mysql $database)
    {
        $this->database = $database;
    }

    public function getUsers()
    {
        $users = $this->database->query('SELECT * FROM users ORDER BY id DESC');
        print_r($users);
    }
}

$database = new Mysql();
$database->connect('mysql:host=localhost;dbname=test', 'root', '');
$user = new User($database);
$user->getUsers(); 
```

这样好多了！我们不再有对`Mysql`类的隐式依赖，这也使得我们的`User`类更容易进行单元测试。这个类表明了它需要某种`Mysql`类的意图。它也不知道(也不关心)`Mysql`会从哪里来，它只需要一个来操作。`User`类现在是完全模块化的，遵循单一责任原则(操纵用户的原则)。

用类似的方法，我们也可以改进我们的现代代码:

```
class UserController extends Controller
{
    protected $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }

    public function actionListAllUsers()
    {          
       // fetch all users from database
        $users = $this->user->findAll();

       // show them on screen
        print_r($users);          
    }
} 
```

这里，我们再次通过构造函数传递`UserController`的依赖项(`User`类)，使它变得更好。一切都好吗？不见得...

## 我们还有一个问题

`User`类仍然存在的一个问题是，它被绑定到**特定类型的数据库** ( `Mysql`)。如果我们想使用 Sqlite 或 SQL Server 等其他数据库会怎么样？这就是**依赖倒置原则**发挥作用的地方。

为了克服这个问题，我们需要创建一个**接口**(抽象或契约——参见上面 DIP 的定义),这将允许我们使用带有`User`类的*任何数据库*。这是我们所有数据库都会实现的接口:

```
interface DB
{
    public function connect($dsn, $user = '', $pass = '');
    public function query($query);
} 
```

下面是正在实现上述接口的`Mysql`类:

```
class Mysql implements DB
{
    protected $db = null;

    public function connect($dsn, $user = '', $pass = '')
    {
        $this->db = new PDO($dsn, $user, $pass);
    }

    public function query($query)
    {
        return $this->db->query($query);
    }
} 
```

实现上述接口的另一个数据库类:

```
class Sqlite implements DB
{
    protected $db = null;

    public function connect($dsn, $user = '', $pass = '')
    {
        $this->db = new PDO($dsn);
    }

    public function query($query)
    {
        return $this->db->query($query);
    }
} 
```

如你所见，`Mysql`和`Sqlite`都实现了接口强加的方法。最后这里是我们的`User`类:

```
class User
{
    private $database = null;

    public function __construct(DB $database)
    {
        $this->database = $database;
    }

    public function getUsers()
    {
        $users = $this->database->query('SELECT * FROM users ORDER BY id DESC');
        print_r($users);
    }
} 
```

注意，在上面类的构造函数中，我们现在传递的是接口，而不是特定类型的数据库。

现在我们可以为`User`类使用任何数据库，这里是`Mysql`的例子:

```
$database = new Mysql();
$database->connect('mysql:host=localhost;dbname=test', 'root', '');
$user = new User($database);
$user->getUsers(); 
```

`Sqlite`数据库类的例子:

```
$database = new Sqlite();
$database->connect('sqlite:database.sqlite');
$user = new User($database);
$user->getUsers(); 
```

太神奇了！我们现在可以很容易地改变数据库来使用我们的`User`类，我们的`User`类通过遵循依赖倒置原则更加灵活。如果客户需要使用一些其他的数据库，没问题，我们实现一个并以类似的方式指定给`User`类！

如你所见，我们采用了基本和常见的代码示例，并将其转换为可重用、灵活、模块化和可测试的代码。

你可能想多读一些关于 S.O.L.I.D 原则的文章，来写一些令人愉快的代码，并且避免 T2 代码。

[![](img/fb5b35332b98360e7522cb3b25ec15da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_-PhSxVB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/R-HEJrk66Ko)