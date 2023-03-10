# 从差到好:直接投资、直接投资和服务

> 原文：<https://dev.to/sarfraznawaz2005/from-bad-to-good-di-dic-and-services-922>

在这篇文章中，我打算触及以下概念:

*   依赖注入([我之前在 DI](http://codeinphp.github.io/post/dependency-injection-in-php/) 上的帖子)
*   依赖注入容器(DIC)
*   服务

尽管这些术语乍一看似乎很吓人，尤其是对新手来说，但它们确实很容易理解，并有助于编写易于理解和扩展的高质量代码。我们将采用这些原则把一个^legacy code^转换成面向对象的。

假设我们有一个简单的应用程序，它向所有订阅者发送时事邮件，下面是遗留代码:

```
// file: index.php or could also be considered as Controller file in MVC paradigm

require __DIR__. 'vendor/autoload.php';

use Demo\Mailer\SmtpMailer;

$dsn = 'sqlite:' . __DIR__. 'database.sqlite';
$pdo = new PDO($dsn);

$mailer = new SmtpMailer('smtp.example.com', 'user', 'password', '465');

$sql = 'SELECT * from subscribers';

foreach ($pdo->query($sql) as $row) {
    $mailer->sendMessage(
        $row['email'],
        'This is the email body',
        $row['name'],
        'info@example.com'
    );
} 
```

现在上面的代码闻起来有这些问题:

*   逻辑混乱，SQL 查询，发送电子邮件数据库配置都在一个文件中

要解决这个问题，第一反应是将其转换成类:

```
class SubscriberMailer
{
    public function sendEmails()
    {
        $dsn = 'sqlite:' . __DIR__. 'database.sqlite';
        $pdo = new PDO($dsn);

        $mailer = new SmtpMailer('smtp.example.com', 'user', 'password', '465');

        $sql = 'SELECT * from subscribers';

        foreach ($pdo->query($sql) as $row) {
            $mailer->sendMessage(
                $row['email'],
                'This is the email body',
                $row['name'],
                'info@example.com'
            );
        }
    }
} 
```

这看起来不错，但它有更多的问题:

*   逻辑是*还是*混在一起，SQL 查询、发送电子邮件数据库配置都在一个文件中
*   它对 PDO 和邮件类有硬编码的依赖(紧耦合)，如果我们想改变邮件类呢？
*   由于紧耦合，很难进行单元测试

让我们试着解决这些问题。我们可以看到这个类依赖于 PDO，所以我们可以通过它的构造函数^pass^它，而不是把它硬编码到`SubscriberMailer`类中，这样就产生了这个类:

```
class SubscriberMailer
{
    protected $pdo = null;

    public function __construct($pdo)
    {
        $this->pdo = $pdo;
    }

    public function sendEmails()
    {
        $mailer = new SmtpMailer('smtp.example.com', 'user', 'password', '465');

        $sql = 'SELECT * from subscribers';

        foreach ($this->pdo->query($sql) as $row) {
            $mailer->sendMessage(
                $row['email'],
                'This is the email body',
                $row['name'],
                'info@example.com'
            );
        }
    }
} 
```

这稍微好一点，这个类不关心 PDO 对象来自哪里，它只需要一个来操作，你不能在没有提供一个的情况下实例化这个类，这样它的意图就很清楚了。所以这就是所谓的**依赖注入**，即使这个术语最初听起来很吓人，也没必要害怕(阅读我的[以前的文章](http://codeinphp.github.io/post/dependency-injection-in-php/)关于注入依赖的更多方法)。下面是我们在这个时候如何使用上面的类:

```
require __DIR__. 'vendor/autoload.php';

use Demo\Mailer\SmtpMailer;
use Demo\Mailer\SubscriberMailer;

$dsn = 'sqlite:' . __DIR__. 'database.sqlite';
$pdo = new PDO($dsn);

$subscriberMailer = new SubscriberMailer($pdo);
$subscriberMailer->sendEmails(); 
```

与第一段代码相比，这看起来更好了。然而，我们仍然有一个问题，因为我们已经将 smtp 配置硬编码到我们的类中，如果我们想在不改变类的情况下更改 smtp 配置，该怎么办呢？或者，如果我们为开发和生产使用不同的 smtp 配置设置会怎么样？记住，一个类应该是一个黑匣子，它只是简单地完成它的工作，而不需要反复编辑，你只能扩展它，不能编辑它。所以这个推理提示我们，像 pdo 一样，我们也应该通过类的构造函数传递 smtp config，并从类本身移动它。

```
class SubscriberMailer
{
    protected $pdo = null;
    protected $smtpConfig = [];

    public function __construct($pdo, array $smtpConfig)
    {
        $this->pdo = $pdo;
        $this->smtpConfig = $smtpConfig;
    }

    public function sendEmails()
    {
        $mailer = new SmtpMailer(
            $this->smtpConfig['host'],
            $this->smtpConfig['user'],
            $this->smtpConfig['password'],
            $this->smtpConfig['port']
        );

        $sql = 'SELECT * from subscribers';

        foreach ($this->pdo->query($sql) as $row) {
            $mailer->sendMessage(
                $row['email'],
                'This is the email body',
                $row['name'],
                'info@example.com'
            );
        }
    }
} 
```

用法:

```
require __DIR__. 'vendor/autoload.php';

use Demo\Mailer\SmtpMailer;
use Demo\Mailer\SubscriberMailer;

$dsn = 'sqlite:' . __DIR__. 'database.sqlite';
$pdo = new PDO($dsn);

$subscriberMailer = new SubscriberMailer($pdo, [
   'host' => 'smtp.example.com',
   'user' => 'user',
   'password' => 'password',
   'port' => '465',
]);

$subscriberMailer->sendEmails(); 
```

这是更好的，我们现在可以在一个地方很容易地改变 smtp 配置，我们不需要接触我们的类。但是它仍然存在这些问题:

*   它与 smtp 邮件程序紧密耦合，如果我们想将邮件程序从 SMTP 更改为另一个类似 sendmail 的邮件程序，该怎么办？
*   SQL 还是我们的类写的，逻辑混了，我们的 mail 类不应该了解用户或者和数据库交互，它只需要发送邮件就可以了！

> `Hint`:`new`关键字被认为是代码味道，无论何时你遇到它，它意味着你应该通过依赖注入来传递。

所以到目前为止，我们知道要去除硬编码的依赖或紧耦合，我们应该通过依赖注入来传递它。因此，我们应该传递邮件程序对象:
，而不是传递邮件程序配置

```
class SubscriberMailer
{
    protected $pdo = null;
    protected $mailer = null;

    public function __construct(\PDO $pdo, SmtpMailer $mailer)
    {
        $this->pdo = $pdo;
        $this->mailer = $mailer;
    }

    public function sendEmails()
    {
        $sql = 'SELECT * from subscribers';

        foreach ($this->pdo->query($sql) as $row) {
            $this->mailer->sendMessage(
                $row['email'],
                'This is the email body',
                $row['name'],
                'info@example.com'
            );
        }
    }
} 
```

用法:

```
require __DIR__. 'vendor/autoload.php';

use Demo\Mailer\SmtpMailer;
use Demo\Mailer\SubscriberMailer;

$dsn = 'sqlite:' . __DIR__. 'database.sqlite';
$pdo = new PDO($dsn);

$mailer = new SmtpMailer('smtp.example.com', 'user', 'password', '465');

$subscriberMailer = new SubscriberMailer($pdo, $mailer);       
$subscriberMailer->sendEmails(); 
```

这样更好，我们移除了对 smtp 邮件程序的硬编码依赖，注意我们还在类的构造函数中对`PDO`和`SmtpMailer`进行了类型提示，这非常有用，因为:

*   我们的类表明它需要一个`PDO`和`SmtpMailer`来正常工作
*   如果有人传递了这两个依赖项之外的东西，他们会得到明确的错误消息
*   类型提示使我们的代码自文档化
*   它使 IDE 自动完成

现在我们的 mailer 类知道它需要一个`SmtpMailer`的实例才能工作，并且它应该有一个`sendMessage`方法。记住，我们重构了我们的代码，希望我们可以使用其他类型的邮件程序，比如 sendmail，但是问题是它不一定有`sendMessage`方法，这意味着我们不能使用它。那么，我们如何解决这个问题，以便能够使用其他类型的邮件实现呢？答案是使用一个接口/契约来代替它:

```
// app/Demo/Contracts/MailerInterface.php
interface MailerInterface
{
   public function sendMessage($email, $body, $subject, $from);
}

// app/Demo/Mailer/SmtpMailer.php
use Demo\Contracts\MailerInterface;

class SmtpMailer implements MailerInterface
{
    public function sendMessage($email, $body, $subject, $from) {
        // code to send email
    }
}

// app/Demo/Mailer/SubscriberMailer.php    
class SubscriberMailer
{
    protected $pdo = null;
    protected $mailer = null;

    public function __construct(\PDO $pdo, MailerInterface $mailer)
    {
        $this->pdo = $pdo;
        $this->mailer = $mailer;
    }

    public function sendEmails()
    {
        $sql = 'SELECT * from subscribers';

        foreach ($this->pdo->query($sql) as $row) {
            $this->mailer->sendMessage(
                $row['email'],
                'This is the email body',
                $row['name'],
                'info@example.com'
            );
        }
    }
} 
```

就像我们正在执行的每一步一样，这一步也有很好的优势:

*   我们的`SubscriberMailer`类现在有了很大的可扩展性，因为它现在接受**任何实现了具有`sendMessage`方法的`MailerInterface`的对象**
*   它更多地记录了我们的代码，现在很清楚应该如何构造一个实现`MailerInterface`的对象

目前为止一切顺利。我们现在看到在我们的类中有 SQL 来获取用户。同样，它不应该关心谈论数据库，它只需要用户的电子邮件发送电子邮件。为了解决这个问题，我们需要使用 **[存储库模式](http://shawnmc.cool/the-repository-pattern)** ，它本质上是一个与数据库对话的类来操作特定的实体，在本例中是用户。因此，我们可以将`UserRepository`作为依赖项传递，它有一个获取所有用户的方法:

```
// app/Demo/Mailer/SubscriberMailer.php
use Demo\Contracts\MailerInterface;
use Demo\Repositories\UserRepository;

class SubscriberMailer
{
    protected $mailer = null;
    protected $userRepository = null;

    public function __construct(MailerInterface $mailer, UserRepository $userRepository)
    {
        $this->mailer = $mailer;
        $this->userRepository = $userRepository;
    }

    public function sendEmails()
    {
        $users = $this->userRepository->getAll();

        foreach ($users as $user) {
            $this->mailer->sendMessage(
                $user->email,
                'This is the email body',
                $user->name,
                'info@example.com'
            );
        }
    }
} 
```

用法:

```
require __DIR__. 'vendor/autoload.php';

use Demo\Mailer\SmtpMailer;
use Demo\Mailer\SubscriberMailer;
use Demo\Repositories\UserRepository;

$mailer = new SmtpMailer('smtp.example.com', 'user', 'password', '465');

$subscriberMailer = new SubscriberMailer($mailer, new UserRepository);    
$subscriberMailer->sendEmails(); 
```

厉害！我们的`SubscriberMailer`类现在没有任何混淆的逻辑，它是一个完整的黑盒，任务是向提供的用户发送电子邮件。它不需要关心你如何通过`MailerInterface`或者`UserRepository`，它只需要他们去操作。它只有一个发送电子邮件的任务。更别说我们班也容易被单元测试。还要注意，我们删除了 PDO 的依赖性，因为现在是`UserRepository`为我们提供用户。

我们现在可以称我们的类为**服务**，它执行一个且只有一个任务，没有任何耦合。如果你听说过 **[面向服务的架构](https://en.wikipedia.org/wiki/Service-oriented_architecture)** ，这就是它，一个服务类的集合，每个服务类只有一个职责，没有任何硬编码的依赖。

目前，我们对自己的类都很满意，但是依赖注入的一个缺点是创建和配置对象的所有复杂性都是你的工作。现在这并不是那么糟糕，因为它发生在一个地方，让你控制，但这是我们仍然可以改善的东西。

为此，我们需要利用所谓的**依赖注入容器** (DIC)。DIC 的工作是为我们创建和配置对象。PHP 有许多 DIC，但让我们看看使用著名的 **[Pimple](http://pimple.sensiolabs.org/)** 容器的修改代码，这里我们将依赖项或服务包装到 Pimple 容器中。我不会详细介绍如何使用它，你可以参考它的文档。

```
// file: container.php

use Demo\Mailer\SmtpMailer;
use Demo\Repositories\UserRepository;
use Pimple\Container;
use Demo\Mailer\SubscriberMailer;

$container = new Pimple();

// container can also hold configs - ideally should be put in separate file...
$container['smtp_config'] = [
   'host' => 'smtp.example.com',
   'user' => 'user',
   'pass' => 'password',
   'port' => '465',
];

$container['mailer'] = $container->share(function(Pimple $container){
   return new SmtpMailer(
      $container['smtp_config']['host'],
      $container['smtp_config']['user'],
      $container['smtp_config']['pass'],
      $container['smtp_config']['port']
   );
});

$container['userRepository'] = $container->share(function(){
   return new UserRepository();
});

$container['subscriberMailer'] = $container->share(function(Pimple $container){
   return new SubscriberMailer($container['mailer'], $container['userRepository']);
});

// file: index.php

require __DIR__. 'vendor/autoload.php';
require __DIR__. 'container.php';

$subscriberMailer = $container['subscriberMailer'];
$subscriberMailer->sendEmails(); 
```

请注意，与初始版本相比，我们的`index.php`代码现在是多么清晰和简洁。

使用 DIC 为我们提供了以下优势:

*   将我们的对象创建包装在容器的匿名函数中使得对象创建变得懒惰，这意味着只有在需要时才创建对象*，如果我们从不引用我们的`SubscriberMailer`类，那些对象根本就不会被创建，节省了我们的内存！*
**   使用容器的`share`方法意味着无论我们需要多少次，它都会给我们相同的原始对象实例。如果我们需要发送许多电子邮件，我们不需要许多实例。这也使得代码速度更快。*   哪些对象依赖于其他对象的所有逻辑都被抽象到容器本身中，我们不需要担心它们是如何创建的，或者它们的依赖关系是什么，我们只需要在容器之外直接使用我们的服务。*   如果我们需要改变配置或什么的，我们只在一个地方编辑它，没有人需要知道这个改变。*

 ** * *

总之，无论您使用哪种框架，这些原则都可以用来编写易于理解、扩展和测试的代码。

[![](img/d129f0b0addca36f85662780a1fff5d5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XmorgYaW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/Xr7FgawiRZg)*