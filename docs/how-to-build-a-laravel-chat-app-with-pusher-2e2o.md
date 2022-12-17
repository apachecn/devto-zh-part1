# 如何用 Pusher 搭建一个 Laravel 聊天 app

> 原文：<https://dev.to/mezie/how-to-build-a-laravel-chat-app-with-pusher-2e2o>

Laravel 提供了一个事件广播系统，允许开发人员在服务器端代码和客户端 JavaScript 应用程序之间共享相同的事件名称，从而可以轻松构建具有实时交互的现代应用程序。

另一方面，Pusher 是一个构建可伸缩实时应用程序的简单可靠的平台。Laravel 提供了对 Pusher out of the box 的支持，这使得用 Laravel 和 Pusher 无缝地构建实时应用程序成为可能。事实上，由于 Taylor Otwell、Jeffrey Way、Matt Stauffer 和其他许多人的支持，Pusher 已经成为 Laravel 社区制作实时应用程序的首选工具之一。

在这篇文章中，我将向你展示如何用 Pusher 构建一个 laravel 聊天应用程序。我将使用 Vuejs 作为我的 JavaScript 框架，尽管你可以使用自己选择的 JavaScript 框架，甚至是 jQuery 和普通 JavaScript。

在我们开始之前，让我们快速地看一下我们将构建什么。

[![](img/1eaa4787a2ea293b7aa9d7e0bb251542.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9ZKbb8jS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/02/laravel-pusher-chat-demo.gif)

完整演示的代码可在 [GitHub](https://github.com/ammezie/laravel-chat) 上获得。

## 设置 Laravel

我们将从创建一个新的 Laravel 项目开始。虽然创建新的 Laravel 项目有不同的方法，但我更喜欢使用 Laravel 安装程序。打开你的终端，运行下面的代码:

```
laravel new laravel-chat 
```

Enter fullscreen mode Exit fullscreen mode

这将在您运行上面命令的目录下创建一个`laravel-chat`项目。

在我们开始使用 Laravel 事件广播之前，我们首先需要注册`App\Providers\BroadcastServiceProvider`。打开`config/app.php`并取消注释`providers`数组中的下面一行。

```
// App\Providers\BroadcastServiceProvider 
```

Enter fullscreen mode Exit fullscreen mode

我们需要告诉 Laravel 我们正在使用`.env`文件中的 Pusher 驱动程序:

```
// .env

BROADCAST_DRIVER=pusher 
```

Enter fullscreen mode Exit fullscreen mode

尽管 Laravel 支持 Pusher 开箱即用，但我们仍然需要安装 Pusher PHP SDK。我们可以使用 composer:
来实现

```
composer require pusher/pusher-php-server 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，我们需要在 config/broadcasting.php 中配置我们的 Pusher 应用凭证。

## 设置推动器

如果你还没有，在[https://pusher.com/signup](https://pusher.com/signup)创建一个免费的 Pusher 账户，然后登录你的仪表盘，创建一个应用程序。

现在，让我们填写我们的 Pusher 应用程序凭据。如果你打开`config/broadcasting.php`，你会注意到 Laravel 正在从`.env`文件:
中提取一些推手凭证

```
// Don't add your credentials here!
// config/broadcasting.php

'pusher' => [
  'driver' => 'pusher',
  'key' => env('PUSHER_APP_KEY'),
  'secret' => env('PUSHER_APP_SECRET'),
  'app_id' => env('PUSHER_APP_ID'),
  'options' => [],
], 
```

Enter fullscreen mode Exit fullscreen mode

我们需要在这里稍微修改一下源代码以使其工作。修改源代码，使其看起来像这样:

```
 'pusher' => [
      'driver' => 'pusher',
      'key' => env('PUSHER_APP_KEY'),
      'secret' => env('PUSHER_APP_SECRET'),
      'app_id' => env('PUSHER_APP_ID'),
      'options' => [
          'cluster' => env('PUSHER_CLUSTER'),
          'encrypted' => true,
      ],
  ], 
```

Enter fullscreen mode Exit fullscreen mode

然后让我们更新。包含我们的 Pusher 应用凭证的 env 文件(注意添加的集群凭证，它不会在您的`.env`文件中，因为 Laravel 尚未添加此功能:

```
// .env

PUSHER_APP_ID=xxxxxx
PUSHER_APP_KEY=xxxxxxxxxxxxxxxxxxxx
PUSHER_APP_SECRET=xxxxxxxxxxxxxxxxxxxx
PUSHER_CLUSTER=xx 
```

Enter fullscreen mode Exit fullscreen mode

记得用您的 Pusher 应用凭证替换 **x** s。你可以在**总览**标签的**键**部分找到你的应用凭证。

现在我们已经设置了项目的后端，让我们继续设置前端。Laravel 提供了一些前端框架和库，包括我们将在本教程中使用的 Bootstrap、Vuejs 和 Axios。

我们还将使用 Laravel Mix，它是一个 Webpack 的包装器，可以帮助我们编译 CSS 和 JavaScript。

但是首先，我们需要通过 NPM 安装这些依赖项:

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

为了订阅和收听事件，Laravel 提供了 Laravel Echo，这是一个 JavaScript 库，可以轻松订阅频道和收听 Laravel 广播的事件。我们需要将它和 Pusher JavaScript 库一起安装:

```
npm install --save laravel-echo pusher-js 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装好，我们需要告诉 Laravel Echo 使用推进器。在`resources/assets/js/bootstrap.js`文件的底部，Laravel 删除了 Echo 集成，尽管它被注释掉了。只需取消 Laravel Echo 部分的注释，并使用
更新细节

```
// resources/assets/js/bootstrap.js

import Echo from "laravel-echo"

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: 'xxxxxxxxxxxxxxxxxxxx',
    cluster: 'eu',
    encrypted: true
}); 
```

Enter fullscreen mode Exit fullscreen mode

记得用您的 Pusher 应用程序密钥替换 xs。也使用您之前在`config/broadcasting.php`中指定的相同集群。

现在我们已经完成了 Laravel 和 Pusher 以及其他依赖项的设置，是时候开始构建我们的聊天应用程序了。

## 认证用户

我们的聊天应用程序要求用户登录后才能开始聊天。因此，我们需要一个认证系统，使用 Laravel，它就像在终端中运行 artisan 命令一样简单:

```
php artisan make:auth 
```

Enter fullscreen mode Exit fullscreen mode

这将为身份验证系统创建必要的路由、视图和控制器。

在我们继续创建用户之前，我们需要运行一个新安装的 Laravel 附带的用户迁移。但是要做到这一点，我们首先需要设置我们的数据库。打开`.env`文件并输入您的数据库详细信息:

```
// .env

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel-chat
DB_USERNAME=root
DB_PASSWORD=root 
```

Enter fullscreen mode Exit fullscreen mode

使用您自己的数据库详细信息进行更新。现在，我们可以运行我们的迁移:

```
php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行的 MySQL 版本早于 5.7.7，或者 MariaDB 版本早于 10.2.2，那么 Laravel 5.4 中有一个 bug。更多信息[点击这里](https://github.com/laravel/framework/issues/17508)。这可以通过将`app/Providers/AppServiceProvider.php`的 boot()替换为:
来解决

```
// app/Providers/AppServiceProvider.php

// remember to use
Illuminate\Support\Facades\Schema;

/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
  Schema::defaultStringLength(191);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 消息模型和迁移

通过运行命令:
创建消息模型和迁移文件

```
php artisan make:model Message -m 
```

Enter fullscreen mode Exit fullscreen mode

打开消息模型，并将下面的代码添加到其中:

```
// app/Message.php

/**
 * Fields that are mass assignable
 *
 * @var array
 */
protected $fillable = ['message'];
Within the databases/migrations directory, open the messages table migration that was created when we ran the command above and update the up method with:

Schema::create('messages', function (Blueprint $table) {
  $table->increments('id');
  $table->integer('user_id')->unsigned();
  $table->text('message');
  $table->timestamps();
}); 
```

Enter fullscreen mode Exit fullscreen mode

`message`将有五列:自动增量`id`、`user_id`、`message`、`created_at`和`updated_at`。user_id 列将保存发送消息的用户的 id，而 message 列将保存实际发送的消息。运行迁移:

```
php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

## 用户与消息的关系

我们需要建立用户和消息之间的关系。一个用户可以发送许多消息，而一个特定的消息是由一个用户发送的。因此，用户和消息之间的关系是一对多的关系。要定义这种关系，请将下面的代码添加到用户模型:

```
// app/User.php

/**
 * A user can have many messages
 *
 * @return \Illuminate\Database\Eloquent\Relations\HasMany
 */
public function messages()
{
  return $this->hasMany(Message::class);
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要通过将下面的代码添加到消息模型来定义相反的关系:

```
// app/Message.php

/**
 * A message belong to a user
 *
 * @return \Illuminate\Database\Eloquent\Relations\BelongsTo
 */
public function user()
{
  return $this->belongsTo(User::class);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 定义 App 路线

让我们创建聊天应用程序需要的路线。打开`routes/web.php`，用下面的代码替换路由，定义三条简单的路由:

```
// routes/web.php

Auth::routes();

Route::get('/', 'ChatsController@index');
Route::get('messages', 'ChatsController@fetchMessages');
Route::post('messages', 'ChatsController@sendMessage'); 
```

Enter fullscreen mode Exit fullscreen mode

主页将显示聊天信息和一个输入新信息的输入框。一条`GET messages`路由将获取所有聊天消息，一条`POST messages`路由将用于发送新消息。

**注意:**由于我们已经删除了`/home`路线，您可能想要将`app/Http/Controllers/Auth/LoginController.php`和`app/Http/Controllers/Auth/RegisterController.php`的`redirectTo`属性更新为:

```
protected $redirectTo = '/'; 
```

Enter fullscreen mode Exit fullscreen mode

## 聊天控制器

现在让我们创建一个控制器来处理我们的聊天应用程序的逻辑。使用下面的命令创建一个 chats controller:

```
php artisan make:controller ChatsController 
```

Enter fullscreen mode Exit fullscreen mode

打开新创建的`app/Http/Controllers/ChatsController.php`文件，并向其中添加以下代码:

```
// app/Http/Controllers/ChatsController.php

use App\Message;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

public function __construct()
{
  $this->middleware('auth');
}

/**
 * Show chats
 *
 * @return \Illuminate\Http\Response
 */
public function index()
{
  return view('chat');
}

/**
 * Fetch all messages
 *
 * @return Message
 */
public function fetchMessages()
{
  return Message::with('user')->get();
}

/**
 * Persist message to database
 *
 * @param  Request $request
 * @return Response
 */
public function sendMessage(Request $request)
{
  $user = Auth::user();

  $message = $user->messages()->create([
    'message' => $request->input('message')
  ]);

  return ['status' => 'Message Sent!'];
} 
```

Enter fullscreen mode Exit fullscreen mode

在`ChatsController`的`__contruct()`中使用授权中间件表示控制器的所有方法将只对授权用户开放。然后`index()`将简单地返回一个视图文件，我们将很快创建这个文件。`fetchMessages()`返回用户所有消息的`JSON`。最后，`sendMessage()`将把消息保存到数据库中，并返回一个状态消息。

## 创建聊天应用视图

对于聊天应用程序视图，我们将利用 [Bootsnipp 聊天片段](http://bootsnipp.com/snippets/featured/collapsible-chat-widget)进行一些修改。

创建一个新的`resources/views/chat.blade.php`文件并粘贴到其中:

```
<!-- resources/views/chat.blade.php -->

@extends('layouts.app')

@section('content')

<div class="container">
    <div class="row">
        <div class="col-md-8 col-md-offset-2">
            <div class="panel panel-default">
                <div class="panel-heading">Chats</div>

                <div class="panel-body">
                    <chat-messages :messages="messages"></chat-messages>
                </div>
                <div class="panel-footer">
                    <chat-form
                        v-on:messagesent="addMessage"
                        :user="{{ Auth::user() }}"
                    ></chat-form>
                </div>
            </div>
        </div>
    </div>
</div>
@endsection 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们有一些带有`chat`视图的定制标签，这些是我们很快将创建的 Vue 组件。`chat-messages`组件将显示我们的聊天消息，`chat-form`将提供一个输入框和一个发送消息的按钮。

在我们创建 Vue 组件之前，让我们为`chat`视图添加样式。打开`resources/views/layouts/app.blade.php`(在我们运行 make:auth 时创建的)，在样式链接后添加下面的代码:

```
<!-- resources/views/layouts/app.blade.php -->

<style>
  .chat {
    list-style: none;
    margin: 0;
    padding: 0;
  }

  .chat li {
    margin-bottom: 10px;
    padding-bottom: 5px;
    border-bottom: 1px dotted #B3A9A9;
  }

  .chat li .chat-body p {
    margin: 0;
    color: #777777;
  }

  .panel-body {
    overflow-y: scroll;
    height: 350px;
  }

  ::-webkit-scrollbar-track {
    -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
    background-color: #F5F5F5;
  }

  ::-webkit-scrollbar {
    width: 12px;
    background-color: #F5F5F5;
  }

  ::-webkit-scrollbar-thumb {
    -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3);
    background-color: #555;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

查看`resources/assets/js/bootstrap.js`，您会注意到 Laravel 已经设置了一些开箱即用的前端依赖项(jQuery、Bootstrap、Lodash、Vue、Axios、Echo)。我们可以开始使用 Vue，无需任何进一步的设置。

在`resources/assets/js/components`中创建新的`ChatMessages.vue`文件，并将下面的代码粘贴到其中:

```
// resources/assets/js/components/ChatMessages.vue

<template>
    <ul class="chat">
        <li class="left clearfix" v-for="message in messages">
            <div class="chat-body clearfix">
                <div class="header">
                    <strong class="primary-font">
                        {{ message.user.name }}
                    </strong>
                </div>
                <p>
                    {{ message.message }}
                </p>
            </div>
        </li>
    </ul>
</template>

<script>
  export default {
    props: ['messages']
  };
</script> 
```

Enter fullscreen mode Exit fullscreen mode

该组件接受一组消息作为`props`，遍历它们并显示发送消息的用户名和消息体。

接下来，在`resources/assets/js/components`中创建新的`ChatForm.vue`文件，并将下面的代码粘贴到其中:

```
// resources/assets/js/components/ChatForm.vue

<template>
    <div class="input-group">
        <input id="btn-input" type="text" name="message" class="form-control input-sm" placeholder="Type your message here..." v-model="newMessage" @keyup.enter="sendMessage">

        <span class="input-group-btn">
            <button class="btn btn-primary btn-sm" id="btn-chat" @click="sendMessage">
                Send
            </button>
        </span>
    </div>
</template>

<script>
    export default {
        props: ['user'],

        data() {
            return {
                newMessage: ''
            }
        },

        methods: {
            sendMessage() {
                this.$emit('messagesent', {
                    user: this.user,
                    message: this.newMessage
                });

                this.newMessage = ''
            }
        }    
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

`ChatForm`组件显示一个输入字段和一个发送按钮。它接受经过身份验证的用户作为`props`。它还包含绑定到输入字段的`newMessage`数据。当点击发送按钮或按下输入栏上的回车键时，调用`sendMessage()`。`sendMessage()`简单地触发一个`messagesent`事件，该事件将用户发送的消息传递给根 Vue 实例(它将处理消息的实际发送),并最终清除输入字段。

接下来，我们需要在根`Vue`实例中注册我们的组件。打开`resources/assets/js/app.js`，用下面的代码更新:

```
// resources/assets/js/app.js

require('./bootstrap');

Vue.component('chat-messages', require('./components/ChatMessages.vue'));
Vue.component('chat-form', require('./components/ChatForm.vue'));

const app = new Vue({
    el: '#app',

    data: {
        messages: []
    },

    created() {
        this.fetchMessages();
    },

    methods: {
        fetchMessages() {
            axios.get('/messages').then(response => {
                this.messages = response.data;
            });
        },

        addMessage(message) {
            this.messages.push(message);

            axios.post('/messages', message).then(response => {
              console.log(response.data);
            });
        }
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

一旦创建了 Vue 实例，使用`Axios`，我们向`messages`路由发出一个`GET`请求，并获取所有消息，然后将其传递给将显示在`chat`视图上的消息数组。`addMessage()`接收从`ChatForm`组件发出的消息，将其推送到消息数组，并使用该消息向`messages`路由发出`POST`请求。

## 广播消息发送事件

为了将实时交互添加到我们的聊天应用程序中，我们需要基于一些活动来广播某种事件。在我们的例子中，当用户发送消息时，我们将触发一个`MessageSent`。首先，我们需要创建一个事件，我们称之为`MessageSent` :

```
php artisan make:event MessageSent 
```

Enter fullscreen mode Exit fullscreen mode

这将在`app/Events`目录中创建新的`MessageSent`事件类。这个类必须实现`ShouldBroadcast`接口。该类应该类似于:

```
// app/Events/MessageSent.php

use App\User;
use App\Message;
use Illuminate\Broadcasting\Channel;
use Illuminate\Queue\SerializesModels;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;

class MessageSent implements ShouldBroadcast
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    /**
     * User that sent the message
     *
     * @var User
     */
    public $user;

    /**
     * Message details
     *
     * @var Message
     */
    public $message;

    /**
     * Create a new event instance.
     *
     * @return void
     */
    public function __construct(User $user, Message $message)
    {
        $this->user = $user;
        $this->message = $message;
    }

    /**
     * Get the channels the event should broadcast on.
     *
     * @return Channel|array
     */
    public function broadcastOn()
    {
        return new PrivateChannel('chat');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们定义了两个公共属性，这两个属性将是传递给我们要广播到的频道的数据。

**注意:**这些属性必须是 **public** 才能传递给通道。

由于我们的聊天应用程序是一个只允许认证的应用程序，我们创建了一个名为`Chat`的私人通道，只有通过认证的用户才能连接到该通道。使用`PrivateChannel`类，Laravel 足够聪明，知道我们正在创建一个私有通道，所以不要在通道名前面加上`private-`(由 Pusher 指定)，Laravel 会在引擎盖下面加上`private-`前缀。

接下来，我们需要更新`ChatsController`的`sendMessage()`来广播`MessageSent`事件:

```
// app/Http/Controllers/ChatsController.php

//remember to use
use App\Events\MessageSent;

/**
 * Persist message to database
 *
 * @param  Request $request
 * @return Response
 */
public function sendMessage(Request $request)
{
  $user = Auth::user();

  $message = $user->messages()->create([
    'message' => $request->input('message')
  ]);

  broadcast(new MessageSent($user, $message))->toOthers();

  return ['status' => 'Message Sent!'];
} 
```

Enter fullscreen mode Exit fullscreen mode

由于我们创建了一个私有频道，只有经过认证的用户才能在`chat`频道上收听。因此，我们需要一种方法来授权当前通过身份验证的用户可以实际监听该频道。这可以通过`routes/channels.php`文件中的
来完成

```
// routes/channels.php

Broadcast::channel('chat', function ($user) {
  return Auth::check();
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们将通道名和回调函数传递给`channel()`，该函数将根据当前用户是否通过身份验证返回`true`或`false`。

现在，当发送消息时，`MessageSent`事件将被广播给 Pusher。我们使用的是`toOthers()`,它允许我们从广播的接收者中排除当前用户。

## 监听消息发送事件

一旦广播了`MessageSent`事件，我们需要监听这个事件，这样我们就可以用新发送的消息更新聊天消息。我们可以将下面的代码片段添加到`resources/assets/js/app.js`的`created()`中，紧接在`this.fetchMessages()` :
之后

```
// resources/assets/js/app.js

Echo.private('chat')
  .listen('MessageSent', (e) => {
    this.messages.push({
      message: e.message.message,
      user: e.user
    });
  }); 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 Echo 的`private()`订阅`chat`频道，因为该频道是私有频道。一旦订阅，我们监听`MessageSent`，并基于此，用新发送的消息更新聊天消息数组。

在测试我们的聊天应用程序之前，我们需要使用 Laravel Mix 编译 JavaScript 文件，使用:

```
npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以运行
来启动我们的聊天应用程序

```
php artisan serve 
```

Enter fullscreen mode Exit fullscreen mode

我们的聊天应用程序已经完成，因为我们现在可以实时发送和接收消息。

## 结论

你可以看到用 Laravel 和 Pusher 构建一个实时应用程序是多么简单。有了 Pusher，你不再局限于聊天应用，你可以构建任何需要实时交互的应用。因此，去[创建一个免费的 Pusher 帐户](https://pusher.com/signup)并开始构建伟大的应用程序吧！

这篇文章最初由作者发布在 [Pusher 博客](https://blog.pusher.com/how-to-build-a-laravel-chat-app-with-pusher)上。