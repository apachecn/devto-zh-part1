# 如何在 Laravel 中使用实数常量

> 原文：<https://dev.to/mariordev/how-to-use-real-constants-in-laravel-977>

我在网上看过一些帖子，建议在 Laravel 中使用配置变量作为常量。我看到的问题是，这些不是真正的常数。它们是变量。如果您需要在 Laravel 中使用实数常量，有几种方法可以加载您自己的常量。让我告诉你我是怎么做的。

首先，在 Laravel 应用程序的 bootstrap 文件夹中创建一个名为`constants.php`的文件。在这个文件中定义你的常量，这样它们就都在一个地方了。例如:

### constants.php

```
<?php
define('COLOR_ONE', 'blue');
define('COLOR_TWO', 'red'); 
```

Enter fullscreen mode Exit fullscreen mode

现在，有几种方法可以加载这个文件，以便在整个应用程序中都可以使用这些常量。这些没有特定的顺序。你可以选择你喜欢的任何一种方法。

### 方法一:autoload.php

通过在文件`bootstrap/autoload.php`的末尾添加以下代码来修改该文件:

```
<?php
//Load custom constants.
require __DIR__.'/constants.php'; 
```

Enter fullscreen mode Exit fullscreen mode

当您运行您的应用程序时，这行代码将加载您的 bootstrap/constants.php 文件，其中包含您的所有常量，作为 Laravel 引导序列的一部分。

### 方法二:composer.json

或者，您可以使用`composer.json`加载 bootstrap/constants.php 文件，方法是将以下代码添加到“autoload”部分，如下所示:

```
"autoload": {
    "files": [
        "bootstrap/constants.php"
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

在此更改生效之前，您必须在终端中运行以下命令来重新生成 Laravel 的自动加载文件:

```
$ cd path/to/your/project
$ composer dump-autoload 
```

Enter fullscreen mode Exit fullscreen mode

运行该命令后，当您运行应用程序时，您的常量将自动加载。

这就是全部了。在 Laravel 中定义和加载自己的实常数并不太难。

这个帖子有用吗？有什么问题吗？在推特上告诉我。