# 在 Lumen 中使用 Cloudinary 进行图像管理

> 原文：<https://dev.to/devmelas/using-cloudinary-for-image-management-in-lumen-caf>

[![Cloudinary and Lumen](img/40ff773c0846304aeeeba362f933b4ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0WRXb_W5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pnf9xcns8qghw8valfux.png)

Cloudinary 是一个基于云的端到端映像管理应用程序。这意味着它们在后端协助我们进行图像存储，在前端协助我们进行各种图像转换。根据 cloudinary 的说法，它们是“网络和移动开发者的图像后端”。

另一方面，Lumen 是一个非常快速的基于 Laravel 的微框架，用于开发 REST APIs。如果你熟悉 Laravel，那么你可以和 Lumen 一起去。如果你不是，那就很容易产生错觉。你可以在查看[的文件。](https://lumen.laravel.com)

首先，我们需要一个全新的 Lumen 装置。您需要在本地机器上安装 php 和 composer。通过运行:
获取 lumen 的新副本

```
composer create-project laravel/lumen --pref-dist 
```

Enter fullscreen mode Exit fullscreen mode

你还需要一个 cloudinary 的账户。你可以在这里注册一个免费账户[。](https://www.cloudinary.com)

JRM2K6 开发了一个奇妙的 laravel 包，名为 [cloudder](https://github.com/jrm2k6/cloudder) 。这个包使得与 cloudinary API 的交互变得非常容易。

在新的 lumen 应用程序中，用
将 clouder 拉进来

```
composer require jrm2k6/cloudder:0.4.* 
```

Enter fullscreen mode Exit fullscreen mode

好的。这就是我们所需要的。

接下来，打开你的。env 文件(可以在 Lumen 安装的根文件夹中找到)并添加以下内容:

```
CLOUDINARY_API_KEY=XXXXXXX
CLOUDINARY_API_SECRET=XXXXXXXX
CLOUDINARY_CLOUD_NAME=YOUR_CLOUDINARY_CLOUD_NAME 
```

Enter fullscreen mode Exit fullscreen mode

更新这些值以反映您的详细信息，如您的 cloudinary 仪表盘所示。

接下来，看你的`bootstrap/app.php`文件。用
取消对该节的注释

```
$app->withFacades(); 
```

Enter fullscreen mode Exit fullscreen mode

我们只需要这一行，但是如果你想在你的应用程序中使用口才，你可以继续用
`$app->withEloquent();`取消注释。

接下来，就在`$app->withEloquent()`行下面，添加这个:

```
class_alias('JD\Cloudder\Facades\Cloudder', 'Cloudder'); 
```

Enter fullscreen mode Exit fullscreen mode

这将使我们能够像这样使用 Cloudder facade。

接下来，在类似的注册下注册 Cloudder ServiceProvider，如下所示:

```
$app->register(JD\Cloudder\CloudderServiceProvider::class); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将设置一个控制器来处理我们的请求。如果你熟悉 Laravel，你肯定知道用于创建控制器的`php artisan make:controller ControllerName`命令。这是流明没有的。但是我们可以手动创建它。在`app\Http\Controllers`中，创建一个名为 ImageController.php 的文件，并添加以下内容:

```
<?php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Cloudder;
class ImageController
{
    public function upload(Request $request)
    {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们刚刚创建了一个 ImageController 类，并添加了一个名为 upload 的方法。注意我们正在注入一个`Illuminate\Http\Request`的实例，它是我们使用`use`关键字在文件顶部附近导入的。这将使我们的 post 请求中的参数在方法中对我们可用。请注意，我们正在拉云的外观。

接下来，将下面几行添加到上传方法中:

```
$file_url = "http://yourdomain/defaultimage.png";
if ($request->hasFile('image') && $request->file('image')->isValid()){
    $cloudder = Cloudder::upload($request->file('image')->getRealPath());
    $uploadResult = $cloudder->getResult();
    $file_url = $uploadResult["url"];
}
return response()->json(['file_url' => $file_url], 200); 
```

Enter fullscreen mode Exit fullscreen mode

我们给`$file_url`变量分配一个默认值。然后我们检查在我们的请求实例中是否有有效的文件可用(假设图像是用一个键/名称`image`发送的)。如果它存在，我们将上传图像的真实路径传递给 Cloudder 的上传方法。我们不会保存任何数据，但是如果您愿意，您可以保存图像的 url 和 publicId(当然，您会愿意:)。这个方法可以接受其他参数，比如用户生成的 publicId。你可以在这里查阅 Cloudder 的文档[，了解更多有用的方法。最后，我们将文件 url 作为 JSON 响应返回。你可以把这个网址嵌入到你的`<img/>`标签的`src`属性中去看看。](https://github.com/jrm2k6/cloudder)

最后，我们将在`routes/web.php`文件中为上传设置一个发布路径，如下所示:

```
$router->post('/upload', 'ImageController@upload'); 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过向[http://your.domain/app_folder/upload](http://your.domain/app_folder/upload)发送一个 post 请求，继续用 POSTMAN 测试 api。确保将您的文件名设置为`image`。

Cloudinary 为你提供了很多，你可以在这里查看文档。
这是一个如何使用 Lumen 和 Cloudder 上传图片到 Cloudinary 的概要。如果你有任何问题，请随时提问。也让我知道你的想法。

**这是我在[导师](https://www.codementor.io/chiemelachinedum/using-cloudinary-for-image-management-in-lumen-e5azaq0hf)T3 上的帖子的精确副本*

`Happy Coding - DevMelas`