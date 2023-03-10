# 关于 PHP 环境变量你需要知道什么

> 原文：<https://dev.to/damienalexandre/what-you-need-to-know-about-environment-variables-with-php-d3c>

用于配置的环境变量是当今应用程序设置的最佳实践——数据库凭证、API 密钥、秘密以及在部署之间变化的一切*现在都通过环境暴露给代码，而不是配置文件，或者更糟，直接硬编码。*

[![You can't leak what you don't store](img/570ff32ae0bde57eba55588ee7459251.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i8_cP-fy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zxvspazedf8zscmax4k6.png)

让我们深入了解:

*   它是如何工作的？
*   这真的是个好主意吗？
*   在 PHP 中如何处理它们？
*   最后是一些建议和要避免的常见错误——我们陷入了一些真实世界的陷阱！

我们不打算讨论如何在 webserver / Docker / crontabs 中设置环境变量...因为它依赖于系统、软件，所以我们想把重点放在环境变量上。

如果你的主机使用的是 [Docker Swarm](https://blog.docker.com/2017/02/docker-secrets-management/) 或 [AWS](https://aws.amazon.com/fr/blogs/mt/the-right-way-to-store-secrets-using-parameter-store/) ，事情会有一点不同，例如，他们决定在你的容器文件系统上推送文件来暴露你的秘密，而不是 env vars:那是非常特定于那些平台的，根本不是一个标准。

## 环境变量 101

当您运行一个程序时，它从其父程序继承所有环境变量。因此，如果您在 bash 中设置一个名为`YOLO`的变量，值为`covfefe`，然后运行一个命令，您将能够在任何子进程中读取`YOLO`。

```
$ YOLO=covfefe php -r 'echo getenv("YOLO");'
covfefe 
```

Enter fullscreen mode Exit fullscreen mode

由于这个变量只在本地定义，我们不能从另一个终端(另一个父节点)读取它。因此，我们的想法是确保您的应用程序总是继承所需的变量。

通过运行以下命令，您可以看到 shell 中的所有环境变量，但是您还看不到`YOLO`变量，因为它只是被动态地传递给了`php`命令，而不是在当前进程中设置:

```
$ env 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用语法`export <NAME>=<VALUE>` :
设置环境变量

```
$ export YOLO=covfefe 
```

Enter fullscreen mode Exit fullscreen mode

变量名区分大小写，惯例是只使用英文大写名称，用 _ 作为分隔符(大写字母)。你已经知道一些像`PATH`、`DISPLAY`、`HTTP_PROXY`、...

### 今日最佳做法

您可能已经知道了构建健壮且可伸缩的应用程序的[十二要素方法学](https://www.12factor.net/)(如果没有，我建议您休息一下，去看看)。“配置”一章解释了为什么将配置存储在环境中是正确的做法:

*   不同部署(生产、试运行、测试)的配置差异很大...)，代码没有；
*   Env 变量很容易在部署之间改变，而不需要改变任何代码；
*   它们是独立于语言和操作系统的标准。相同的配置可以在 PHP 和 Python 进程之间共享。

宣言还很好地描述了代码中应该有什么，环境中应该有什么——不要把你的整个应用程序配置都放在里面，**只放不同部署的不同之处。**

## 我在网上看到 env 病毒很危险

[有些文章](http://movingfast.io/articles/environment-variables-considered-harmful/)会告诉你 env 变量对你的秘密有害；主要原因是任何进程都继承了他的父变量，**所有的**。因此，如果你在环境中有一个非常秘密的设置，子进程将可以访问它:

```
$ export YOLO=covfefe
$ php -r "echo exec('echo $YOLO');"
covfefe 
```

Enter fullscreen mode Exit fullscreen mode

子进程可以将环境变量视为公共的、可写入日志的、包含在错误报告中的、在出错时转储给用户的东西...他们会泄露你的秘密。

另一种方法是普通的旧文本文件，带有强 Unix 权限。但是真正应该做的是**在运行您不信任的子进程**时清除环境，就像 nginx 所做的那样。默认情况下，nginx 删除从其父进程继承的所有环境变量，除了 TZ 变量。问题解决了！

这可以用`env -i`来完成，它告诉在一个空环境中启动下面的命令。

```
$ php -r "echo exec('env -i php -r \'echo getenv(\"YOLO\");\'');"

$ php -r "echo exec('php -r \'echo getenv(\"YOLO\");\'');"
covfefe 
```

Enter fullscreen mode Exit fullscreen mode

**始终在受限环境中运行您不信任的进程**。

即使你信任你的代码，你也应该非常小心，将你的变量暴露在尽可能少的进程中——你永远不知道会发生什么。

## 准备好你的 PHP 应用程序

当处理 PHP 项目中的 env 变量时，您希望确保您的代码总是从可靠的来源获取变量，无论是`$_ENV`、`$_SERVER`、`getenv`...但是这三种方法返回的结果不一样！

```
$ php -r "echo getenv('HOME');"
/home/foobar

$ php -r 'echo $_ENV["HOME"];'
PHP Notice: Undefined index: HOME

$ php -r 'echo $_SERVER["HOME"];'
/home/foobar 
```

Enter fullscreen mode Exit fullscreen mode

这是因为我机器上的[`variables_order`PHP 设置](http://us.php.net/manual/en/ini.core.php#ini.variables-order)`GPCS`，因为没有 E 我不能依赖`$_ENV`超级全局。这可能导致代码在一个 PHP 安装上工作，而在另一个上不工作。

还有一点就是开发者不想在本地管理 env 变量。我们不想一直编辑虚拟主机，重新加载 php-fpm，重启一些服务，清除缓存...开发人员想要一种简单而轻松的方式来设置环境变量...像个`.env`档！

一个`.env`文件只是一个环境变量及其值的汇编:

```
DATABASE_USER=donald
DATABASE_PASSWORD=covfefe 
```

Enter fullscreen mode Exit fullscreen mode

### 打点婀库来救驾

#### [vlucas/phpdotenv](https://packagist.org/packages/vlucas/phpdotenv) ，目前最流行的库

这个库将读取一个`.env`文件并填充所有的超级全局变量:

```
$dotenv = new Dotenv\Dotenv( __DIR__ );
$dotenv->load();

$s3Bucket = getenv('S3_BUCKET');
$s3Bucket = $_ENV['S3_BUCKET'];
$s3Bucket = $_SERVER['S3_BUCKET']; 
```

Enter fullscreen mode Exit fullscreen mode

有一些很好的附加功能，比如可以根据需要标记一些变量(这是 Laravel 使用的[)。](https://github.com/laravel/framework/blob/5069e7a36e7f901234ff68f9748e55b6db9922b3/composer.json#L40)

#### [何塞·冈萨雷斯/多腾夫](https://packagist.org/packages/josegonzalez/dotenv)，面向安全

默认情况下，这个库不会填充超级全局变量:

```
$Loader = new josegonzalez\Dotenv\Loader('path/to/.env');
// Parse the .env file
$Loader->parse();
// Send the parsed .env file to the $_ENV variable
$Loader->toEnv(); 
```

Enter fullscreen mode Exit fullscreen mode

它支持必需的键、过滤，并且可以在变量被覆盖时抛出异常。

#### [symfony/dotenv](https://packagist.org/packages/symfony/dotenv) ，街区新小子

从 Symfony 3.3 开始可用，这个组件像其他组件一样处理`.env`文件，并填充超级全局变量:

```
$dotenv = new Symfony\Component\Dotenv\Dotenv();
$dotenv->load( __DIR__.'/.env');

$dbUser = getenv('DB_USER');
$dbUser = $_ENV['DB_USER'];
$dbUser = $_SERVER['DB_USER']; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

关于 [packagist](https://packagist.org/?q=dotenv&idx=packagist&p=0) 还有更多内容，在这一点上，我不敢问为什么每个人都在重新编写同一个解析器。

但是它们都使用相同的逻辑:

*   找到一个`.env`文件；
*   解析它，检查嵌套值，提取所有变量；
*   仅为尚未设置的变量填充所有超全局变量。

我建议**提交一个`.env`文件，其中包含为开发人员**准备的值:每个人都应该能够签出你的项目，并以他们喜欢的方式运行它(命令行服务器、Apache、nginx...)不处理配置。

```
(new Dotenv())->load( __DIR__.'/.env'); 
```

Enter fullscreen mode Exit fullscreen mode

当每个人在本地都有相同的基础设施时，这个建议很有效:相同的数据库密码，相同的服务器端口…当我们在所有项目中使用 Docker Compose 时，我们从不会对不同的开发人员有任何区别，如果您没有这个特权，就允许开发人员通过导入两个文件来覆盖默认设置:

```
(new Dotenv())->load( __DIR__.'/.env', __DIR__.'/.env.dev'); 
```

Enter fullscreen mode Exit fullscreen mode

这样，您只需创建一个`.env.dev`文件，并填充对您来说不同的内容(并将其添加到`.gitignore`)。

那么在生产中，你**不应该加载那些默认值**，所以这个想法是**用一个只在生产中设置的环境变量
来保护加载器**

```
if (!isset($_SERVER['APP_ENV'])) {
    (new Dotenv())->load( __DIR__.'/.env', __DIR__.'/.env.dev');
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你不这样做，你的主机提供商忘记了一个变量，你将在生产中运行开发设置，并有一个糟糕的时间。

## 你必须寻找的陷阱

### 名称冲突

命名是困难的，env 变量也不例外。

因此，在命名您的 env 变量时，您必须具体并尽可能避免名称冲突。由于没有官方的保留名称列表，这取决于你。**前缀自定义变量不能伤害**。

Unix 世界已经这样做了，有`LC_`、`GTK_`、`NODE_`...

### 运行时缺少变量

当变量丢失时，您有两种选择:要么抛出异常，要么使用默认值。这取决于你，但第二个是沉默的...这在很多情况下都会造成伤害。

一旦想要使用 env 变量，就必须在任何地方设置它们:

*   在 web 服务器中；
*   在长期运行的脚本和服务中；
*   在 crontabs...
*   和部署脚本中！

最后一个很容易被忽略，但是由于一些部署可以预热应用程序缓存(像 Symfony 的)...是的，一个**丢失的变量会导致一个损坏的应用交付**。对它们要严格，并在应用程序启动时添加一个需求检查。

### 的`HTTP_`前缀

只有一个前缀你绝对不能用:`HTTP_`。因为这是 PHP 本身(和其他类似 CGI 的上下文)用来存储 HTTP 请求头的。

还记得 [httpoxy](https://httpoxy.org/) 安全漏洞吗？这是由 HTTP 客户端在环境中寻找这个变量引起的，这种方式可以通过简单的 HTTP 头来设置。

一些 DotEnv 库也防止覆盖这些变量，比如 Symfony one 。

### getenv()的线程安全

我有一个坏消息:在某些配置中，使用`getenv`函数会导致意想不到的结果。**这个函数是[非线程安全](https://github.com/laravel/framework/issues/7354)** ！

你不应该用它来检索你的值，所以我建议你调用`$_SERVER`来代替——数组访问和函数调用之间也有一点点性能差异。

### 环境变量总是字符串

PHP 中类型转换的一个主要问题是来自 env 变量的设置并不总是正确的类型。

```
public function connect(string hostname, int port)
{
}

// This will not work properly:
$db->connect($_SERVER['DATABASE_HOSTNAME'], $_SERVER['DATABASE_PORT']); 
```

Enter fullscreen mode Exit fullscreen mode

Symfony 现在允许[转换变量](http://symfony.com/blog/new-in-symfony-3-4-advanced-environment-variables)，更像是读取文件，解码 JSON...

## 到处 Env vars，还是没有

目前在环境变量、文件或它们的混合之间有很多争论:环境变量引用配置文件。事实是，尽管被认为是最佳实践，但 env vars 并没有带来很多优势...

但是，如果使用得当，例如在 Symfony 应用程序中，env 变量可以动态更改，无需清除任何缓存，无需进行任何文件系统访问，无需部署代码:例如，只需重新启动一个进程。

只有一个变量的趋势，比如`APP_CONFIG_PATH`，通过`'%env(json:file:APP_CONFIG_PATH)%'`读取它，对我来说就像是重新发明旧的`parameters.yml`，除非这个文件是由一个可信的工具(比如 AWS Secret Store)自动管理的。还有[envkey.com](https://www.envkey.com/)允许从一个位置控制你的环境变量，而不需要你自己处理文件，我喜欢这种方法，因为它更接近 Heroku 式托管的简单性！

您使用什么向应用程序公开您的凭据？关于环境变量，你有什么专业建议要分享吗？请评论！