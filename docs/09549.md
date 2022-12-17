# 在 Laravel 和雄辩中使用 Postgre 的 UUIDs

> 原文：<https://dev.to/ariera/using-postgres-uuids-in-laravel-and-eloquent>

我喜欢 postgres 的一点是它能够使用**uuid 作为主键**。[小汤姆·哈里森最近写了一篇值得一读的关于这种方法利弊的总结](https://tomharrisonjr.com/uuid-or-guid-as-primary-keys-be-careful-7b2aa3dcb439#.poi02at77)。对我来说，利大于弊，但这可能是你必须根据自己的项目来决定的事情。

然而，如果你决定使用 Laravel，你会惊讶地发现让 UUIDs 工作并不像官方文档让你想的那么简单。

以下是你必须注意的事情的小备忘单

## 设置数据库

你需要做的第一件事是激活 postgres 中的 UUID 扩展。只需创建一个新的迁移，如下所示:

database/migrations/1900 _ 01 _ 01 _ 000001 _ add _ uuid _ extension _ to _ PostgreSQL . PHP

```
<?php
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;
use Illuminate\Support\Facades\DB;

class AddUuidExtensionToPostgresql extends Migration
{

    public function up()
    {
        DB::statement('CREATE EXTENSION IF NOT EXISTS "uuid-ossp";');
    }

    public function down()
    {
        DB::statement('DROP EXTENSION IF EXISTS "uuid-ossp";');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 迁徙

假设您想要一个`users`表。您可能希望自动生成您的`id`。我发现的大多数教程都依赖于模型的`beforeCreate`回调，在应用层面。我认为最好由数据库来处理这件事。

不幸的是，漂亮的 DSL 没有提供这样的方法，所以我们需要使用普通的 SQL。

database/migrations/2017 _ 03 _ 01 _ 000001 _ create _ users . PHP

```
<?php
Schema::create('users', function (Blueprint $table) {
    $table->uuid('id');
    $table->primary('id');
    // ...
});
DB::statement('ALTER TABLE users ALTER COLUMN id SET DEFAULT uuid_generate_v4();'); 
```

Enter fullscreen mode Exit fullscreen mode

## 型号

现在让我们创建相应的`User`模型。

App/User.php

```
<?php
namespace App;
use Illuminate\Database\Eloquent\Model;
class User extends Model
{
  protected $casts = [
    'id' => 'string'
  ];
  protected $primaryKey = "id";
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有几个警告。首先你需要**确保你的主键被定型为`string`** ，否则它将被定型为`integer`给你各种有趣的错误。

第二:在这个例子中，我命名我的主键`id`，这是雄辩的默认名称。所以在这个特殊的例子中覆盖`$primaryKey`并不是很有趣，但是很多人选择将他们的 PK 命名为`uuid`，所以如果你的情况是这样的话，请注意这一点。

最后，许多在线评论和教程指出公共属性`$incrementing`应该设置为`false`，认为 UUIDs 不需要自动递增。尽管这可能会导致我的应用程序崩溃。**禁用增量属性会使新创建的记录**中的`id`属性无效，如下面的代码所示。

App/User.php

```
<?php
class User extends Model
{
  public $incrementing = false;
  // ...
}

$user = new User;
$user->save();
$user->id; /* => null */ 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

第一次在 Laravel 上使用 UUIDs 作为记录的主键可能有些棘手。希望这个提示能帮助你避免我在:D 遇到的新手陷阱

*本帖最初发表于[ariera . github . io](http://ariera.github.io/2017/03/01/using-postgres-uuids-with-laravel.html)T3】*