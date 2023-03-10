# 如何使用 PHP 创建一个 JSON Web 令牌

> 原文：<https://dev.to/robdwaller/how-to-create-a-json-web-token-using-php-3gml>

我花了一年时间断断续续地开发一个名为 [ReallySimpleJWT](https://github.com/RobDWaller/ReallySimpleJWT) 的 PHP JSON Web Token 库，本周我发布了[版本 1.0.0](https://github.com/RobDWaller/ReallySimpleJWT/releases/tag/1.0.0) 。该代码可通过 [GitHub](https://github.com/RobDWaller/ReallySimpleJWT) 和[包装商](https://packagist.org/packages/rbdwllr/reallysimplejwt)访问。

对于那些没有使用过 JSON Web 令牌的人来说，这是一个 URL 友好的、基于令牌的认证系统。它们允许您通过编码的 JSON 有效负载轻松地传输信息。

JSON Web 令牌的核心好处是双重的:您不需要使用会话或 cookies 来维护状态之间的认证；您不必经常调用数据库来获取用户信息，因为这些信息可以存储在令牌有效负载中。

每个令牌分为三个部分，每个部分由一个点分隔。

*   **报头:**包含令牌类型的信息，通常是 JWT，以及使用的散列算法，如 HMAC SHA256 或 RSA。
*   **Payload:** 包含您希望传输的关于用户的任何信息，例如用户标识符。
*   **签名:**这保证了令牌的安全，它是编码头和有效载荷以及秘密的散列。

```
// Token structure
header.payload.signature

// A real world token
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ 
```

JWT 安全是通过签名实现的，签名是通过散列编码的报头和有效载荷并用只有作者知道的秘密来保护它而创建的。

当从用户接收到令牌时，作者将能够通过用已知秘密重新散列接收到的报头和有效载荷并检查它是否与接收到的签名匹配来验证签名。如果有人篡改报头或有效载荷，签名将会不匹配，认证将会失败。

如果您希望快速开始使用 JWT， [ReallySimpleJWT](https://github.com/RobDWaller/ReallySimpleJWT) 库提供了一个易于使用的接口来生成和验证 JSON Web 令牌。

```
use ReallySimpleJWT\Token;

// Generate a token
$token = Token::getToken('userIdentifier', 'secret', 'tokenExpiryDateTimeString', 'issuerIdentifier');

// Validate the token
$result = Token::validate($token, 'secret'); 
```

如果您需要在一个简单的 API 上快速实现用户身份验证，它是完美的。如果你想[阅读文档](https://github.com/RobDWaller/ReallySimpleJWT/blob/master/readme.md)，这个库还提供了更高级的用法和功能。

## 如何在 PHP 中构建 JSON Web 令牌

如果你想建立自己的 JWT 发生器，或者只是想多了解一点，下面的指南会有所帮助。虽然下面的例子是用 PHP 编写的，但是这些概念适用于任何语言，所以所有开发人员都会发现它们很有用。完整的脚本在本指南的底部。

### 创建报头和有效载荷

首先，我们需要创建头和有效负载 JSON 字符串。我们将基于两个简单的数组来实现这一点，每个数组断言关于令牌的许多声明。你可以在相关的 [RFC](https://tools.ietf.org/html/rfc7519#section-4) 中阅读更多关于索赔的信息。对于头部，我们定义了类型`typ`和算法`alg`声明，它们是 RFC 标准声明；对于有效载荷，我们将创建自己的声明`user_id`。

```
// Create token header as a JSON string
$header = json_encode(['typ' => 'JWT', 'alg' => 'HS256']);

// Create token payload as a JSON string
$payload = json_encode(['user_id' => 123]); 
```

### 创建 Base64Url 头和有效载荷字符串

接下来，我们将我们的`$header`和`$payload` JSON 字符串编码为 Base64Url 字符串。这与标准的 Base64 字符串略有不同，而且 PHP 还没有内置 Base64Url 方法。所以我们必须做一些字符串替换魔法，将`+`替换为`-`，将`/`替换为`_`，将`=`替换为`''`。这使得 Base64 字符串在没有任何 URL 编码的情况下在 URL 内传递。

```
// Encode Header to Base64Url String
$base64UrlHeader = str_replace(['+', '/', '='], ['-', '_', ''], base64_encode($header));

// Encode Payload to Base64Url String
$base64UrlPayload = str_replace(['+', '/', '='], ['-', '_', ''], base64_encode($payload)); 
```

### 创建签名

为了创建签名，我们需要使用 PHP 中可用的`hash_hmac()`方法，并使用 sha256 算法。我们传入 Base64Url 编码的头和有效载荷`$base64UrlHeader . "." . $base64UrlPayload`的连接字符串。需要注意的是，我们必须在两个字符串之间包含点`.`。我们添加了一个秘密，最好是长度超过 12 个字符的强有力的秘密。ReallySimpleJWT 库实施了这个原则，但是对于我们的例子，我们不需要担心。最后，我们强制`hash_hmac()`方法以二进制数据的形式返回输出。

```
// Create Signature Hash
$signature = hash_hmac('sha256', $base64UrlHeader . "." . $base64UrlPayload, 'abC123!', true); 
```

### Base64Url 编码签名

一旦我们创建了签名，我们只需要像处理头和有效载荷一样，对它进行 Base64Url 编码。

```
// Encode Signature to Base64Url String
$base64UrlSignature = str_replace(['+', '/', '='], ['-', '_', ''], base64_encode($signature)); 
```

### 创建 JSON Web 令牌

最后，我们通过连接报头`$base64UrlHeader`、有效载荷`$base64UrlPayload`和签名`$base64UrlSignature`来创建 JWT。JWT 的每一部分都被一个点隔开。

```
// Create JWT
$jwt = $base64UrlHeader . "." . $base64UrlPayload . "." . $base64UrlSignature;

// Output
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxMjN9.NYlecdiqVuRg0XkWvjFvpLvglmfR1ZT7f8HeDDEoSx8 
```

就这样，非常简单。你可以在 [JWT.io 网站](https://jwt.io)上测试这段代码产生的 JWT。下面是完整的代码，我建议你阅读 JWT 网站上的相关[文档以及](https://jwt.io/introduction/) [RFC](https://tools.ietf.org/html/rfc7519) 。

如果你愿意，你当然可以使用 [ReallySimpleJWT 库](https://github.com/RobDWaller/ReallySimpleJWT),我将在接下来的一两周内发布一篇关于验证 JWT 的帖子。如果你有任何想法或注意到任何错误，请在 Twitter 上给我发消息 [@RobDWaller](https://twitter.com/RobDWaller) 。

### 脚本

```
// Create token header as a JSON string
$header = json_encode(['typ' => 'JWT', 'alg' => 'HS256']);

// Create token payload as a JSON string
$payload = json_encode(['user_id' => 123]);

// Encode Header to Base64Url String
$base64UrlHeader = str_replace(['+', '/', '='], ['-', '_', ''], base64_encode($header));

// Encode Payload to Base64Url String
$base64UrlPayload = str_replace(['+', '/', '='], ['-', '_', ''], base64_encode($payload));

// Create Signature Hash
$signature = hash_hmac('sha256', $base64UrlHeader . "." . $base64UrlPayload, 'abC123!', true);

// Encode Signature to Base64Url String
$base64UrlSignature = str_replace(['+', '/', '='], ['-', '_', ''], base64_encode($signature));

// Create JWT
$jwt = $base64UrlHeader . "." . $base64UrlPayload . "." . $base64UrlSignature;

echo $jwt; 
```