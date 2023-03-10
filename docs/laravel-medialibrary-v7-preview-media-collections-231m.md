# ★ Laravel-medialibrary v7 预览版:媒体收藏

> 原文：<https://dev.to/freekmurze/laravel-medialibrary-v7-preview-media-collections-231m>

laravel-medialibrary 是一个可以帮助处理 laravel 应用程序中的媒体的包。它可以[跨多个文件系统](https://docs.spatie.be/laravel-medialibrary/v6/advanced-usage/working-with-multiple-filesystems)、[组织你的文件，生成缩略图](https://docs.spatie.be/laravel-medialibrary/v6/converting-images/defining-conversions)、[优化图像](https://docs.spatie.be/laravel-medialibrary/v6/converting-images/optimizing-converted-images)等等。

就像之前在这个博客上提到的，我和我的团队目前正在创建一个新的主要版本 v7，它增加了许多令人敬畏的功能，如[响应图像](https://twitter.com/freekmurze/status/939161592725409796)，协助上传的 vue 组件，...上周你得到了一个[多文件下载](https://dev.to/laravel-medialibrary-v7-preview-multi-file-downloads)的预览，...本周，我们将看看对定义媒体集合的一些改进。

## 什么是媒体收藏？

当前版本的媒体库中已经存在媒体集合。它们允许您将不同类型的文件放入它们自己的集合中。

我们来联想一些媒体:

```
$newsItem = News::find(1);
$newsItem->addMedia($pathToImage)->toMediaCollection('images');
$newsItem->addMedia($pathToAnotherImage)->toMediaCollection('images');
$newsItem->addMedia($pathToPdfFile)->toMediaCollection('downloads');
$newsItem->addMedia($pathToAnExcelFile)->toMediaCollection('downloads'); 
```

特定集合中的所有媒体都可以这样检索:

```
// will return media instances for all files in the images collection
$newsItem->getMedia('images');

// will returns media instance for all files in the downloads collection
$newsItem->getMedia('downloads'); 
```

## V7 中的新功能

在 v7 中，媒体收藏不仅仅是对文件进行分组的名称。通过在您的模型中定义一个媒体集合，您可以将某些行为添加到集合中。

要开始使用媒体收藏，将一个名为`registerMediaCollections`的函数添加到[您准备好的模型](https://docs.spatie.be/laravel-medialibrary/v7/basic-usage/preparing-your-model)中。在该功能中，您可以使用`addMediaCollection`来启动媒体收藏。

```
// in your model

public function registerMediaCollections()
{
    $this->addMediaCollection('my-collection')
        //add options
        ...

    // you can define as much collections as needed
    $this->addMediaCollection('my-other-collection')
        //add options
        ...
} 
```

### 只允许收藏中的某些文件

您可以向`acceptsFile`传递一个回调，它将检查一个文件是否被允许进入集合。在这个例子中，我们只接受`jpeg`文件:

```
use Spatie\MediaLibrary\File;
...
public function registerMediaCollections()
{
    $this
        ->addMediaCollection('only-jpegs-please')
        ->acceptsFile(function (File $file) {
            return $file->mimeType === 'image/jpeg';
        });
} 
```

这会成功:

```
$yourModel->addMedia('beautiful.jpg')->toMediaCollection('only-jpegs-please'); 
```

这将抛出一个`Spatie\MediaLibrary\Exceptions\FileCannotBeAdded\FileUnacceptableForCollection`异常:

```
$yourModel->addMedia('ugly.ppt')->toMediaCollection('only-jpegs-please'); 
```

### 使用特定的磁盘

您可以确保添加到收藏中的文件会自动添加到某个磁盘中。

```
// in your model

public function registerMediaCollections()
{
    $this
       ->addMediaCollection('big-files')
       ->useDisk('s3');
} 
```

向`my-collection`添加文件时，该文件将被存储在`s3`磁盘上。

```
$yourModel->addMedia($pathToFile)->toMediaCollection('big-files'); 
```

添加介质时，您仍然可以手动指定磁盘名。在本例中，文件将存储在`alternative-disk`而不是`s3`。

```
$yourModel->addMedia($pathToFile)->toMediaCollection('big-files', 'alternative-disk'); 
```

### 单档收藏

如果您希望一个集合只包含一个文件，您可以在集合上使用`singleFile`。一个很好的用例是在一个`User`模型上收集头像。在大多数情况下，你会希望一个用户只有一个`avatar`。

```
// in your model

public function registerMediaCollections()
{
    $this
        ->addMediaCollection('avatar')
        ->singleFile();
} 
```

第一次向收藏中添加文件时，它将照常存储。

```
$yourModel->add($pathToImage)->toMediaCollection('avatar');
$yourModel->getMedia('avatar')->count(); // returns 1
$yourModel->getFirstUrl('avatar'); // will return an url to the `$pathToImage` file 
```

将另一个文件添加到单个文件集合时，第一个文件将被删除。

```
// this will remove other files in the collection
$yourModel->add($anotherPathToImage)->toMediaCollection('avatar');
$yourModel->getMedia('avatar')->count(); // returns 1
$yourModel->getFirstUrl('avatar'); // will return an url to the `$anotherPathToImage` file 
```

### 注册媒体转换

建议您在阅读以下段落之前，先阅读关于转换图像的章节。

通常情况下，图像转换记录在您的模型上的`registerMediaConversions`函数中。但是，图像转换也可以在媒体集合中注册。

```
public function registerMediaCollections()
{
    $this
        ->addMediaCollection('my-collection')
        ->registerMediaConversions(function (Media $media) {
            $this
                ->addMediaConversion('thumb')
                ->width(100)
                ->height(100);
        });
} 
```

向`my-collection`添加图像时，将创建一个适合 100x100 的缩略图。

```
$yourModel->add($pathToImage)->toMediaCollection('my-collection');

$yourModel->getFirstMediaUrl('thumb') // returns an url to a 100x100 version of the added image. 
```

看一看[定义转换部分](https://docs.spatie.be/laravel-medialibrary/v7/converting-images/defining-conversions)来学习你可以附加到`addMediaConversion`上的所有函数。

## 在关闭

这些改进将出现在 laravel-medialibrary 的 v7 中。v7 大概会在 2018 年 2-3 月左右发布。当然你现在就可以开始使用 laravel-medialibrary 的 [v6。](https://docs.spatie.be/laravel-medialibrary/v6/)