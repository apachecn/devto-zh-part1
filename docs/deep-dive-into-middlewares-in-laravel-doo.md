# 深入了解拉勒维尔的中间件

> 原文：<https://dev.to/franko4don/deep-dive-into-middlewares-in-laravel-doo>

深入了解拉勒维尔的中间件

什么是 Laravel 中间件？

这是 Laravel 中的一个特性，它提供了一种过滤进入应用程序的 HTTP 请求的机制。这允许您挂钩到 Laravel 请求处理工作流，以执行某种决定应用程序如何工作的逻辑。

你会用中间件做什么？

*   保护您的路线
*   设置 HTTP 响应的标头
*   将请求记录到应用程序中
*   消毒输入参数
*   启用全站点维护模式
*   操作应用程序生成的响应

如何创建自定义中间件？

在 Laravel 中创建中间件就像运行下面的命令一样简单

```
php artisan make:middleware <MiddlewareName>
//MiddlewareName should be replaced with actual name of the middleware 
```

Enter fullscreen mode Exit fullscreen mode

这将在 app 中的中间件文件夹中创建一个具有指定名称的中间件。幸运的是，Laravel 搭建了开始定制中间件所需的基础设施。

```
<?php
namespace App\Http\Middleware;
use Closure;
class RedirectIfSuperAdmin
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        return $next($request);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意`handle`函数接受两个参数`$request`和`$next`。`$request`参数保存应用程序中的传入请求 URI，而`$next`参数用于将请求传递到应用程序的更深处。所需的逻辑写在句柄函数中，这就引出了中间件的类型- `before middleware`和`after middleware`。

`Before middleware`顾名思义，在将请求转发到应用程序之前处理一些逻辑。另一方面，`after middleware`在应用程序处理了请求并构建了响应之后运行。

在中间件出现之前:

```
<?php
namespace App\Http\Middleware;
use Closure;
class RedirectIfSuperAdmin
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        //Your logic goes here
        return $next($request);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

中间件之后:

```
<?php
namespace App\Http\Middleware;
use Closure;
class RedirectIfSuperAdmin
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        $response = $next($request);
        //Your logic goes here e.g return redirect('/)

        return $response;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

中间件的类别

*   全球中间件
*   路由中间件

全局中间件为命中应用程序的每个请求运行。Laravel 附带了这些中间件中的大部分，如`ValidatePostSize`、`TrimStrings`、`CheckForMaintenanceMode`等。

路由中间件只在它们所连接的路由上运行，例如`redirectIfAuthenticated`。

注册中间件

任何创建的中间件都必须注册，因为这是 Laravel 知道此类中间件存在的唯一方式。要注册一个中间件，只需打开名为`kernel.php`的文件，它位于 Http 文件夹中，如下所示:

[![kernel.php location on Laravel](img/2dcee9dc1ab60d6fab4852e163ed46fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T7dO9rx7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_154FD755AC9D5ECE9FD43EEB0CC805AC89CA32D2C242B146361CA35EF3505A84_1507618543348_PrtScr%2Bcapture_2.jpg)

这个文件包含默认情况下 Laravel 附带的所有已注册中间件的列表。它包含三个主要数组，分别是`$middleware`、`$middlewareGroups`和`$routeMiddleware`

```
<?php
namespace App\Http;
use Illuminate\Foundation\Http\Kernel as HttpKernel;
class Kernel extends HttpKernel
{
    /**
     * The application's global HTTP middleware stack.
     *
     * These middleware are run during every request to your application.
     *
     * @var array
     */
    protected $middleware = [
        \Illuminate\Foundation\Http\Middleware\CheckForMaintenanceMode::class,
        \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
        \App\Http\Middleware\TrimStrings::class,
        \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
        \App\Http\Middleware\TrustProxies::class,
    ];
    /**
     * The application's route middleware groups.
     *
     * @var array
     */
    protected $middlewareGroups = [
        'web' => [
            \App\Http\Middleware\EncryptCookies::class,
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
            \Illuminate\Session\Middleware\StartSession::class,
            // \Illuminate\Session\Middleware\AuthenticateSession::class,
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,
            \App\Http\Middleware\VerifyCsrfToken::class,
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],
        'api' => [
            'throttle:60,1',
            'bindings',
        ],
    ];
    /**
     * The application's route middleware.
     *
     * These middleware may be assigned to groups or used individually.
     *
     * @var array
     */
    protected $routeMiddleware = [
        'auth' => \Illuminate\Auth\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
        //the just created middlware
        'superadmin' => \App\Http\Middleware\RedirectIfSuperAdmin::class, 
    ];
} 
```

Enter fullscreen mode Exit fullscreen mode

`$middleware`数组包含为应用程序的每个 HTTP 请求运行的全局中间件，所以如果你想让一个中间件为每个请求运行，你应该在这里注册它。`$middlewareGroups`可以在组中注册中间件，通过使用组名可以更容易地将许多中间件连接到一条路线。`$routeMiddleware`数组保存个人注册的中间件。

分配中间件

一旦注册了中间件，就可以通过两种主要方式将它附加到路由

*   通过控制器中的构造函数方法
*   通过这条路线

通过构造器分配中间件

通过控制器上的构造器分配中间件提供了更大的灵活性，因为它提供了两个重要的功能`except($parameters)`和`only($parameters)`，这两个功能可用于阻止或允许中间件应用于该控制器中的一些辅助功能。在不使用助手功能的情况下，中间件应用于控制器上的每一个功能。

```
<?php
use Illuminate\Http\Request;

class ForumController extends Controller
{

    public function __construct(){
      /**in this case the middleware named auth is applied
       to every single function within this controller
       */
        $this->middleware('auth');
    }

    public function viewForum(){

      return view('index');
    }

    public function edit($id){

    }

    public function delete($id){

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

通过`except`和`only`功能，我们可以选择中间件将应用于哪些功能，如下所示:

```
<?php
use Illuminate\Http\Request;

class ForumController extends Controller
{

    public function __construct(){
      /**the authentication middleware here applies to all functions but
       viewForums() and viewForumDetails() and the opposite of this happens
       when you use only()
       */
        $this->middleware('auth')->except(['viewForums', 'viewForumDetails']);
    }

    public function viewForums(){

      return view('index');
    }

    public function edit($id){

    }

    public function delete($id){

    }

    public function viewForumDetails(){

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过路由的中间件分配

假设中间件已经在中注册，可以直接附加到路由，如下所示:

```
<?php
//method 1
Route::get('admin/profile', function () {
  //action
})->middleware('auth');

/**method 2
or using the fully qualified class name like so:
*/
use App\Http\Middleware\CheckAge;

Route::get('admin/profile', function () {
    // action
})->middleware(CheckAge::class);

//method 3
Route::group(['middleware' => ['web']], function () {
    //action
}); 
```

Enter fullscreen mode Exit fullscreen mode

N:B 中间件组可以像单个中间件一样被分配给一个路由

中间件参数

附加参数可以传递给中间件。一个典型的例子是，每个用户 id 被分配给一个角色，中间件检查用户的角色，以确定他/她是否有权访问所请求的 URI。参数可以传递给中间件，如下所示:

```
<?php
//First method (Through route)
Route::get('admin/profile', function () {
  //action
})->middleware('auth:<role>'); //<role> here should be replaced by whatever parameter the user intends to pass.

//Second method (Through a controller)
use Illuminate\Http\Request;

class ForumController extends Controller
{

    public function __construct(){
        $this->middleware('auth:<role>');
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

通过用逗号分隔每个参数，可以将多个参数传递给中间件。

```
<?php
Route::get('admin/profile', function () {
  //action
})->middleware('auth:<role>,<age>,<country>'); //<role>, <age>, <country> here should be replaced by whatever parameters the user intends to pass. 
```

Enter fullscreen mode Exit fullscreen mode

这些参数在变量`$next`之后被传递给中间件的句柄函数

```
<?php
class RedirectIfSuperAdmin
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next, $role, $age, $country)
    {   
        //Logic for the middleware using the parsed parameters
        return $next($request);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

摘要

要创建一个中间件，您需要经历以下过程

*   用 artisan 命令`php artisan make:middleware <Middleware Name>`创建中间件。
*   在 kernel.php 注册位于 app→Http 文件夹中的中间件
*   在创建的中间件中编写您的逻辑
*   将中间件分配给路由或控制器

结论

Laravel 中间件使得保护我们的路径、净化输入和做许多其他事情变得更加容易，而不需要写太多的逻辑。查看官方的 Laravel 文档[这里](https://laravel.com/docs/5.5/middleware)了解更多中间件的特性和最重要的实践。