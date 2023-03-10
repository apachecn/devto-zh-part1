# 使用 Fly 构建 Laravel 博客应用程序

> 原文：<https://dev.to/m1guelpf/building-a-laravel-blog-app-with-fly-4efl>

*原载于[我的博客](https://miguelpiedrafita.com/fly-laravel/)*

Laravel 提供了一个强大的路由系统，允许开发人员在应用程序中围绕多个域构建逻辑，从而可以轻松构建带有自定义域的现代应用程序。

另一方面，Fly 是一个简单而可靠的应用交付网络，它提供有用的服务，如将不同的源路由到一个域，保护您的应用程序或以安全、简单和快速的方式提供接受自定义域的逻辑。

在这篇文章中，我将向你展示如何用 Fly 构建一个 laravel 博客应用程序。我将使用一个定制的客户端来与 Fly API 交互，但是您也可以使用 Guzzle、cURL 或任何其他您喜欢的 HTTP 客户端。

完整演示的代码在 GitHub 上可以找到[，你可以在这里](https://github.com/m1guelpf/fly-blog-example)探索现场演示[。如果你想玩 PHP Fly API 客户端，你可以在](https://fly-php-blog-example.miguelpiedrafita.com/) [GitHub](https://github.com/m1guelpf/php-fly-api) 上找到它。

## **第一部分:博客应用**

### **设置 Laravel**

我们将从创建一个新的 Laravel 项目开始。虽然创建新的 Laravel 项目有不同的方法，但我更喜欢使用 Laravel 安装程序。打开你的终端，运行下面的代码:

```
laravel new laravel-blog 
```

Enter fullscreen mode Exit fullscreen mode

这将在您运行上面命令的目录下创建一个`laravel-blog`项目。

### **认证用户**

我们的应用程序要求用户登录后才能发帖或添加域名。因此，我们需要一个认证系统，使用 Laravel，它就像在终端中运行 artisan 命令一样简单:

```
php artisan make:auth 
```

Enter fullscreen mode Exit fullscreen mode

这将为认证系统创建必要的路由、视图和控制器。

在我们继续创建用户之前，我们需要运行一个新安装的 Laravel 附带的用户迁移。但是要做到这一点，我们首先需要设置我们的数据库。打开`.env`文件并输入您的数据库详细信息:

```
// .env

DB_CONNECTION=mysql
DB_HOST=[YOUR_DATABASE_HOST]
DB_PORT=3306
DB_DATABASE=[YOUR_DATABASE_NAME]
DB_USERNAME=[YOUR_DATABASE_USER]
DB_PASSWORD=[YOUR_USER_PASSWORD] 
```

Enter fullscreen mode Exit fullscreen mode

在我们运行迁移之前要做的最后一件事是允许用户拥有自定义域。为此，打开`database/migrations`目录中的`users`迁移，并在时间戳前添加以下代码:

```
// database/migrations/2014_10_12_000000_create_users_table.php

$table->string('domain')->nullable(); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以运行我们的迁移:

```
php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

Laravel 5 中有一个 bug，如果你运行的是 5.7.7 之前的 MySQL 版本或者 10.2.2 之前的 MariaDB 版本。更多信息[在这里](https://github.com/laravel/framework/issues/17508)。这可以通过用
替换`AppServiceProvider`的`boot()`方法来解决

```
// app/Providers/AppServiceProvider.php

// add this under the namespace line
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

### **发布模型并迁移**

通过运行命令:
创建一个 Post 模型和迁移文件

```
php artisan make:model Post -m 
```

Enter fullscreen mode Exit fullscreen mode

打开`Post`模型，并将下面的代码添加到其中:

```
// app/Post.php

/**
 * Fields that are mass assignable
 *
 * @var array
 */
protected $guarded = []; 
```

Enter fullscreen mode Exit fullscreen mode

在`databases/migrations`目录中，打开我们运行上面的命令时创建的`posts`表迁移，并用
更新`up()`方法

```
// database/migrations/xxxx_xx_xx_xxxxxx_create_posts_table.php

Schema::create('posts', function (Blueprint $table) {
  $table->increments('id');
  $table->integer('user_id')->unsigned();
  $table->string('title')->default('Untitled');
  $table->text('body');
  $table->timestamps();
}); 
```

Enter fullscreen mode Exit fullscreen mode

帖子将有六列:自动递增的`id`、`user_id`、`title`、`body`、`created_at`和`updated_at`。

`user_id`列将保存发送消息的用户的 ID,`title`列将保存文章的标题,`body`列将保存文章的内容。

运行迁移:

```
php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

### **用户对帖子的关系**

我们需要设置用户和帖子之间的关系。一个用户可以有许多帖子，而一个特定的帖子是由一个用户创建的。所以，用户和消息之间的关系是一种`one to many`关系。

为了定义这种关系，将下面的代码添加到`User`模型:

```
// app/User.php

/**
 * A user can have many posts
 *
 * @return \Illuminate\Database\Eloquent\Relations\HasMany
 */
public function posts()
{
  return $this->hasMany(Post::class);
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要通过将下面的代码添加到`Post`模型:
来定义反向关系

```
// app/Post.php

/**
 * A post belongs to a user
 *
 * @return \Illuminate\Database\Eloquent\Relations\BelongsTo
 */
public function user()
{
  return $this->belongsTo(User::class);
} 
```

Enter fullscreen mode Exit fullscreen mode

### **定义 App 路线**

让我们创建我们的应用程序需要的路线。打开 routes/web.php，用下面的代码替换路由，定义三个简单的路由:

```
// routes/web.php
Route::get('/', 'PostsController@index')->name('index');

Auth::routes();

Route::get('posts/{post}', 'PostsController@show')->name('post');
Route::post('posts', 'PostsController@create')->name('create'); 
```

Enter fullscreen mode Exit fullscreen mode

主页将显示用户的文章和一个用于添加新文章的输入框。一个`GET`帖子路径将显示一个特定的帖子，一个`POST`帖子路径将用于创建新帖子。

**注意**:由于我们已经移除了`/home`路线，您可能想要将`app/Http/Controllers/Auth/LoginController.php`和`app/Http/Controllers/Auth/RegisterController.php`的`redirectTo`属性更新为:

```
protected $redirectTo = '/'; 
```

Enter fullscreen mode Exit fullscreen mode

### **邮政总监**

现在让我们创建一个控制器来处理我们的聊天应用程序的逻辑。用下面的命令创建一个`PostsController`:

```
php artisan make:controller PostsController 
```

Enter fullscreen mode Exit fullscreen mode

打开我们刚刚创建的控制器，向其中添加以下代码:

```
// app/Http/Controllers/PostsController.php

use App\Post;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

public function __construct()
{
  $this->middleware('auth')->only('create');
}

/**
 * Show posts.
 *
 * @return \Illuminate\Http\Response
 */
public function index()
{
  return view('posts', ['posts' => Post::all()]);
}

/**
 * Show a specific post
 *
 * @return \Illuminate\Http\Response
 */
public function show(Post $post)
{
  return view('post')->withPost($post);
}

/**
 * Persist post to database
 *
 * @param Request $request
 * @return \Illuminate\Http\Response
 */
public function create(Request $request)
{

  $post = Auth::user()->posts()->create($request->validate([
    'title' => 'required|string',
    'body' => 'required|string',
  ]));

  return redirect()->route('post', $post);
} 
```

Enter fullscreen mode Exit fullscreen mode

在 ChatsController 的`__construct()`中使用`auth`中间件表示控制器的所有方法将只对授权用户开放。

`index()`方法将简单地返回一个视图文件，我们将很快创建这个文件。

`show()`方法返回一个附加了`post`的视图文件。

最后，`create()`方法将把`post`保存到数据库中，并返回一个到`post`页面的重定向。

### **创建视图**

为了保持简单，我们将使用修改版的 [StartBootstrap 博客模板](https://startbootstrap.com/template-categories/blogs/)。

创建一个新的`resources/views/posts.blade.php`文件并粘贴到其中:

```
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    Posts {{ config('app.name') }}

    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
  </head>

  <body>
    <!-- Page Content -->
    <div class="container">
      <div class="row">
        <!-- Blog Entries Column -->
        <div class="col-md-8">

          <h1 class="my-4">Posts</h1>

          <!-- Blog Post -->
          @foreach($posts as $post)
          <div class="card mb-4">
            <div class="card-body">
              <h2 class="card-title">{{ $post->title }}</h2>
              <p class="card-text">{{ str_limit($post->body, 200) }}</p>
              <a href="{{ route('post', $post) }}" class="btn btn-primary">Read More &rarr;</a>
            </div>
            <div class="card-footer text-muted">
              Posted {{ $post->created_at->diffForHumans() }}
            </div>
          </div>
          @endforeach
        </div>

        <!-- Sidebar Widgets Column -->
        <div class="col-md-4">
          @auth
          <!-- New Post Widget -->
          <div class="card my-4">
            <h5 class="card-header">New Post</h5>
            <div class="card-body">
              <form method="POST" action="{{ route('create') }}">
              {{ csrf_field() }}
                <div class="form-group">
                  <label for="title">Title:</label>
                  <input type="text" class="form-control" id="title" name="title">
                </div>
                <div class="form-group">
                  <label for="body">Content:</label>
                    <textarea class="form-control" rows="5" id="body" name="body"></textarea>
                </div>
                <button class="btn btn-primary" type="submit">Post</button>
              </form>
            </div>
          </div>
          @else
          <div class="card my-4">
            <p class="text-center"><a href="{{ route('login') }}">Login</a> to make a post</p>
          </div>
          @endauth
        </div>

      </div>
      <!-- /.row -->

    </div>
    <!-- /.container -->

    <!-- Footer -->
    <footer class="py-5 bg-dark">
      <div class="container">
        <p class="m-0 text-center text-white">Copyright &copy; {{ config('app.name') }} {{ date('Y') }}</p>
      </div>
      <!-- /.container -->
    </footer>

    <!-- Bootstrap core JavaScript -->
    <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>

  </body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要一个视图来显示一篇文章，所以让我们创建一个新的`resources/views/post.blade.php`文件，并将以下内容粘贴到其中:

```
<!doctype html>
<html lang="en">

  <head>

    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="author" content="{{ $post->user->name }}">

    {{ $post->title }} - {{ config('app.name') }}

    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
  </head>

  <body>

    <!-- Page Content -->
    <div class="container">

      <div class="row">

        <!-- Post Content Column -->
        <div class="col-lg-12">

          <!-- Title -->
          <h1 class="mt-4">{{ $post->title }}</h1>

          <!-- Author -->
          <p class="lead">
            by {{ $post->user->name }}
          </p>

          <hr>

          <!-- Date/Time -->
          <p>Posted {{ $post->created_at->diffForHumans() }}</p>

          <hr>

          <!-- Post Content -->
          <p>{{ $post->body }}</p>
      </div>
      <!-- /.row -->

    </div>
    <!-- /.container -->

    <!-- Footer -->
    <footer class="py-5 bg-dark">
      <div class="container">
        <p class="m-0 text-center text-white">Copyright &copy; {{ config('app.name') }} {{ date('Y') }}</p>
      </div>
      <!-- /.container -->
    </footer>

    <!-- Bootstrap core JavaScript -->
    <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>

  </body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们有了一个简单的博客平台。让我们添加自定义域。

## **第二部分:自定义域名**

### **设置 Fly.io**

如果您还没有，请在[https://fly.io/app/sign-up](https://fly.io/app/sign-up)创建一个免费飞行帐户，然后登录您的仪表板并创建一个网站。

首先，让我们安装一个帮助我们与 Fly API 交互的包。为此，只需打开您的终端并运行以下代码:

```
composer require m1guelpf/fly-api 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们填写我们的 Fly 应用程序凭据。打开`config/services.php`文件，在右方括号前添加以下内容:

```
// config/services.php

'fly' => [
  'token' => env('FLY_TOKEN'),
  'site' => env('FLY_SITE')
], 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经注意到我们正在从。env 文件，所以让我们更新。包含它的 env 文件:

```
// .env

FLY_TOKEN=[YOUR_FLY_TOKEN]
FLY_SITE=[YOUR_FLY_SITE_SLUG] 
```

Enter fullscreen mode Exit fullscreen mode

如果您不知道从哪里获取令牌，请转到您的 Fly 仪表盘，单击顶部导航栏上的“帐户”按钮，打开“设置”菜单，单击导航栏上的“个人访问令牌”项目，然后创建一个新的帐户。

### **设置路由**

我们需要设置两个新的路由，一个用于用户可以添加自定义域的页面，另一个用于用户访问自定义域时可以看到的页面。为此，我们将首先像这样添加我们的设置路线:

```
// routes/web.php

// the routes we defined before
Route::view('domain', 'domain-setup')->middleware('auth')->name('domain-setup');
Route::post('domain', 'DomainController@create')->middleware('auth'); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将在文件 :
的开头添加一个路由组

```
// routes/web.php

Route::group(['domain' => '{domain}'], function() {
  Route::get('/', 'DomainController@index');
});

// the rest of the routes 
```

Enter fullscreen mode Exit fullscreen mode

此外，为了在不使用自定义域时加载索引页面，我们需要将索引路由移动到我们刚刚定义的路由组 :
之前的路由组

```
// routes/web.php

Route::group(['domain' => '[YOUR_APP_DOMAIN_HERE]'], function() {
  Route::get('/', 'PostsController@index')->name('index');
});

// the route group we defined before

// the rest of the routes, minus the index one 
```

Enter fullscreen mode Exit fullscreen mode

### **创建视图**

我们需要一个用户可以添加自定义域的页面，所以我们将创建一个带有表单的标准引导页面。创建一个`resources/views/domain-setup.blade.php`文件，并将以下内容粘贴到其中:

```
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row">
        <div class="col-md-8 col-md-offset-2">
            <div class="panel panel-default">
                <div class="panel-heading">Custom Domain</div>

                <div class="panel-body">
                    @if (session('status'))
                        <div class="alert alert-success">
                            {!! session('status') !!}
                        </div>
                    @endif
                    @if (count($errors->all()) > 0)
                          <div class="alert alert-danger">
                              {{ $errors->first() }}
                          </div>
                    @endif

                    @if(is_null(Auth::user()->domain))
                        <form class="text-center" method="POST">
                          {{ csrf_field() }}
                          <input class="form-control" name="domain" type="text" placeholder="yourdomain.com" value="{{ old('domain') }}" required>
                          <br>
                          <button type="submit" class="btn btn-primary">Setup Custom Domain</button>
                        </form>
                    @else
                  <p>You have setup <b>{{ Auth::user()->domain }}</b> as your custom domain.</p>
                    @endif
                </div>
            </div>
        </div>
    </div>
</div>
@endsection 
```

Enter fullscreen mode Exit fullscreen mode

### **域控制器**

现在，让我们创建将处理自定义域的控制器。用下面的命令创建一个`DomainController`:

```
php artisan make:controller DomainController 
```

Enter fullscreen mode Exit fullscreen mode

打开我们刚刚创建的控制器，向其中添加以下代码:

```
// app/Http/Controllers/PostsController.php

use App\User;
use Facades\M1guelpf\FlyAPI\Fly;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

/**
 * Render the index page for custom domains.
 *
 * @return \Illuminate\Http\Response
 */
public function index($domain)
{
  $user = User::where('domain', $domain)->findOrFail();

  return view('posts', ['posts' => $user->posts]);
}

/**
 * Persist a custom domain to database
 *
 * @param Request $request
 * @return \Illuminate\Http\Response
 */
public function create(Request $request)
{
  Auth::user()->update($request->validate([
    'domain' => 'required|string|unique:users',
  ]));

  $domain = Fly::connect(config('services.fly.token'))->createHostname(config('services.fly.site'), $request->input('domain'));

  return redirect()->back()->withStatus("Success! To finish the setup, you need to point your domain to <b>{$domain['data']['attributes']['preview_hostname']}</b>. After that, everything's good to go.");
} 
```

Enter fullscreen mode Exit fullscreen mode

## **第三部分:如何改进？**

在这篇文章中，我们创建了一个[博客应用程序](https://fly-php-blog-example.miguelpiedrafita.com/),让用户连接自定义域。嗯，*我*已经创建了应用程序，你只是在阅读它！因此，为了解决这个问题，这里列出了一些你可以改进的地方:

*   允许用户删除自定义域
*   支持多个自定义域
*   改善界面
*   允许私人帖子
*   支持职位降价
*   那件事我错过了但你意识到了并想实现

继续玩 Fly，在一些副业项目中使用它，也许在你下一个很棒的项目中使用它！

*喜欢这篇文章？考虑在《T2》上支持我。[了解更多](https://miguelpiedrafita.com/patreon)。*****