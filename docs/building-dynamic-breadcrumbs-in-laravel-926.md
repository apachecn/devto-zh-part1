# 在 Laravel 中构建动态面包屑

> 原文：<https://dev.to/jordanirabor/building-dynamic-breadcrumbs-in-laravel-926>

[![](img/6056911b732cfc57f502a74c98b80af3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A7hHWvcy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/40410/Yk7veZxQRSaunKy4aLB8_Untitled%25201.png.jpg)

## 面包屑的重要性

过去，web 开发人员更关心 web 应用程序能够提供的功能(后端)和信息，而不太关注 web 页面的视觉外观(前端)。这不完全是他们的错，当时没有合适的技术来开发复杂的网页设计。少数几个能施一点魔法的用起来很麻烦。

今天，这是一个完全不同的故事；web 开发中的 UI/UX 设计阶段与保持一切运行的后端逻辑的预先规划一样重要。有许多可接受的设计实践可以帮助 web 应用程序赢得“用户友好”的标签，但是还有更多设计缺陷是每个人都应该注意的。

人们讨厌看到不请自来的弹出框；我也是，但是弹出窗口并不是唯一会让用户厌烦的东西。无法在网站上找到自我并轻松导航就像在大商场迷路一样糟糕；同样的压倒性的感觉。

虽然这可能是一个严重的问题，但不一定如此！面包屑可能是给用户带来他们应得的流畅网络导航体验的最简单的解决方案。

> 面包屑提供了一个导航系统，帮助用户了解他们相对于网站上其他网页的当前位置。面包屑这个名字来源于众所周知的童话故事 [Hansel 和 Grettel](https://en.wikipedia.org/wiki/Hansel_and_Gretel) ，因为它做了几乎相同的事情；留下痕迹以防止用户迷路，从而提升用户体验。网站上的面包屑也将减少用户进入所选页面时必须触发的动作数量。

在 Laravel 中设置 breadcrumbs 非常简单。有一个包负责大部分逻辑，我们将研究如何使用这个包并从中获得最佳特性。

本教程的源代码可以在 GitHub 的[这里](https://github.com/Jordanirabor/Laravel-Breadcrumbs)找到。

## 设置 Laravel 应用程序

这整个教程是针对 Laravel 开发人员的。

我们将通过 Composer 拉入 [Laravel Breadcrumbs](https://github.com/davejamesmiller/laravel-breadcrumbs) 包，并根据用户查看的页面编写代码来动态呈现 Breadcrumbs 导航服务。

本文的结果代码可以在 GitHub 的[这里](https://github.com/Jordanirabor/Laravel-Breadcrumbs)找到。

出于本文的考虑，我们将安装一个 Laravel 应用程序的新实例。

```
laravel new breadcrumbs 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
composer create-project --prefer-dist laravel/laravel breadcrumbs 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们通过在终端中键入下面的命令来拉入 [Laravel Breadcrumbs](https://github.com/davejamesmiller/laravel-breadcrumbs) 包。

```
 composer require davejamesmiller/laravel-breadcrumbs 
```

Enter fullscreen mode Exit fullscreen mode

### 创建 HTTP 路由

让我们在`routes/web.php`文件中创建一些`HTTP`路线(并给它们命名)——我们将在本文的后面部分通过名称来引用这些路线。

以下是本示例所需的路线:

*routes/web.php*

```
 Route::get('/',  ['as' => 'home', 'uses' => 'MainController@home']);

Route::get('/continent/{name}',  ['as' => 'continent', 'uses' => 'MainController@continent']);

Route::get('/country/{name}',  ['as' => 'country', 'uses' => 'MainController@country']);

Route::get('/city/{name}',  ['as' => 'city', 'uses' => 'MainController@city']); 
```

Enter fullscreen mode Exit fullscreen mode

为了演示 Laravel Breadcrumbs 的强大功能，我们将构建一个小应用程序，在其中注册洲、国家和城市模型。我们还会说一个大陆`hasMany`个国家和一个国家`hasMany`个城市。

为了创建这些模型，我们将这些命令写到终端:

```
php artisan make:model Continent
php artisan make:model Country
php artisan make:model City 
```

Enter fullscreen mode Exit fullscreen mode

让我们也为每个模型创建迁移文件:

```
php artisan make:migration continents
php artisan make:migration countries
php artisan make:migration cities 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们定义每个模型类中的关系方法:

*app/continental . PHP*T2】

```
namespace App;
use App\Country;

use Illuminate\Database\Eloquent\Model;

class Continent extends Model
{
    public function country(){
        return $this->hasMany(Country::class);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*app/Country.php*

```
namespace App;

use App\City;
use App\Continent;

use Illuminate\Database\Eloquent\Model;

class Country extends Model
{

    protected $guarded = [];
    public function city(){
        return $this->hasMany(City::class);
    }

    public function continent(){
        return $this->belongsTo(Continent::class);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*app/City.php*

```
namespace App;

use App\Country;

use Illuminate\Database\Eloquent\Model;

class City extends Model
{

    protected $guarded = [];
    public function country(){
        return $this->belongsTo(Country::class);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意`guarded`被设置为空数组，这是因为我们打算在播种数据库时进行某种形式的批量赋值。每当您对模型文件进行这样的更改时，请记住在应用程序进入生产阶段之前恢复更改。

现在让我们创建一个控制器来处理应用程序收到的所有`HTTP`请求。我们将调用控制器`MainController`，并通过返回的视图向模型的`compact`实例声明不同的动作(`methods`)，每个视图将显示自己的面包屑。

为了创建这个控制器，我们将这个命令写到终端:

```
php artisan make:controller MainController 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们编写在处理完`HTTP`请求后返回视图的动作(`methods`)。

*app/Http/Controllers/main Controllers . PHP*T2】

```
namespace App\Http\Controllers;
use App\Continent;
use App\Country;
use App\City;
use Illuminate\Http\Request;

class MainController extends Controller
{
    public function home(){
        return view('home');
    }

     public function continent($name){
           $continent = Continent::where('name', $name)->first();
        return view('continent', compact('continent'));
    }

     public function country($name){
           $country = Country::where('name', $name)->first();
        return view('country', compact('country'));
    }

     public function city($name){
           $city = City::where('name', $name)->first();
        return view('city', compact('city'));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经包含了模型之间的关系，所以当涉及到动态链接和关系属性时，我们可以探索 [Laravel Breadcrumbs](https://github.com/davejamesmiller/laravel-breadcrumbs) 包的功能。

现在让我们创建一些视图来匹配我们刚刚创建的模型。在`resources/views`目录中，我们将添加四个新文件，我们将它们命名为:

```
home.blade.php
continent.blade.php
country.blade.php
city.blade.php 
```

Enter fullscreen mode Exit fullscreen mode

它们的名字相当直观；第一个将保存主页的前端代码，第二个将保存大陆页面的前端代码，第三个将保存国家页面的前端代码，最后一个将保存城市页面的前端代码。

很好，现在让我们在这些新视图中放一些代码:

*resources/views/home . blade . PHP*T2】

```
<!DOCTYPE html>
<html>
<head>

<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-alpha.6/css/bootstrap.min.css" integrity="sha384-rwoIResjU2yc3z8GV/NPeZWAv56rSmLldC3R/AZzGRnGxQQKnKkoFVhFQhNUwEyJ" crossorigin="anonymous">

 </head>
 <body>

{{ Breadcrumbs::render('home') }}

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

*resources/views/continental . blade . PHP*T2】

```
<!DOCTYPE html>
<html>
<head>

<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-alpha.6/css/bootstrap.min.css" integrity="sha384-rwoIResjU2yc3z8GV/NPeZWAv56rSmLldC3R/AZzGRnGxQQKnKkoFVhFQhNUwEyJ" crossorigin="anonymous">

 </head>
 <body>

{{ Breadcrumbs::render('continent', $continent) }}

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

*resources/views/country . blade . PHP*T2】

```
<!DOCTYPE html>
<html>
<head>

<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-alpha.6/css/bootstrap.min.css" integrity="sha384-rwoIResjU2yc3z8GV/NPeZWAv56rSmLldC3R/AZzGRnGxQQKnKkoFVhFQhNUwEyJ" crossorigin="anonymous">

 </head>
 <body>

{{ Breadcrumbs::render('country', $country->continent, $country) }}

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

*资源/观点/城市.刀锋. php*

```
<!DOCTYPE html>
<html>
<head>

<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-alpha.6/css/bootstrap.min.css" integrity="sha384-rwoIResjU2yc3z8GV/NPeZWAv56rSmLldC3R/AZzGRnGxQQKnKkoFVhFQhNUwEyJ" crossorigin="anonymous">

 </head>
 <body>

{{ Breadcrumbs::render('city', $city->country->continent, $city->country, $city) }}

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

`Continent`和`Country`模型展示了一种`One To Many`关系。许多城市属于一个国家，许多国家属于一个大陆。一个大陆是一个城市的祖父母，而一个国家是一个城市的父母。在这篇文章中，我们不会深入研究绑定拉勒维尔关系的逻辑，但是如果你想了解它，你可以在这里找到好的知识[。](https://scotch.io/courses/laravel-eloquent-relationships/getting-started)

最后，让我们稍微修改一下迁移文件，以便它们为数据库中的表定义适当的结构。

*数据库/迁移/2017 _ 11 _ 02 _ 092826 _ continentals . PHP*

```
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class Continents extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('continents', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        //
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*数据库/迁移/2017 _ 11 _ 02 _ 092835 _ countries . PHP*

```
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class Countries extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('countries', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->string('continent_id');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        //
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*数据库/迁移/2017 _ 11 _ 02 _ 092845 _ cities . PHP*

```
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class Cities extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('cities', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->string('country_id');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        //
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在终端上用这个命令运行迁移:

```
php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

让我们在数据库中插入一些数据，这样我们就可以测试这个应用程序。为此，我们将创建三个`factory`文件【每个模型一个】，并更新`database/seeds`目录中【已经存在的】`DatabaseSeeder.php`文件的`run`方法。

为了创建工厂文件，我们将这些命令写到终端:

```
php artisan make:factory ContinentFactory --model=Continent
php artisan make:factory CountryFactory --model=Country
php artisan make:factory CityFactory --model=City 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将分别创建这些文件:

*数据库/工厂/ContinentFactory.php*

```
 use Faker\Generator as Faker;

$factory->define(App\Continent::class, function (Faker $faker) {
    return [
        //
    ];
}); 
```

Enter fullscreen mode Exit fullscreen mode

*数据库/工厂/国家工厂. PHP*T2】

```
 use Faker\Generator as Faker;

$factory->define(App\Country::class, function (Faker $faker) {
    return [
        //
    ];
}); 
```

Enter fullscreen mode Exit fullscreen mode

*数据库/工厂/city factory . PHP*T2】

```
 use Faker\Generator as Faker;

$factory->define(App\City::class, function (Faker $faker) {
    return [
        //
    ];
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们更新 Laravel 应用程序的每个新实例附带的`DatabaseSeeder.php`文件。我们将使用该文件向数据库中插入三行，我们将插入`Africa`(洲模型)、`South Africa`(国家模型)和`Johannesburg`(城市模型)，我们还将指定它们的关系:

```
 use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {

      factory(App\City::class)->create([
        'name' => 'Johannesburg',
        'country_id' => function(){

            return factory(App\Country::class)->create([
                'name' => 'South Africa',
                'continent_id' => function(){

                    return factory(App\Continent::class)->create([
                        'name' => 'Africa' ])->id;
                }
                ])->id;
        }
        ]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在终端使用这个命令播种数据库:

```
php artisan db:seed --class=DatabaseSeeder 
```

Enter fullscreen mode Exit fullscreen mode

## 设置面包屑文件

我们需要在 routes 目录中创建一个`breadcrumbs.php`文件。每当呈现 breadcrumb 时都会引用这个文件，因为它指示 Laravel 如何处理 breadcrumb 信息。如果没有这个文件，每当 Laravel 在视图中遇到对 breadcrumb 函数的调用时，它就会在我们面前发出一个错误。

我们将在新创建的`breadcrumbs.php`文件中定义的第一个 breadcrumb 函数是我们主页的函数。每当访问名为“home”的路线时，都会加载“home”面包屑。

*routes/bread crumbs . PHP*

```
Breadcrumbs::register('home', function ($breadcrumbs) {
     $breadcrumbs->push('Home', route('home'));
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 呈现静态面包屑

在上面的代码中，我们看到`Breadcrumbs`类被用来调用一个静态方法。register 方法注册一个名为 home 的新 breadcrumb，并调用一个闭包。然后闭包接受一个$breadcrumbs 参数，并在其 URL 旁边推送一个新的 breadcrumbs 实例。

```
 $breadcrumbs->push('Home', route('home')); 
```

Enter fullscreen mode Exit fullscreen mode

`push`方法中的“home”是硬编码的，当面包屑呈现在 Home 视图或任何其他需要`Home`面包屑显示完整导航链的视图上时，会出现什么。

`route('home')`返回“主页”的 URL，并且当它被呈现在任何视图上时将成为`Home`指向的链接。

最后，在主视图代码中，这是我们呈现面包屑的方式。

*resources/views/home . blade . PHP*T2】

```
{{ Breadcrumbs::render('home') }} 
```

Enter fullscreen mode Exit fullscreen mode

`render`方法接收要在主视图上显示的面包屑的名称。在包含某种形式的数据库关系的更复杂的导航链中，`render`函数将接受尽可能多的`Models`实例作为参数，以呈现完整的面包屑导航链。

我们接下来会详细讨论这一点。

## 渲染一个动态面包屑

当我们有一个 web 应用程序，我们不知道用户将要访问的页面的确切属性，会发生什么？例如，我们有一个显示各大洲、国家和城市信息的网站。我们不能总是将任何洲、国家或城市的名称硬编码到`routes/breadcrumbs.php`文件中，因为我们永远不知道用户会查看哪个。

为了使动态面包屑实时呈现成为可能，我们可以编写`routes/breadcrumbs.php`文件，以便在我们的应用程序中有效地与动态`Models`一起工作，并在用户探索我们的 web 应用程序的更深层次时创建面包屑导航链。

我们之前说过，大陆`Model` `hasMany`国家和国家`Model` `hasMany`城市。了解了这一点，如果我们访问南非的约翰内斯堡，我们应该能够得到类似这样的面包屑显示，因为南非属于非洲。

[![](img/a8152b6ed01cc9d72a66ec34cc896617.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0_fkvwyw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/40410/FinwqBwHSYyWCCIwKflV_breadcrumb.png)

为了使用我们上面看到的`Model`关系生成面包屑导航链，我们从编写注册`continent`面包屑的代码开始:

*routes/bread crumbs . PHP*

```
Breadcrumbs::register('continent', function ($breadcrumbs, $continent) {
    $breadcrumbs->parent('home');
    $breadcrumbs->push($continent->name, route('continent', ['name' => $continent->name]));
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们注册一个`continent`面包屑并传递一个`closure`。闭包接受一个新参数，`$continent`(将由调用视图实时提供)。接下来，`home`面包屑被指定为父级，最后，`$continent`面包屑的名称和 URL 被推送。

为了在大陆视图代码中呈现面包屑，我们编写了以下代码片段:

*resources/views/continental . blade . PHP*T2】

```
{{ Breadcrumbs::render('continent', $continent) }} 
```

Enter fullscreen mode Exit fullscreen mode

`render`方法接收面包屑的名称作为第一个参数。它还接收一个`$continent`参数，这将有助于将`continent` `Model`的实例解析为它的基本属性，比如名称和 URL。

在我的本地机器上，访问`http://127.0.0.1:8000/continent/africa`将导致带有面包屑显示的`Africa`大陆页面。

[![](img/973c63079094688ddac28de236cf7b42.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j3C-5Zdy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/40410/dJFNYZqgRX6qFIBeC2DE_bread.png)

## 渲染一个完整的导航链

我们已经查看了呈现单个面包屑的代码，现在让我们看看呈现完整面包屑链的代码和逻辑。出于本文的考虑，我们将呈现一个与国家页面相关的城市页面，然后是一个大陆页面，最后是一个主页。

这是`routes/breadcrumbs.php`文件的最终代码[对于本文，它可以变得更大，这取决于您的目标]:

*routes/bread crumbs . PHP*

```
Breadcrumbs::register('home', function ($breadcrumbs) {
     $breadcrumbs->push('Home', route('home'));
});

Breadcrumbs::register('continent', function ($breadcrumbs, $continent) {
    $breadcrumbs->parent('home');
    $breadcrumbs->push($continent->name, route('continent', ['name' => $continent->name]));
});

Breadcrumbs::register('country', function ($breadcrumbs, $continent, $country) {
    $breadcrumbs->parent('continent', $continent);
    $breadcrumbs->push($country->name, route('country', ['name' => $country->name]));
});

Breadcrumbs::register('city', function ($breadcrumbs, $continent, $country, $city) {
    $breadcrumbs->parent('country', $continent, $country);
    $breadcrumbs->push($city->name, route('city', ['name' => $city->name]));
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们添加了另外两个面包屑— `country`和`city` —我们需要这些额外的面包屑，以便当视图调用包含多个面包屑的导航链时，所有的面包屑可以有效地相互通信。

注册和推送`country`和`city`面包屑的方法与注册和推送`continent`面包屑的方法相同，所以我们不会详细讨论。

要在城市页面的视图代码中呈现 breadcrumb，我们只需在所需位置插入以下代码片段:

*资源/观点/城市.刀锋. php*

```
 {{ Breadcrumbs::render('city', $city->country->continent, $city->country, $city) }} 
```

Enter fullscreen mode Exit fullscreen mode

这里的`render`方法接收面包屑的名称作为第一个参数。接下来，它接收一个`$city->country->continent`参数，该参数将评估为一个`continent`T3 的实例，它还接收一个`$city->country`参数，该参数是国家/城市`belongsTo`，最后是一个`$city`实例。

就是这样。我们只用了几行代码就完全生成了功能齐全的动态面包屑导航。

## 结论

在一个互联网服务的“用户”寻求最容易使用的产品的世界里，开发者在网站上添加面包屑是正确的。这篇文章告诉我们，只需几行代码，我们就可以创建一个全功能的导航服务。使用 [Laravel Breadcrumb](https://github.com/davejamesmiller/laravel-breadcrumbs) 包还可以做更多的事情【例如，我们可以根据自己的喜好改变样式和布局】，尽可能地探索它。

本教程的源代码可以在 GitHub 的[这里](https://github.com/Jordanirabor/Laravel-Breadcrumbs)找到。