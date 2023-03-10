# ★ Laravel-medialibrary v7 预览版:多文件下载

> 原文：<https://dev.to/freekmurze/laravel-medialibrary-v7-preview-multi-file-downloads-29a7>

laravel-medialibrary 是一个强大的软件包，可以帮助处理 laravel 应用程序中的媒体。它可以[跨多个文件系统](https://docs.spatie.be/laravel-medialibrary/v6/advanced-usage/working-with-multiple-filesystems)、[组织你的文件，生成缩略图](https://docs.spatie.be/laravel-medialibrary/v6/converting-images/defining-conversions)、[优化图像](https://docs.spatie.be/laravel-medialibrary/v6/converting-images/optimizing-converted-images)等等。

我和我的团队正在努力创建一个新的主要版本 v7，它增加了许多令人敬畏的功能，如[响应图像](https://twitter.com/freekmurze/status/939161592725409796)，协助上传的 vue 组件，单个文件集合，...在这篇文章中，我想强调 v7 即将推出的一个很酷很方便的特性:多文件下载。

在我们的包中，每个文件都与一个`Media`模型相关联。要下载单个文件，您可以在控制器中进行:

```
public function download(Media $media) 
{
   return response()->download($media->getPath());
} 
```

因为`Media` [实现了](https://github.com/spatie/laravel-medialibrary/blob/ac8b8df1d2266260a20dc181b528050e09b1c9b4/src/Media.php#L236-L242) [Laravel 的 Responsable 接口](https://laravel-news.com/laravel-5-5-responsable)你也可以把那个写的稍微短一点:

```
public function download(Media $media) 
{
   return $media;
} 
```

相当甜蜜！但是如果你想一次下载多个文件呢？目前媒体库不会帮助你，你需要自己解决这个问题。您可以生成一个 zip 文件，其中包含您想要下载到服务器上的所有媒体，然后执行

```
return response()->download($pathToZippedMediaFiles); 
```

但是，也许你想下载大文件，在这种情况下，创建压缩需要一段时间。或者，也许文件存储在远程服务上，如 S3，所以现在需要更长的时间，因为文件需要先拷贝。

我很高兴地告诉大家，媒体库 v7 将为您解决这个问题。它包括一个`ZipStreamResponse`类，允许你用一个流来响应。文件将被动态压缩，你甚至可以包含来自多个文件系统的文件。

让我们来看一个如何使用`ZipStreamResponse`的例子。我们将创建两条路线。访问`add-files`会给我们的媒体库添加一些文件，访问`download-files`会下载它们。我在这里使用路线是为了演示，在现实世界的应用程序中，你可能会在控制器中使用`ZipStreamResponse`。

```
Route::get('add-files', function() {
    //create a regular model
    $article = Article::create();

    // add a file to the downloads collection on the local disk
    $article
        ->addMedia($pathToAFile)
        ->toMediaCollection('downloads');

    // add a file to the downloads collection on the s3 disk
    $article
    ->addMedia($pathToABigFile)
    ->toMediaCollection('downloads', 's3');

    return 'files added!';
});

Route::get('download-files', function() {
    //get all files in the download collection
    $allMedia = Article::first()->getMedia('downloads');

    // download them in a streamed way, so no prob if your files are very large
    return ZipStreamResponse::create('my-files.zip')->addMedia($allMedia);
}); 
```

最后一行是最重要的一行。zip 是动态创建的，它从本地磁盘和 s3 中提取数据，这有点酷。

编写代码比我想象的要容易。maennchen/zipstream-php 做了创建 zip 流的艰苦工作。我需要做的就是将提供的`ZipStream`类集成到我们的媒体库中。这里是`Spatie\MediaLibrary\ZipStreamResponse` :
的完整源代码

```
namespace Spatie\MediaLibrary;

use Illuminate\Http\Request;
use Illuminate\Http\Response;
use Illuminate\Contracts\Support\Responsable;
use Spatie\MediaLibrary\Media;
use Symfony\Component\HttpFoundation\StreamedResponse;
use ZipStream\ZipStream;

class ZipStreamResponse implements Responsable
{
    /** string */
    protected $zipName;

    /** Illuminate\Support\Collection */
    protected $mediaItems;

    public static function create(string $zipName)
    {
        return new static($zipName);
    }

    public function __construct(string $zipName)
    {
        $this->zipName = $zipName;
    }

    public function addMedia($mediaItems)
    {
        $this->mediaItems = $mediaItems;

        return $this;
    }

    public function toResponse($request)
    {
        return new StreamedZipResponse(function () {
            $zip = new ZipStream($this->zipName);

            $this->mediaItems->each(function (Media $media) use ($zip) {
                $zip->addFileFromStream($media->file_name, $media->stream());
            });

            $zip->finish();
        });
    }
} 
```

当然，有些情况下(例如，当相同的资源被反复下载时，或者当下载速度非常重要时)，您仍然希望在本地创建一个 zip 文件并存储起来以备后用。但是我相信`StreamedZipResponse`确实为众所周知的 80%提供了一个很好的解决方案

如上所述，laravel-medialibrary 的 [v7 将于 2018 年 2 月至 3 月发布，届时将提供多文件下载。当然你现在就可以开始使用 laravel-medialibrary](https://github.com/spatie/laravel-medialibrary/tree/v7) 的 [v6。](https://docs.spatie.be/laravel-medialibrary/v6/)