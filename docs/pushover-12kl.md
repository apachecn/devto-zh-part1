# 容易做的事情

> 原文：<https://dev.to/scottrobertson/pushover-12kl>

大约 2 个月前，我发现了一个不错的服务，名叫 pushover.net。

> Pushover 可以轻松地向您的 Android 和 iOS 设备发送实时通知。

我四处寻找一个像样的 PHP 库，但没有找到。他们中的大多数人要么对于这样一个简单的任务有太多的代码(基本上是一个 curl 调用)，要么完全没有经过测试。

我构建的库非常容易使用，也很容易设置。下面你可以看到一个如何发送推送通知的例子:

```
include __DIR__. '/vendor/autoload.php';

$pushover = new \Scottymeuk\Pushover\Client(['token' => '']);
$pushover->message = 'testing';

// Push Single
$push = $pushover->push($token);

// Push Multiple
$push = $pushover->pushMultiple([
    $token1,
    $token2
]); 
```

Enter fullscreen mode Exit fullscreen mode

pushover.net 唯一的缺点是[应用](https://pushover.net/clients)很贵，但是考虑到我用它做的事情的数量，它是值得的。

如果你想试试这个库，你可以在 Github 上查看。

想不出一个用例？我使用这个库构建了一个快速的 CLI 工具，使从桌面向手机推送东西变得容易。你也可以在 Github 上查看。

如果你碰巧使用了这些，请在下面的评论中告诉我。