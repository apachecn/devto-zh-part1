# 谷歌现在可以利用人工智能的无形力量将人类与机器人分开

> 原文：<https://dev.to/geordyjames/google-can-now-separate-humans-from-bots-using-invisible-powers-of-ai>

不再点击“我不是机器人”复选框，谷歌向前迈进，正式发布了它有史以来最安全和创新的隐形 reCAPTCHA 服务。谷歌利用他们先进的机器学习算法和先进的风险分析使其隐形。人类用户将在看不到“我不是机器人”复选框的情况下通过，而可疑的人和机器人仍然必须解决这些挑战。新的谷歌隐形 reCAPTCHA 有助于保护您的网站免受机器人攻击和垃圾邮件。

谷歌隐形 reCAPTCHA 的官方视频如下

现在让我们看看如何在我们的网站中轻松实现新的谷歌隐形 reCAPTCHA。

现在，在我们开始编码之前，我们需要注册我们的网站，并从谷歌获得一个客户端密钥和密钥。你可以从谷歌免费注册你的网站。注册过程相当简单和直接，所以我跳过它。

客户端集成相当容易

将此代码片段粘贴到 HTML 模板的结束标记之前:

```
<script src='https://www.google.com/recaptcha/api.js'></script> 
```

然后将这段代码粘贴到

to create a button protected by the Invisible reCAPTCHA. You will need to create a callback function to handle the result.

```
<button
class="g-recaptcha"
data-sitekey="6Lc8xBgUAAACBD8a6QoJsCLdevUFF1LEeJ0iN5_m"
data-callback="YourOnSubmitFn">
Submit
</button> 
```

服务器端的集成有点棘手

你需要通过发送给[https://www.google.com/recaptcha/api/siteverify](https://www.google.com/recaptcha/api/siteverify)来验证客户端的响应，谷歌会验证它并返回一个 JSON 响应。

这是很棘手的，所以我做的是调用一个 HTTP 调用的函数在上面的 URL 和 JSON 解码上面来自谷歌的响应，如下所示

```
 $getResponse = $this->getHTTP(
    array(
        'secret' => $this->config['secret-key'],
        'remoteip' => $remoteIp,
        'response' => $recaptcha,
    )
 );

// get reCAPTCHA server response
$responses = json_decode($getResponse, true);

private function getHTTP($data)
{

$url =    'https://www.google.com/recaptcha/api/siteverify?'.http_build_query($data);
 $response = file_get_contents($url);

return $response;
} 
```

您可以通过访问[此链接](https://shareurcodes.com/blog/google%20invisible%20recaptcha%20integration%20with%20php)下载完整的代码和库。