# 使用 Laravel 5.0 创建 Web 应用程序

> 原文：<https://dev.to/connor11528/create-a-web-app-with-laravel-50>

源代码:[https://github.com/connor11528/laravel-5-fundamentals](https://github.com/connor11528/laravel-5-fundamentals)
推特:[https://twitter.com/Connor11528](https://twitter.com/Connor11528)

如果你喜欢这篇文章，给回购一个[星](https://github.com/connor11528/laravel-5-fundamentals)或者文章一个[股](https://twitter.com/Connor11528)！

### [T1】简介](#intro)

我开始在前端使用 Vue.js。在看了令人敬畏的 Vue.js Laracasts 系列后，我决定用 PHP 和 Laravel 进行全栈开发。Laravel 5 Fundamentals 系列非常容易理解，并带领我们在这个 PHP 框架中进行应用程序开发。这篇文章涵盖了课程内容的前半部分。

> 如果您在安装 Laravel 或其依赖项时遇到问题，[在这里创建 github 问题](https://github.com/connor11528/laravel-5-fundamentals/issues)，我们将尽力帮助您！

### 生成申请

我们假设你已经安装了 PHP >= 5.4，MySQL，Composer 和一些扩展( [install docs](https://laravel.com/docs/5.0) )。我们使用的是 2015 年初发布的 5.0 版本。该框架有一个固定的发布时间表，目前版本为 5.3。5.3 的功能简直不可思议。在本帖中，我们将介绍使用 5.0 的 Laravel 基础知识。

```
$ composer create-project laravel/laravel laravel-5-fundamentals "5.0.*" --prefer-dist
$ cd laravel-5-fundamentals
$ php artisan serve 
```

这将在您的浏览器中启动一个 web 服务器。可以在`localhost:8000`查看。

### 创建控制器

Laravel 像其他 web 框架一样遵循 MVC 模式。如果您来自 Rails 或 Django 背景，这应该非常熟悉！

```
$ php artisan make:controller PagesController --plain 
```

这将生成**app/Http/Controllers/pages controller**。控制器与路线相匹配。

列出我们应用程序的所有路线:

```
php artisan route:list 
```

### 刀片语法

Blade 是 Laravel 的前端模板语言。在 HTML 中使用它将变量从服务器传递到标记。在 5.3 版本中，Laravel 自动与 Vue.js 一起发布，并使它很容易与其他 JS web 框架切换——如果你喜欢这种类型的东西的话。

```
@if (count($records) === 1)
    I have one record!
@elseif (count($records) > 1)
    I have multiple records!
@else
    I don't have any records!
@endif

@unless (Auth::check())
    You are not signed in.
@endunless

@for ($i = 0; $i < 10; $i++)
    <li>The current value is {{ $i }}</li>
@endfor

@foreach ($users as $user)
    <p>This is user {{ $user->id }}</p>
@endforeach

@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>No users</p>
@endforelse

@while (true)
    <p>I'm looping forever.</p>
@endwhile 
```

叶片控制结构[文档](https://laravel.com/docs/5.3/blade#control-structures)

### 电子邮件配置

在 **config/mail.php** 中设置`'pretend' => true,`，这样电子邮件就会被写入日志文件，而不是通过网络发送。这样更容易当地发展。

检查**配置**目录中的所有文件。它们非常容易阅读，并且有非常详尽的记录。有趣的事实:文章中的每一行注释都比前一行短三个字符。

```
/*
    |--------------------------------------------------------------------------
    | Mail "Pretend"
    |--------------------------------------------------------------------------
    |
    | When this option is enabled, e-mail will not actually be sent over the
    | web and will instead be written to your application's logs files so
    | you may inspect the message. This is great for local development.
    |
    */ 
```

### 迁徙

###### 在生产中更改数据库模式

在生产中，我们不能简单地回滚数据库来更改模式。

相反，创建一个新的迁移

```
$ php artisan make:migration add_excerpt_to_articles_table --table="articles" 
```

为了删除一个表，需要 doctrine/dbal 包。用 composer: `composer require doctrine/dbal`安装。

###### 创建数据库表

创建文章表和迁移。

```
$ php artisan make:migration create_articles_table --create="articles" 
```

###### 改变数据库模式

要更改模式，使用 rollback 命令，在**数据库/迁移**中对适当的文件进行更改，然后运行`php artisan migrate`

```
$ php artisan
 migrate:refresh      Reset and re-run all migrations
 migrate:reset        Rollback all database migrations
 migrate:rollback     Rollback the last database migration 
```

###### 查看数据库记录

```
$ php artisan migrate
$ sqlite3 storage/database.sqlite
.tables
.schema
.mode column
.headers on
select * from articles;
delete from articles where id=7; 
```

运行上面的 sqlite 命令后，我们可以使用 SQL 来查询我们的数据库。

### 雄辩

雄辩是 Laravel 的 ActiveRecord 实现。这意味着 ORM 包装器允许您编写生成 SQL 语句的 PHP 代码。我们定义雄辩的模型来模拟我们的数据库。[雄辩的医生](https://laravel.com/docs/5.3/eloquent)

```
$ php artisan make:model Article
$ php artisan tinker 
```

### 异常处理

为了捕捉 404 页面错误，我们可以修改**app/Exceptions/handler . PHP**。错误页面模板存储在**resources/views/errors/404 . blade . PHP**中。

如果我们有一个视图文件可以在“错误。{errorStatusCode}”，它将自动显示该状态代码( [5.0 自定义错误页面](https://mattstauffer.co/blog/laravel-5.0-custom-error-pages))。

图像存储在**中。/public** 文件夹。

### 表格生成器为少码

表单生成器和 HTML 生成器从核心框架中移除。

它们可以通过以下方式安装:

```
$ composer require illuminate/html 
```

illuminate/html[github repo](https://github.com/illuminate/html)。

注意，以上是针对 Laravel 5.0 的。如果使用 Laravel >=5.3，则使用`composer require "laravelcollective/html":"^5.3.0"`。更多信息在[拉勒维尔集体](https://laravelcollective.com/)网站上。

### 表单请求(验证)

```
php artisan make:request CreateArticleRequest 
```

在 **app/Http/Requests/** 中生成文件。

[验证文件](https://laravel.com/docs/5.0/validation)

在 html 视图中查看表单错误:`<pre>{{ var_dump($errors) }}</pre>`

### 结论

这大概涵盖了 Laracasts 的 Laravel 5 基础课程的前半部分。目前为止强烈推荐！在下半部分，我们将讨论 Laravel 5.0 的雄辩的数据库关系、认证和更多高级特性！

源代码:[https://github.com/connor11528/laravel-5-fundamentals](https://github.com/connor11528/laravel-5-fundamentals)
推特:[https://twitter.com/Connor11528](https://twitter.com/Connor11528)

如果你喜欢这篇文章，给回购一个[星](https://github.com/connor11528/laravel-5-fundamentals)或者文章一个[股](https://twitter.com/Connor11528)！