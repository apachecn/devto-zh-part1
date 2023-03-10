# 学习语言:安装和配置

> 原文：<https://dev.to/nathanheffley/learning-laravel-installation-and-configuration-144>

Laravel 是“Web 工匠的 PHP 框架”,在过去几年里，它已经成为 PHP 后端开发的热门话题。在你开始学习它之前，你需要学习如何使用它建立一个新的项目。

> 注意:本文将假设您已经在开发计算机上安装了 PHP 7、Composer、NPM 和 MySQL。虽然这是 Laravel 的初学者课程，但不是其他基础系统的课程。众所周知，PHP 很难在 Windows 上安装，所以为了简洁起见，我不会在本文中讨论这个问题。

## 安装 Laravel

要开始使用 Laravel，首先需要安装命令行工具。这个工具可以让你快速启动新项目。你所需要做的就是使用 Composer 来要求专用的安装程序。

```
composer global require "laravel/installer" 
```

Enter fullscreen mode Exit fullscreen mode

为了能够在系统中的任何地方使用这个新命令，您需要确保您的 Composer vendor bin 作为$PATH 系统变量的一部分。一旦您成功地配置了安装程序，您就可以通过创建一个新项目来测试它。

```
laravel new sitename 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，您将拥有一个名为 sitename 的新文件夹，其中包含一个全新的 Laravel 项目。

## 下载依赖项

现在您已经创建了项目框架，将`cd`放入您的 sitename 文件夹并准备工作。您需要做的第一件事是安装依赖项。使用 Composer，这是尽可能容易的。

```
composer install 
```

Enter fullscreen mode Exit fullscreen mode

根据您的计算机和互联网连接，再等一会儿，您就可以开始了！

Laravel 还带有一个内置的构建系统，它使用 Webpack 来编译您的 JavaScript 和 Sass，并向前端提供 Vue。为了利用这个预先配置的系统，您还需要安装您的 NPM 依赖项。

```
npm install

npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

任何时候你想编译你的资产，你所要做的就是运行`dev`命令，所有的东西都会被编译到你的公共目录中。

> 一旦你在一个生产服务器上启动你的站点，你应该使用一个`npm run prod`命令来代替`dev`。在开发过程中，还有一个方便的`npm run watch`命令，它会在你对任何资产进行修改时重新编译你的资产。

安装完所有的依赖项后，就可以进入设置项目的最后一步了。

## 配置您的新项目

剩下的工作就是更改实际的配置文件。在您的项目中，应该有一个名为`.env`的文件。如果找不到，复制`.env.example`文件，自己创建一个`.env`文件。最重要的配置设置都在这个文件中。如果您使用 Git 这样的源代码控制(您应该这样做),请不要将这个文件提交到您的存储库中。它将包含敏感数据，如数据库用户名和密码，以及开发应用程序时需要的任何其他 API 令牌或密码。

为了启动和运行 Laravel，我们现在实际上不需要更改这里的任何设置。当你在自己的项目中工作时，最有可能改变的第一个设置是`DB_DATABASE`、`DB_USERNAME`和`DB_PASSWORD`变量。到时候我会让你自己决定应该在每个盒子里放什么！

但是，在您实际查看站点之前，您确实需要为`APP_KEY`变量设置一个值。你可以在那里手动输入一串随机的字母和数字，或者你可以回到你的终端，使用 Laravel 自带的一个内置命令。

```
php artisan key:generate 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到一条消息，提示您的`Application key [base64:gibberishhere=] set successfully.`如果您再次查看您的`.env`文件，您会看到`APP_KEY`现在已经设置好了，您已经准备好最终在浏览器中签出您的项目了！

如果您有一个像 Apache 或 Nginx 这样的本地服务器系统，您可以创建一个将用户指向您的`/sitename/public`目录的服务器。出于开发目的，您可以简单地使用另一个内置的 artisan 命令来启动本地服务器。

```
php artisan serve 
```

Enter fullscreen mode Exit fullscreen mode

会有消息说一个`Laravel development server started`和一个 URL。你应该可以访问`http://localhost:8000`或`http://127.0.0.1:8000`，并看到预装的默认欢迎页面！

恭喜你！你现在已经准备好开始创建令人惊叹的网站了。我迫不及待地想看看你有什么不可思议的创造，我很想听听他们的故事。

* * *

如果你有兴趣了解更多关于 Laravel 的信息或者只是想聊聊天，请访问我的网站。

我很想听听你们的想法。有人对更多的初学者教程感兴趣吗？