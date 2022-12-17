# 用 PHP 捕捉和解析 JSON Post 数据

> 原文：<https://dev.to/dev_nope/catch-and-parse-json-post-data-in-php-1p56>

几天前，我一直在做一个项目，这个项目和今天的许多其他项目一样，与一些外部 API 进行通信。在某些时候，为了执行一些测试，我不得不在我的项目中编写一个 API 端点来伪造实际的外部 API。我需要提到的是，外部 API 需要的是`application/json`内容类型，所以基本上`curl`设置看起来是这样的:

```
<?php

//...

$ch = curl_init();
curl_setopt( $ch, CURLOPT_URL, $url );
curl_setopt( $ch, CURLOPT_RETURNTRANSFER, true );
curl_setopt( $ch, CURLOPT_VERBOSE, true );
curl_setopt( $ch, CURLOPT_HEADER, true );
curl_setopt( $ch, CURLOPT_TIMEOUT, $this->timeout_limit );
curl_setopt( $ch, CURLOPT_ENCODING, '' );
curl_setopt( $ch, CURLOPT_MAXREDIRS, 10 ); 
curl_setopt( $ch, CURLOPT_CUSTOMREQUEST, 'POST' );
curl_setopt( $ch, CURLOPT_POSTFIELDS, json_encode( $data ) );
curl_setopt( $ch, CURLOPT_HTTPHEADER, array(
   'Content-Type: application/json'
) );

// ... 
```

Enter fullscreen mode Exit fullscreen mode

问题是，当我试图捕捉帖子数据并使用它时，我得到了作为`$_POST`或`$_REQUEST`的值的`null`，我不明白为什么🤔。显然(也许你们中的许多人已经知道了)，如果数据以`multipart/form-data`或`application/x-www-form-urlencoded`的形式发送，那么是的，您可以使用 PHP globals `$_POST`或`$_REQUEST`来捕捉和使用这些数据。但是，如果数据是作为`application/json`发送的，那么在 PHP 5.6 之前，您可以使用 [$HTTP_RAW_POST_DATA](http://php.net/manual/en/reserved.variables.httprawpostdata.php) 来获取原始的 POST 数据，或者(基于 PHP 文档，这是更好的方法)使用[PHP://input](http://php.net/manual/en/wrappers.php.php)——一个只读流，允许您从请求体读取原始数据。

所以，结果我最终获得了这样的帖子数据:

```
<?php
// receive the JSON Post data
$data = json_decode( file_get_contents( 'php://input' ), true ); 
```

Enter fullscreen mode Exit fullscreen mode

这些 stackoverflow 讨论大有功劳:
[https://stack overflow . com/questions/2731297/file-get-contentsphp-input-or-http-raw-post-data-哪一个更好](https://stackoverflow.com/questions/2731297/file-get-contentsphp-input-or-http-raw-post-data-which-one-is-better-to)
[https://stack overflow . com/questions/813487/how-to-post-JSON-to-PHP-with-curl/813512](https://stackoverflow.com/questions/813487/how-to-post-json-to-php-with-curl/813512)

大家编码快乐！💻 🤓 👍