# 我如何开发 web 应用程序以及我们在工作中做些什么

> 原文：<https://dev.to/littlefox/how-i-do-web-applications-and-what-we-do-at-work>

自从在 [#ourstack](////dev.to/t/ourstack) 中的第一篇文章以来，我一直想写一些我喜欢的 web 应用程序开发组件。这是我的第一篇文章，我将命名组件，显示替代方案，并解释为什么我发现它们优于任何替代方案；)

我还将谈论工作中使用的不同组件以及我们为什么选择它们(如果不仅仅是 legacy)。

**免责声明:**我不会告诉你我在哪里工作，但它可能是可谷歌的。工作中有更好的软件项目，但我将只讲述我大部分时间都必须使用的那个。

# 前端和后端/刀架之间的切割

## 好

我真的很喜欢前端和后端之间的良好，平滑的切割。如果您开始一个新的 web 应用程序，从 API 描述开始。尽可能完整地描述。使用类似于 [OpenAPI](https://openapis.org) 的东西进行描述，因为这是可重用的、机器可读的和人类可读的。您甚至可以将该描述作为 API 的完整文档来重用，使其可供第三方使用只是一个配置问题，如果有的话。

当一切都在 API 上运行时，很容易为你的应用程序制作一个应用程序，连接其他服务和编写脚本。测试前端和后端也可能简单得多(用静态数据启动测试服务器，或者针对开发后端运行脚本来测试它)。

此外，使用 REST API，您只能在服务器和最终用户之间传输数据。应用程序只被加载一次，并且(希望)已经被缓存。这对于任何可能在慢速或昂贵的互联网(移动网络，超出数据量，...meh)。

## 不好/我们的工作方式

不剪。我们的服务器代码在页面内容函数调用之前有数据库操作。大概是这样:

```
my $rs = $c->dbi('Table')->search({foo => 'bar' });
$page->add_js('$("#foo").hide();'); 
```

Enter fullscreen mode Exit fullscreen mode

你真的不想这么做。我们这样做是因为这个项目开始的时候，工作中没有人知道得更清楚(几年前)。但是，选择了这种架构，以后真的很难改变。此外，我们的许多客户都是工业客户——如果他们使用 Internet Explorer 7，我们会很高兴。

# 前端框架

## 棱角分明 4

我喜欢用 Angular 4 编写客户端应用程序。不是 JavaScript(好东西)。它确实默认使用 Webpack(另一件好事)。它有很棒的工具(又一个好东西(我现在要停止它))。它也被 Google 使用，有很好的文档和社区。

我不喜欢 Angular 的唯一一点是部署时相对较高的代码量。

## 老办法/我们在工作中做什么

我们工作的前端是一大堆 javascript 文件(命名为“global.js”和其他类似的好名字),结合了 jQuery、内联 css，甚至内联 javascript。用这种方式工作真的不好，但它确实有效。

我们的 HTML 是在服务器上生成的，然后交付给客户端。没有 javascript 什么都不能工作，但是一切都可以工作到 IE8，有时甚至是 IE6。

### 另类

这有一些好处，但是我强烈建议不要这样做。如果你不能使用一个漂亮的客户端 web 应用程序框架，也许做另一个调用 REST API 的服务器端应用程序是一个选择？你可以改变前端没有进一步的问题，你甚至可以同时拥有两者。

# 服务器端/后端

## Perl，AnyEvent，Dancer2，DBIx::Class

### Perl，AnyEvent

我非常喜欢 perl 编程语言(专为挑剔者设计的 perl 5)。它有许多很棒的模块可以安装。它足够灵活，不会妨碍异步编程，并且易于编写。有了一些实践和编码标准，它也很容易阅读，即使其他人告诉否则^^

异步编程的一个很好的模块是 AnyEvent。它做了所有烦人的事情，而且工作正常。您可以使用它进行文件操作、后台处理和其他工作，并且只需一名员工即可轻松地同时为多个客户提供服务。还有一个现成的用于 AnyEvent 的 web 服务器:twiggy。最好的:崔姬与 Dancer2 的异步路由支持配合得很好。

### 舞者 2

perl 中最酷的 web 应用程序模块之一是 Dancer2 框架。最基本的例子是这样的:

```
# lib/Dev/To/Dancer2/Example.pm
package Dev::To::Dancer2::Example;

use Dancer2;

get '/' => sub {
    return 'Hello dev.to!';
}
1; 
```

Enter fullscreen mode Exit fullscreen mode

```
# app.psgi
use Dev::To::Dancer2::Example;
Dev::To::Dancer2::Example->to_app; 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的 hello world 应用程序，在/ url 返回“Hello dev.to”。

### 替代品

Dancer2 的一个流行替代品似乎是 Mojolicious，它似乎也是 Catalyst 的继任者。Mojo 看起来更完整，但也像一堆孤立的组件，所有的东西都被重写以匹配框架哲学或其他东西。在 that^^，我可能是错的

还有，在做 REST-API 的时候，你也可以看看 Raisin 或者 PerlSwagger(还没上 CPAN，会重写，是我写的)。两者都是针对纯 api 的 web 应用程序的框架。

### DBIx::Class

大多数 web 应用程序都有某种数据库。我更喜欢关系数据库系统，尤其是 PostgreSQL。因为手工编写 SQL 查询既烦人又低效——如果操作不当——还是一场安全噩梦，所以我总是在应用程序中使用 DBIx::Class。

DBIx::Class 是一个对象关系映射器，也就是说，它为所有的表提供了类，并且可以为每个数据库查询获取对象——无论是结果集(即多个结果、整个表还是没有结果)还是结果(表中的一行)。它还可以处理你的关系，并给你你的专辑的艺术家-如果你有正确的外键。

我也喜欢用代码定义我的模式，也喜欢把数据库的版本控制交给代码。我最喜欢的是 DBIx::Class::Schema::Versioned::Inline。

## 我们在工作中做什么

我们工作中的堆栈有点过时，但不像我们的前端那么糟糕。

我们使用 Catalyst 作为 web 应用程序的基础。我们有一些自定义插件扔进它，做一些 hacky 的东西，有一些全球性的东西(如催化剂对象，我们用来访问我们的数据库和东西)。

对于我们的数据库，我们几乎只使用 DBIx::Class。有一些手工编写的查询，主要是出于性能原因。我们的模式是手动版本化的，并在数据库中更新。我们从参考数据库中生成 DBIx::Class 类。

我们遇到的最糟糕的攻击是运行时打补丁的 DBIx::类，它支持我们的非外键(我们有外键，但数据库不知道它们)和我们的虚拟文件系统，从交付给客户服务器的单个文件加载所有包。

遗憾的是，我们甚至很少使用模板。我们的大多数页面逻辑都在它所属的控制器中，但是页面看起来应该是什么样子也在那里...我们有许多内嵌的 javascript 和 css，这使得我们生成的 HTML 膨胀了——主要是因为有相同的代码和另一个生成的 ID。

不过，我们有一个非常好的表单生成器。我们的大多数页面只是在一个数据库条目中存储东西，看起来像这样:

```
package pages::test::simple;

use Moose;
extends 'core::GUI::Page::Detail';

has '+default_form_groups' => (
    default => sub {
        return [
            {
                label    => 'Element details',
                elements => [
                    {
                        # generate form elements automatically for the database columns
                        columns => [qw(name description starttime endtime)],
                    },
                    {
                        # or set things manually and have more control
                        type       => 'text',
                        readonly   => 1,
                        label      => 'Name of related entry',
                        column     => 'fk_other/name',
                    }
                ],
            },
        ];
    },
);
1; 
```

Enter fullscreen mode Exit fullscreen mode

我真的很喜欢我们的表格生成器，它只是讨厌它只能与数据库一起工作。如果你做的事情没有存储在数据库中，你必须自己做很多。这通常意味着创建表单元素，告诉它们它们没有被保存，添加 id，并向服务器端函数发出 AJAX 请求...它可以工作，但不是很好。

# 结论/结束

当编写一个新的 web 应用程序时，试着在你的业务逻辑、你的页面逻辑和页面定义它们外观的方式之间划一条线。加强这一点的最好方法之一是在两个不同的应用程序之间使用 REST API:前端和后端。这对我来说是最重要的规则，我也试图在工作中传播它(因为客户的需求:这实际上可能是成功的)。

我希望你学到了新的东西，我没有写错或无聊的东西。如果你有问题或者我在这里犯了一些错误，请评论。

感谢您的阅读！