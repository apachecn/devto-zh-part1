# 如何在 Laravel 会话表中存储用户 id

> 原文：<https://dev.to/stayallive/how-to-store-user-id-in-the-laravel-session-table-2fm3>

有时，您希望将用户 id 与会话一起存储，以便可以清除所有会话甚至单个会话，从而为您的用户创建更安全的系统，或者您可能希望查看用户登录的次数。有许多用例，但默认情况下是不可能的...所以让我们实现这个漂亮的特性:)

> 注意:在 Laravel **5.2** 中不需要，因为这是开箱即用的

*注意:本指南仅适用于`database`会话驱动，其他驱动不支持。*

先修改迁移:

```
<?php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateSessionTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create(config('session.table'), function (Blueprint $table) {
            $table->string('id')->unique();

            $table->integer('user_id')->unsigned()->nullable()->default(null);
            // You can even add a foreign key constraint if you want :)
            //$table->foreign('user_id')->references('id')->on('users')->onUpdate('CASCADE')->onDelete('CASCADE');
            $table->text('payload');

            $table->integer('last_activity');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists(config('session.table'));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在默认字段中添加了一个表示用户的无符号整数字段，我们允许 null，因为没有登录的用户没有用户 id。运行`php artisan migrate`运行迁移。

接下来，我们需要添加自定义驱动程序，因为我们需要覆盖 Laravel 提供的数据库会话驱动程序的一个方法。

```
<?php

namespace App\Session;

class DatabaseSessionHandler extends \Illuminate\Session\DatabaseSessionHandler
{
    /**
     * {@inheritDoc}
     */
    public function write($sessionId, $data)
    {
        $user_id = (auth()->check()) ? auth()->user()->id : null;

        if ($this->exists) {
            $this->getQuery()->where('id', $sessionId)->update([
                'payload' => base64_encode($data), 'last_activity' => time(), 'user_id' => $user_id,
            ]);
        } else {
            $this->getQuery()->insert([
                'id' => $sessionId, 'payload' => base64_encode($data), 'last_activity' => time(), 'user_id' => $user_id,
            ]);
        }

        $this->exists = true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要一个服务提供商向 Laravel 注册我们的新驱动程序，我使用了一个不同的驱动程序名称(`app.database`)，但是如果你愿意，你也可以覆盖默认的数据库驱动程序(称为`database`)。

```
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use App\Session\DatabaseSessionHandler;

class SessionServiceProvider extends ServiceProvider
{
    /**
     * Register the service provider.
     *
     * @return void
     */
    public function register()
    {
        $this->app->session->extend('app.database', function ($app) {
            $lifetime = $this->app->config->get('session.lifetime');
            $table = $this->app->config->get('session.table');
            $connection = $app->app->db->connection($this->app->config->get('session.connection'));

            return new DatabaseSessionHandler($connection, $table, $lifetime, $this->app);
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

将服务提供者添加到您的`config/app.php`中的 providers 数组中，并将您的`config/session.php`配置文件中的驱动程序名称更改为`app.database`。

如果您现在浏览您的应用程序并登录和注销，您将看到会话表相应地添加和删除用户 id。

现在，您可以为 sessions 表添加一个雄辩的模型，并将关系添加到您的用户模型中，并在 sessions 上进行查询:)