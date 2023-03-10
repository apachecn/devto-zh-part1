# 使用 Laravel 5.4 验证 0，包括保存用户

> 原文：<https://dev.to/ophasnoname/auth0-with-laravel-54-including-saving-the-user>

声明:对不起，我的英语，不是我的母语..但是我尽力了。

在我设法从 Wacken 2017 中恢复过来后的假期，剩下的时间再次投入到 PHP 和 Laravel 的一些乐趣中。

对于一个需要用户管理的小项目，最近一位同事向我推荐了 auth0 服务。

什么是 [auth0](https://auth0.com) ？你需要尽一切努力来包含各种认证提供者，比如脸书、谷歌、Github 等等。在免费版本中有些限制，但是对于一个开始来说绰绰有余(7000 个用户，2 个提供商)。

现在是时候将我的 Laravel 应用程序连接到 auth0 了。文档非常好(也与 Laravel 中的集成有关)，但是当我试图将用户保存到我自己的数据库中时，示例不起作用。所以如果你必须完成同样的任务，或者我必须再做一次..下面来一个小教程。

作为起点，让我们假设:

*   Laravel 5.4 应用程序工作正常，用户(已经在那里)的迁移已经执行
*   Auth0 帐户已设置

大多数时候，我们会遵循他们的[文档](https://auth0.com/docs/quickstart/webapp/laravel)，做一些小的改变。

## 在 Laravel 5.4 中集成 Auth0

在您的项目目录中，我们首先为 Auth0
安装正确的包

```
composer require auth0/login:"~5.0" 
```

Enter fullscreen mode Exit fullscreen mode

现在更改您的:/config/app.php

```
<?php
'providers' => array(
    Auth0\Login\LoginServiceProvider::class,
);
'aliases'array( 
    'Auth0' => Auth0\Login\Facade\Auth0::class 
); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们不同于文档，因为我们想在我们的数据库中保存我们的用户。为此，我们构建了一个 user Repository:/app/Repository/user Repository . PHP，内容如下:

```
<?php
namespace App\Repository;
use Auth0\Login\Contract\Auth0UserRepository as Auth0UserRepository;
use App\User as User;
class UserRepository implements Auth0UserRepository {
    public function getUserByDecodedJWT($jwt)
    {
        $jwt->user_id = $jwt->sub;
        return $this->upsertUser($jwt);
    }
    public function getUserByUserInfo($userInfo)
    {
        return $this->upsertUser((object) $userInfo['profile']);
    }
    /**
     * Check if user is in database, if not create.
     * 
     * @return User 
     */
    protected function upsertUser($profile) {
        $user = User::where("auth0id", $profile->user_id)->first();
        // create user if not in database
        if ($user === null) {
            $user = new User();
            $user->email = $profile->email;
            $user->auth0id = $profile->user_id;
            $user->name = $profile->name;
            // random password, we dont need it 
            $user->password = md5(time());
            $user->save();
        }
        return $user;
    }
    public function getUserByIdentifier($identifier)
    {
        //Get the user info of the user logged in (probably in session)
        $user = \App::make('auth0')->getUser();
        if ($user === null) return null;
        // build the user
        $user = $this->getUserByUserInfo($user);
        // it is not the same user as logged in, it is not valid
        if ($user && $user->id == $identifier) {
            return $user;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们不设置密码，登录通过 auth0 管理。 ***你还需要做的就是在用户表*** 中插入列 auth0id，当然这是 Laravel 之前不知道的。该 ID 用于分配。

该存储库现在将注册到我们的 AppServiceProvider:/app/Providers/AppServiceProvider . PHP

```
<?php
   /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        $this->app->bind(
          Auth0UserRepositoryContract::class,
          UserRepository::class // das unser Repository - in der Doku wird Auth0Repo.. genutzt
        );
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在到配置了。为此，我们创建必要的配置文件，并为服务设置必要的路由。

```
php artisan vendor:publish 
```

Enter fullscreen mode Exit fullscreen mode

File: /routes/web.php

```
Route::get('/login', ['as' => 'login', 'uses' => 'IndexController@login']);
Route::get('/logout', ['as' => 'logout', 'uses' => 'IndexController@logout']);
Route::get('/callback', '\Auth0\Login\Auth0Controller@callback'); 
```

Enter fullscreen mode Exit fullscreen mode

IndexController 中的功能如下:

```
<?php

    public function index() 
    {
        $isLoggedIn = \Auth::check();

        return view('index')
              ->with('isLoggedIn', $isLoggedIn)
              ->with('user', \Auth::user());
    }

    public function login()
    {
        return \App::make('auth0')->login();
    }

    public function logout()
    {
        \Auth::logout();
        return \Redirect::intended('/');
    } 
```

Enter fullscreen mode Exit fullscreen mode

所以已经很清楚了，你后来是怎么访问用户数据的:***\ Auth::user()；*** 现在我们需要包含 auth0 作为 user provider:/app/config/auth . PHP

```
 'providers' => [
        'users' => [
            'driver' => 'auth0',
        ], 
```

Enter fullscreen mode Exit fullscreen mode

## 为应用程序设置

为了获得我们配置的必要参数，我们访问 Auth0 的网站。您必须在那里创建一个基本的 web 应用程序，并设置以下 URL。

-回调:[http://dein-server.de/callback](http://dein-server.de/callback)
-注销:[http://dein-server.de/logout](http://dein-server.de/logout)

如果你还没有一个域名的话，整体也应该和“本地主机”一起工作。

对于应用程序的配置，我们仍然需要:

-域
-客户端 id
-客户端密码

在您的/app/config/laravel-auth0.php 中，您现在要么直接设置这些值，要么使用您的"。环境文件。

## 测试验证 0

如果您现在调用[http://dein-server.de/login](http://dein-server.de/login)，您应该看到一个登录，然后在您的用户对象中看到相应的数据以及用户数据库表中的一个条目。

文档中缺少了什么:我如何实际保护一条路线？只需将身份验证中间件添加到您的路线中。

```
# monitor (protected)
Route::get('/monitor/id/{id?}/{title?}', 'MonitorController@show')
    ->middleware('auth')
    ->name('monitor.show');

Route::get('/monitor/create', 'MonitorController@create')
    ->middleware('auth')
    ->name('monitor.create'); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！—如果你喜欢这个的德语版本，请访问我的 [GeekPub](https://www.geekpub.de/2017/08/auth0-mit-laravel-5-4-inklusive-speichern-der-user/)