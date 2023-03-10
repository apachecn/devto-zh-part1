# 野外的 PHP 框架:近距离观察 PHP 生态系统

> 原文：<https://dev.to/sparkpost/php-frameworks-in-the-wild-a-closer-look-at-the-php-ecosystem>

### SparkPost 和 PHP 框架:触及表面

PHP 是网络上使用的较老的语言之一，创建于 90 年代中期。一些环境仍然停留在运行 PHP 3，而其他环境运行的是最新版本。在过去的几年里，围绕着令人惊叹的 PHP 框架和工具已经有了很多的发展，其中大多数都有发送邮件的简单方法。我们最近讨论了如何简化 PHP 库，但是有时候使用原生解决方案仍然会更容易。下面是如何使用 SparkPost 通过一些更流行的 PHP 框架发送邮件的快速分解。

### Plain ol' PHP

有时你只需要快速调用 API。你所需要的只是一点卷曲魔法来做你需要的。查看[这个片段](https://github.com/SparkPost/php-sparkpost/issues/164#issuecomment-289888237)来拒绝 SparkPost 请求。

```
<?php 

/**
 * Use this method to access the SparkPost API
 */
function sparkpost($method, $uri, $payload = [], $headers = [])
{
    $defaultHeaders = ['Content-Type: application/json'];

    $curl = curl_init();
    $method = strtoupper($method);

    $finalHeaders = array_merge($defaultHeaders, $headers);

    $url = 'https://api.sparkpost.com:443/api/v1/'.$uri;

    curl_setopt($curl, CURLOPT_CUSTOMREQUEST, $method);

    curl_setopt($curl, CURLOPT_URL, $url);
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);

    if ($method !== 'GET') {
        curl_setopt($curl, CURLOPT_POSTFIELDS, json_encode($payload));
    }

    curl_setopt($curl, CURLOPT_HTTPHEADER, $finalHeaders);

    $result = curl_exec($curl);

    curl_close($curl);

    return $result;
}

echo "Sending email...\n";
$email_results = sparkpost('POST', 'transmissions', [
    'content' => [
        'from' => [
            'name' => 'SparkPost Team',
            'email' => 'php@yourdomain.com',
        ],
        'subject' => 'First Mailing From PHP+cURL',
        'text' => 'Congratulations!! You just sent an email with PHP+cURL!',
    ],
    'recipients' => [
        ['address' => 'john.doe@example.com',],
    ],
], ['Authorization: YOUR_API_KEY']); 
```

### +=是

在 SparkPost，我们创建了一个 [composer 包](https://packagist.org/packages/sparkpost/sparkpost)，以简化在所有现代 PHP 环境中使用我们的 API。它安装起来很快，并提供了一些“糖”来消除 API 中一些更令人困惑的部分。为了保持我们的库尽可能的灵活，我们使用 [PHP HTTP](http://docs.php-http.org/en/latest/) 让你带自己的请求库。为了使用它，我们需要`guzzlehttp/guzzle`、`php-http/guzzle6-adapter`和`sparkpost/sparkpost`库。那么我们就可以使用下面的代码了。

```
<?php
require 'vendor/autoload.php';

use SparkPost\SparkPost;
use GuzzleHttp\Client;
use Http\Adapter\Guzzle6\Client as GuzzleAdapter;

$httpClient = new GuzzleAdapter(new Client());
$sparky = new SparkPost($httpClient, ['key'=>'YOUR_API_KEY', 'async' => false]);

$promise = $sparky->transmissions->post([
    'content' => [
        'from' => 'name' => 'SparkPost Team <phplibrary@yourdomain.com>',
        'subject' => 'First Mailing From PHP Library',
        'text' => 'Congratulations!! You just sent an email with the PHP library!',
    ],
    'recipients' => [[ 'address' => 'John Doe <john.doe@example.com>'], ],
]); 
```

### WordPress 的世界

我们自己的 [Raju](https://thehungrycoder.com/) 写了一个 [SparkPost 插件](https://github.com/SparkPost/wordpress-sparkpost/)来使用 SparkPost 作为你的电子邮件提供商。它展示了使用模板、通过 SMTP 或 API 发送等关键功能，并允许你[在大量的点上挂钩](https://github.com/SparkPost/wordpress-sparkpost/blob/master/docs/hooks.md)来动态修改电子邮件和设置。此外，它很容易[开始](https://www.sparkpost.com/blog/sparkpost-wordpress-plugin/)ðÿ˜ž

### PHPMailer

PHPMailer 可能是 PHP 中最流行的邮件发送方式了！它有一个很棒的 API，可以很容易地插入任何 SMTP 提供者。看看这个[简单的例子](https://github.com/SparkPost/code-snippets/blob/master/snippets/SMTP/smtp_example.php)来快速开始使用 SparkPost。

```
<?php

// This example uses the PHPMailer library:
// https://github.com/PHPMailer/PHPMailer

require 'vendor/autoload.php';

$mail = new PHPMailer;

// configure for sparkpost
$mail->isSMTP();
$mail->Host = 'smtp.sparkpostmail.com';
$mail->Port = 587;
$mail->SMTPSecure = 'tls';
$mail->SMTPAuth = true;
$mail->Username = 'SMTP_Injection';
$mail->Password = 'YOUR_API_KEY';

$mail->setFrom('phpmailer@yourdomain.com');
$mail->addAddress('john.doe@example.com');
$mail->Subject = 'First Mailing From PHPMailer';
$mail->Body = 'Congratulations!! You just sent an email from PHPMailer!';

if (!$mail->send()) {
  echo "Message could not be sent\n";
  echo "Mailer Error: " . $mail->ErrorInfo . "\n";
} else {
  echo "Message has been sent\n";
} 
```

### Laravel

Laravel 本身就是一头野兽，所以我只谈要点。你可以按照官方的 [Laravel 文档](https://laravel.com/docs/5.4/mail#driver-prerequisites)来设置 SparkPost 邮件驱动程序。一旦你这样做了，你就可以[生成](https://laravel.com/docs/5.4/mail#generating-mailables)和[发送](https://laravel.com/docs/5.4/mail#sending-mail)你的邮件。Matt Stauffer 写了一篇关于使用 SparkPost 和 Laravel 的文章，值得一看。

### 代码点火器

CodeIgniter 提供了一个简单的 API 来发送邮件。你可以在代码中或者通过 [config/email.php](https://www.codeigniter.com/user_guide/libraries/email.html#setting-email-preferences-in-a-config-file) 添加 SMTP 配置。看看下面的例子，快速发送一封电子邮件。

```
<?php

// inside a controller

$config = [
  'protocol' => 'smtp',
  'smtp_host' => 'smtp.sparkpostmail.com',
  'smtp_user' => 'SMTP_Injection',
  'smtp_pass' => 'YOUR_API_KEY',
  'smtp_crypto' => 'tls',
  'smtp_port' => '587',
  'newline' => "\r\n",
];

$this->load->library('email');
$this->email->initialize($config);

$this->email->from('codeigniter@yourdomain.com', 'Your Name');
$this->email->to('john.doe@example.com');
$this->email->subject('Testing SparkPost SMTP from CodeIgniter');
$this->email->message('Congratulations!! You just sent an email from CodeIgniter!');

$this->email->send(); 
```

如果您想让我们更深入地了解任何特定的工具，或者如果我们错过了您最喜欢的框架ðÿ'‹，请务必在下面发表评论或发推文给我们

*本帖最初发表于[sparkpost.com](https://www.sparkpost.com/blog/php-frameworks/)T3】*