# 学习 Laravel:真正激进的路由

> 原文：<https://dev.to/nathanheffley/learning-laravel-really-radical-routing-4e9h>

*原贴于[nathanheffley.com](https://www.nathanheffley.com/blog/learning-laravel-really-radical-routing)T3】*

因为路线是每个用户在访问你的网站时要交互的第一件事，所以这是我们开始 Laravel 之旅的地方。

在大多数项目中，您的路线将包含在`routes\web.php`文件中。当你在创建一个全新的项目后打开它(如果你需要帮助[设置一个 Laravel 项目](https://www.nathanheffley.com/blog/learning-laravel-installation-configuration)，请阅读本系列的第一课)你会看到一般的开始路线:

```
<?php

Route::get('/', function () {
    return view('welcome');
}); 
```

Enter fullscreen mode Exit fullscreen mode

让我们来分析一下到底发生了什么。

```
<?php

Route::get('/', function () { 
```

Enter fullscreen mode Exit fullscreen mode

首先我们调用 Route facade 上的`get`函数。正如您可能已经猜到的那样，这是在设置一个响应 GET 请求的路由。该函数中的第一个参数是实际路线本身。在这种情况下，路由是为根 url 设置的。假设我们的站点在本地运行，这个路由将处理对`localhost:8000`的任何 GET 请求。任何其他类型的请求，比如 POST 或者任何到不同路径的请求，比如`localhost:8000/example`，都会忽略这条路径，继续寻找更好的匹配。

第二个参数是 function，每当有人向匹配该路由的服务器发出请求时，就会调用这个函数。在接下来的课程中，我将向你展示如何将这个函数提取到其他文件中，但是现在我们将坚持这样做。对于小型项目或简单路线，直接在路线文件中使用函数是一个可行的选择。

```
<?php

return view('welcome'); 
```

Enter fullscreen mode Exit fullscreen mode

在函数内部，我们只是返回一个叫做视图的东西。`view`函数是一个特殊的 Laravel 助手，它接受一个字符串，然后将该字符串翻译成返回的文件名。视图将在下一课中介绍，所以请务必回来查看，以了解这到底是怎么回事。现在，请放心，这将返回一个名为“欢迎”的视图。当您创建一个新项目并在`localhost:8000`第一次访问它时，这个视图显示了大的“Laravel”文本和您看到的几个链接。

总之，这条路线是说，每当有人得到'/'路线，他们应该被送回'欢迎'的看法。

让我们创建自己的路线。我们不用担心其他文件或视图，只返回一个简单的字符串。

```
<?php

Route::get('/hello', function () {
    return 'Hello, World!';
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在访问`localhost:8000/hello`，你应该会看到“你好，世界！”在你的屏幕上输出普通的旧文本。恭喜你，你已经在拉勒维尔创建了一条功能齐全的路线！

当然，从现在开始，你创建的大多数路线都不会这么简单。在下一课中，我们将学习如何创建视图，以便您可以返回完全有效的 HTML 页面。学完这一课后，你将能够使用 Laravel 创建一个完整的网站，尽管功能有限。

虽然这似乎是一个过于简单的课程，但这只是因为 Laravel 让它变得如此简单。我们肯定会在未来重新访问路线，因为这里隐藏着更多的功能，但我们需要覆盖更多的基础知识，才能充分发挥这些功能的潜力！