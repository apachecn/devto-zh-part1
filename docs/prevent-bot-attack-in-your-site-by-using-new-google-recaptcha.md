# 通过使用新的 Google reCAPTCHA 来防止您站点中的 Bot 攻击

> 原文：<https://dev.to/geordyjames/prevent-bot-attack-in-your-site-by-using-new-google-recaptcha>

如今，我们的网站面临的僵尸攻击和垃圾邮件数量大幅增加。但是 web 开发人员可以很容易地通过在表单提交和登录系统中使用 reCAPTCHA 来防止它们。集成 reCAPTCHA 最好也最容易的方法之一是使用 google reCAPTCHA，这是 google 提供的一项免费服务。谷歌 reCAPTCHA 的主要进步是它的外观和感觉，易于访问者使用。

现在，在我们开始编码之前，我们需要注册我们的网站，并从谷歌获得一个客户端密钥和密钥。你可以从谷歌免费注册你的网站。注册过程相当简单和直接，所以我跳过它。

客户端集成相当简单
将这段代码粘贴到 HTML 模板的结束标记之前:

```
<script src='https://www.google.com/recaptcha/api.js'></script> 
```

然后将这段代码粘贴到

where you want the reCAPTCHA widget to appear:

```
<div class="g-recaptcha" data-sitekey="6LdV2ygTAAAAACZ2Js-_G6uLp99TEn0MUNpDPS3c"></div> 
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

 $url = 'https://www.google.com/recaptcha/api/siteverify?'.http_build_query($data);
 $response = file_get_contents($url);

 return $response;
 } 
```

您可以通过访问[此链接](https://shareurcodes.com/blog/new%20google%20recaptcha%20integration%20with%20php)下载完整的代码和库。