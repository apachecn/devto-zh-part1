# 我们如何以及为什么简化 SparkPost PHP 客户端库

> 原文：<https://dev.to/sparkpost/how-and-why-we-simplified-the-sparkpost-php-client-library>

你可能还记得去年我们发布了 SparkPost PHP 客户端库的 2.0 版本。我们已经讨论了我们的方法[在我们所有的客户端库](https://www.sparkpost.com/blog/client-libraries/)中的演变，但是我想更深入地研究 PHP 的变化。

## 为什么

在 18 个月的时间里，我们的客户端库处于无约束状态，我们已经学到了一些重要的经验。最大的一个问题是，当你在包装一个活的、不断增长的 API 时，抽象是困难的。随着 16 个 API 端点和[更多的到来](https://developers.sparkpost.com/api/labs-introduction.html?_ga=2.195912812.1986239921.1501527668-424867781.1485888065)，我们限制了我们的用户和我们自己，因为我们的客户端库落后于我们的 API 添加。我们改变了思路，专注于提供一个带有一些语法“糖”的抽象薄层，以简化 API 中更复杂的部分。

## 使用 PHP 客户端库

在新版本中，我们决定让一个方法驱动所有的 SparkPost 请求。这让您可以快速、灵活地访问任何 API 端点。

```
<?php
// in 1.x
$sparky->setupUnwrapped('metrics');

$results = $sparky->metrics->get('deliverability', [
  'from'=>'2016-03-07T17:00',
  'to'=>'2016-03-08T17:30',
  'metrics'=>'count_targeted,count_injected,count_rejected',
  'timezone'=>'America/New_York'
]);

// in 2.x
$promise = $sparky->request('GET', 'metrics/deliverability', [
  'from'=>'2016-03-07T17:00',
  'to'=>'2016-03-08T17:30',
  'metrics'=>'count_targeted,count_injected,count_rejected',
  'timezone'=>'America/New_York'
]); 
```

Enter fullscreen mode Exit fullscreen mode

在 1.x 版本中，我们做了大量的属性映射。例如，要启用内联 css，您可以在调用`$sparky->transmission->send`时设置`inlineCss => true`。现在在 2.x 版本中，我们只是传递你提供的数据，所以为了内联 css，你可以设置`'options' => [ 'inline_css' => true ]`。虽然乍一看这可能更复杂，但它实际上使它更容易使用，因为你现在可以依靠我们的主 [API 文档](https://developers.sparkpost.com/api/transmissions.html?_ga=2.35456993.1986239921.1501527668-424867781.1485888065#transmissions-retrieve-and-delete-get)，快速将 JSON 翻译成关联数组。

我们还去掉了不推荐的`egeloen/ivory-http-adapter`，使用了`php-http/httplug`。这意味着您可以继续使用自己的 HTTP 请求库，并增加了在 PHP 中使用[承诺](http://docs.php-http.org/en/latest/components/promise.html)的新能力。

## 如何使用

在 [github repo](https://github.com/SparkPost/php-sparkpost/tree/master/examples) 中，我们有很多关于如何使用我们新的 2.x 库的例子。下面是如何开始的快速浏览。

### 安装&设置

首先，我们需要从 composer 中提取库。如果你没有这样的设置，请查看他们的[入门页面](https://getcomposer.org/doc/00-intro.md)。(如果你使用的是早于 5.6 的 PHP 版本，或者你不能使用 composer，你仍然可以使用我们的 API 和一些[简单的 cURL magic](https://github.com/SparkPost/php-sparkpost/issues/164#issuecomment-289888237) ！)

我们将使用 [guzzle](http://docs.guzzlephp.org/en/stable/) 作为我们的请求库。您可以使用任何一个[支持的客户端](https://packagist.org/providers/php-http/client-implementation)。

```
composer require guzzlehttp/guzzle
composer require php-http/guzzle6-adapter
composer require sparkpost/sparkpost 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了依赖项，我们可以设置库了。为了同步运行我们的代码，我们将把`async`设置为`false`。

```
<?php
require 'vendor/autoload.php';

use SparkPost\SparkPost;
use GuzzleHttp\Client;
use Http\Adapter\Guzzle6\Client as GuzzleAdapter;

$httpClient = new GuzzleAdapter(new Client());
$sparky = new SparkPost($httpClient, ['key'=>'YOUR_API_KEY', 'async' => 
false]); 
```

Enter fullscreen mode Exit fullscreen mode

### 楼宇传输

看下面的传输，我们可以看到它几乎是相同的一个定期 SparkPost 传输。不同的是我们加了一个`cc`和`bcc`。这就是我提到“糖”。该库将检查并格式化您的`cc`和`bcc`收件人，以符合 SparkPost API 的要求。

```
<?php
$transmissionData = [
    'content' => [
        'from' => [
            'name' => 'SparkPost Team',
            'email' => 'from@sparkpostbox.com',
        ],
        'subject' => 'Mailing via PHP library 2.x',
        'text' => 'Woo! We did the thing!',
    ],
    'recipients' => [
        [
            'address' => [
                'name' => 'YOUR_NAME',
                'email' => 'YOUR_EMAIL',
            ],
        ],
    ],
    'cc' => [
        [ 'address' => [ 'name' => 'ANOTHER_NAME', 'email' => 'ANOTHER_EMAIL' ] ]
    ],
    'bcc' => [
        [ 'address' => [ 'name' => 'AND_ANOTHER_NAME', 'email' => 'AND_ANOTHER_EMAIL' ] ]
    ],
] 
```

Enter fullscreen mode Exit fullscreen mode

```
<?php
try {
    $response = $sparky->transmissions->post($transmissionData);
    echo $response->getStatusCode()."\n";
    print_r($response->getBody())."\n";
}
catch (\Exception $e) {
    echo $e->getCode()."\n";
    echo $e->getMessage()."\n";
} 
```

Enter fullscreen mode Exit fullscreen mode

这就对了。您的电子邮件已关闭！

深入图书馆，在 [GitHub](https://github.com/SparkPost/php-sparkpost) 上查看回购协议，或者来与我们在 [Slack](http://slack.sparkpost.com/?_ga=2.103678784.1986239921.1501527668-424867781.1485888065) 的团队交流。如果你发现一个 bug(ðÿ˜),提交一个[问题](https://github.com/SparkPost/php-sparkpost/issues)或者一个[公关](https://github.com/SparkPost/php-sparkpost/pulls)。请稍后回来查看即将发布的关于在一些流行的 PHP 框架中使用 SparkPost 的帖子。

*本帖最初发表于[sparkpost.com](https://www.sparkpost.com/blog/php-client-library-2-0/)T3】*