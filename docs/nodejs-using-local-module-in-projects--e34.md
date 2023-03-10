# [Node.js]在项目中使用本地模块

> 原文：<https://dev.to/dimpiax/nodejs-using-local-module-in-projects--e34>

链接到 Medium 的文章:[https://Medium . com/@ dimpiax/node-js-using-local-module-in-project-c 6 FB 54 BBC 83 a](https://medium.com/@dimpiax/node-js-using-local-module-in-project-c6fb54bbc83a)

您有几个项目使用相同的抽象库，您可以持有“相同的独立副本(不好的)”或在 npm.js 创建一个私有存储库。

我将与你分享我在具体案例中的实验方法。

文件结构如下:

```
/serviceA
/serviceB
/library 
```

这两个服务都使用相同的抽象类/脚本，或者其中的一些。您可以安装本地库作为依赖，只需把本地路径。

进一步的例子将与**服务 A** 有关。

```
cd serviceA
npm i --no-save ../library 
```

重要的是**而不是**将依赖项保存在 package.json 中。此外，npm 会创建库的别名，因此在任何更改之后，您都有一个全新的版本。安装后，您可以将库的依赖文件作为默认文件。

还不错，但是在**生产**的情况下怎么办？
例如，我用 babel 建立了一个项目，想添加一些额外的文件到已建的文件夹中，我会通过 git hook，scp 或 s3 进一步上传。

*用例，更详细地说，上传 zip 文件到 s3 并更新 Lambda 函数。没有在 lambda 函数中安装依赖项的场景，所以需要按原样放置项目。*

解决方案是通过脚本将库放在构建文件夹中——虽然不好，但很有效。也许某个时候 **npm** 会通过命令支持这种方法。
例如:`import syncManager from 'library/managers/syncManager'`

准备 package.json 和里面的一些脚本。
T2`package.json:`

```
{
  "name": "serviceA",
  "version": "0.1.0",
  "main": "index.js",
  "config": {
    "sourceDir": "src",
    "buildDir": "dist",
    "library": {
      "name": "library",
      "path": "../library"
    }
  },
  "scripts": {
    "clean": "rimraf $npm_package_config_buildDir",
    "build:prod": "NODE_ENV=production npm run clean && mkdir $npm_package_config_buildDir && cp$npm_package_main package.json package-lock.json $npm_package_config_buildDir && cd $npm_package_config_buildDir && npm i",
    "pack:prod": "npm run build:prod && mkdir -p $npm_package_config_buildDir/node_modules/$npm_package_config_library_name && cp -r $npm_package_config_library_path/. $npm_package_config_buildDir/node_modules/$npm_package_config_library_name/.",
  },
  "author": "Dima Pilipenko <dimpiax@gmail.com>",
  "dependencies": {
  }
} 
```

*注意`dependencies`没有提到库。*
通过调用`npm run pack:prod`项目正准备进一步使用。

希望这种方法对其他人也有用:)

**结束。**

亲爱的读者，如果你对这种方法有什么建议或问题，让我们在评论中讨论吧。