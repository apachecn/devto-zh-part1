# 我的第一个 npm 包猫鼬-摩根

> 原文：<https://dev.to/nempet/my-first-npm-package-mongoose-morgan-dak>

嗨，

最近我在做我的一个 express 应用程序项目，我需要将来自 morgan npm 包的所有数据记录到数据库中。所以我发现有一些 npm 包可以做这件事，但是我必须自己创建一个。

这个包被命名为**mongose-mor gan**，这里有一个[链接](https://www.npmjs.com/package/mongoose-morgan)到它。

所以 mongose-morgan 是一个 npm package express 中间件，它通过添加额外的功能将 mongose 和 morgan 包结合起来，将 mor gan 数据记录到 MongoDB 中。

要安装它只需调用:

```
npm install mongoose-morgan 
```

Enter fullscreen mode Exit fullscreen mode

要使用它的所有功能，只需将此代码添加到您的 express 应用程序中。

```
app.use(morgan({
    collection: 'error_logger'
    connectionString: 'mongodb://localhost:27017/logs-db',
    user: 'admin',
    pass: 'test12345'
   },
   {
    skip: function (req, res) {
        return res.statusCode < 400
    }
   },
   'dev'
)); 
```

Enter fullscreen mode Exit fullscreen mode

更重要的是，你拥有摩根软件包的所有可用功能，如**格式**和**选项**功能，所以你可以选择任何你想要的。