# 提高网站安全性的四个技巧

> 原文：<https://dev.to/restoreddev/four-tips-for-a-more-secure-website-52ip>

安全性是 web 开发中的一个热门话题，这是有道理的。每隔几个月就会有一个主要网站被破解，数百万条用户记录被泄露。很多时候，违规的原因是被忽视的简单漏洞。这里有一些提示，可以让你快速了解使你的网站更加安全的标准技术。注意:如果你遵循了这些建议，我不能保证网站的安全，在这篇文章中，我甚至没有涉及到安全的许多方面。这篇文章是为了提高对用于纠正 web 应用程序中出现的一些常见漏洞的技术的认识。

## 1。参数对你的健康有益

据 OWASP 称，web 应用程序的最大漏洞是 SQL 注入。什么是 SQL 注入？它是嵌入到 SQL 查询中的用户提供的数据，没有任何保护。这里有一个 PHP 中的例子:

```
<?php

$connection = new PDO(...);
$username = $_POST['username'];
$query = "SELECT username, password FROM users WHERE username = '$username'";
$connection->query($query); 
```

Enter fullscreen mode Exit fullscreen mode

您的下一个问题可能是，代码有什么问题？这是用户名变量在查询变量中的使用方式。用户名来自不可信的来源，可能被黑客操纵来更改查询。如果用户名的公布值实际上是`jdoe’ AND 1=1`会怎样？因为我们在查询字符串中直接使用了`$username`变量，所以查询会变成`SELECT username, password FROM users WHERE username = ‘jdoe’ AND 1=1`。恶意的一方可能会*向你的数据库中注入*任何种类的查询，泄露私人数据。

那么防止这种情况的方法是什么呢？数据库供应商创建了一个名为参数化的解决方案。它允许程序员向数据库发送查询，然后在以后提供数据。数据库将初始查询存储为模板，然后将数据绑定到模板后执行。由于数据是独立的，因此不会作为查询的一部分进行评估。下面是上面纠正的例子:

```
<?php

$connection = new PDO(...);
$username = $_POST['username'];

$query = "SELECT username, password FROM users WHERE username = :username"
$statement = $connection->prepare($query);
$statement->execute(['username' => $username]); 
```

Enter fullscreen mode Exit fullscreen mode

如果黑客提交`jdoe’ AND 1=1`作为用户名，那么文本将只是作为比较的一部分被评估，而不是作为查询被执行。参数化查询的工作方式如下:`SELECT username, password FROM users WHERE username = '\'jdoe\' AND 1=1'`。

我建议对任何查询使用参数化查询，不管它是否使用不可信数据。在许多情况下，它还可以让您的查询运行得更快。大多数流行的 ORM 都使用参数化，所以为你的团队选择一个好的 ORM 可以省去你很多头疼的事情。

## 2。逃跑并不像听起来那么难

下一个最受欢迎的网站漏洞被称为跨站脚本或 XSS。如果网站易受 XSS 攻击，则用户提供的 JavaScript 可在该网站上执行。在典型的 web 应用程序中，您只希望您网站的 JavaScript 在浏览器上执行。但是，如果不小心，恶意 JavaScript 可能会从用户通过参数或表单提交的数据中运行。这里有一个例子:

```
<h1><?php echo $_GET['title']; ?></h1> 
```

Enter fullscreen mode Exit fullscreen mode

在这个页面中，我们使用一个`title` GET 参数来显示页面的标题。因此，我们可以打开`/index.php?title=Hello`，看到单词`Hello`作为页面标题。很好，但是如果有人输入一些 JavaScript 作为标题呢？打开`/index.php?title=<script>alert('Hello world');</script>`会在页面上打印脚本并触发一个打开警报！XSS 的危险在于，黑客可能会使用恶意的 JavaScript 来欺骗用户，制造垃圾邮件，甚至窃取用户名和密码等表单数据。

防止 XSS 的最好方法是对页面输出中的任何 HTML 字符进行转义。“转义”将特殊字符转换为等效的 HTML 代码，防止字符被解释为 HTML。例如，字符`<`通常作为 HTML 标签的一部分在浏览器中被读取。然而，当它被转义后，它就变成了`&lt;`,告诉浏览器将`<`输出到页面，而不是作为 HTML 运行。现在，固定的例子:

```
<h1><?php echo htmlspecialchars($_GET['title'], ENT_QUOTES, 'UTF-8', false); ?></h1> 
```

Enter fullscreen mode Exit fullscreen mode

PHP 现在可以将任何特殊字符转换成等价的 HTML 实体，使得输出对于用户输入是安全的。服务器返回的文本看起来像:`<h1>&lt;script&gt;alert(&#039;Hello world&#039;);&lt;/script&gt;</h1>`。如您所见，所有的 HTML 标记都被转换了，阻止了脚本标记的运行。

在大多数现代框架中，实体转换作为模板化过程的一部分自动发生。Django 模板、 [Erubis](http://www.kuwata-lab.com/erubis/) 或 [Blade](https://laravel.com/docs/5.5/blade) 将为您处理转换，因此您不必担心。但是，如果您需要将用户数据直接打印到 HTML 页面上，最好意识到这个问题。

## 3。烹饪文本时不要忘记消毒

你的下一个问题可能是，“当我想在网页中显示用户提供的 HTML 时，我该怎么做？”。这是 CMS 网站上非常常见的场景。用户可能会用一堆 HTML 字符写一篇博客文章，用于格式化需要在页面上输出时保留的文本。但同时，我们不希望糟糕的 JavaScript 嵌入到博客文章中。解决方法是消毒。

净化是清理文本的过程，以删除/逃脱不良的 JavaScript，但留下良好的 HTML 标签。一种流行的 Python 杀毒软件叫做[漂白剂](https://bleach.readthedocs.io/en/latest/index.html)(由 Mozilla 创造)。这里是在使用:

```
import bleach

post_text = (
    "<h1>Hello</h1>\n"
    "<script>alert('Hello world');</script>"
)

clean_text = bleach.clean(
    post_text,
    tags=['h1']
)

print(clean_text)
# Outputs:
# <h1>Hello</h1>
# &lt;script&gt;alert('Hello world');&lt;/script&gt; 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们能够通过将`h1`标签作为一个可接受的标签传递给漂白剂来保留它，但是漂白剂也避开了脚本标签，使得我们的字符串免受 JavaScript 的攻击。这个字符串现在可以安全地输出到 HTML 页面上了。通常，在将数据存储到数据库中之前使用清理，因为这可能是一个比普通模板转义更慢的过程。

如果你需要接受 HTML 格式的文本，消毒是你最好的选择。每种语言都有很好的杀毒剂，包括 PHP 的 [HTML 净化器](http://htmlpurifier.org/)，Ruby 的[丝瓜](https://github.com/flavorjones/loofah)以及之前提到的 Python 的[漂白剂](https://bleach.readthedocs.io/en/latest/index.html)。

## 4。使用 CSRF 代币在更安全的水域冲浪

跨站点请求伪造(也称为`csurf`)是由一个网站代表当前用户向另一个网站提交恶意表单引起的。假设您有一个银行网络应用程序，用于向另一个账户转账。大概是这样:

```
from django.shortcuts import redirect
from .models import Transfer

# https://mybank.com/transfer/create def transfer_money(request):
    account_num = request.POST['account_num']
    money_total = request.POST['money_total']

    transfer = Transfer(account_num=account_num)
    transfer.complete(total=money_total)
      transfer.save()

    return redirect('transfer-index') 
```

Enter fullscreen mode Exit fullscreen mode

和一个提交到该路径的表单:

```
<form action="https://mybank.com/transfer/create" method="POST">
    <label>Account Number</label>
    <input type="text" name="account_num" />

    <label>Transfer Total</label>
    <input type="text" name="money_total" />

    <button type="submit">Submit</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

这个路径和表格允许银行用户通过提供目的地账号和要转账的总金额将资金发送到另一个账户。但是，如果不同的网站向这个 URL 提交表单，会发生什么情况呢？如果黑客创建了`mybankk.com/transfer`，并且该页面有一个提交给`mybank.com/transfer/create`的表单，该怎么办？大概是这样:

```
<form action="https://mybank.com/transfer/create" method="POST">
    <label>Account Number</label>
    <input type="text" name="account_num_fake" />
    <input type="hidden" name="account_num" value="10000001" />

    <label>Transfer Total</label>
    <input type="text" name="money_total" />

    <button type="submit">Submit</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

浏览器将阻止此表单请求，对吗？一点也不。如果提交表单的用户已经登录到`mybank.com`，表单将被`mybank.com`接受，没有问题。许多黑客使用这种技术来欺骗用户做出他们不想做的动作。在这种情况下，一个不知情的用户可能使用`mybankk.com/transfer`进行银行转账，但无意中转账到了账户# 10000001。由于黑客控制了账号，他可以把钱转到任何他想要的账户。

解决此漏洞的方法是使用表单标记。表单令牌是随机生成的字符串令牌，存储在用户的会话中。当您从网站生成表单时，您将把令牌作为隐藏字段嵌入表单中。然后，当提交表单时，您的 web 应用程序可以验证表单令牌是否与用户会话中的内容相匹配。

```
<!-- mybank.com transfer form -->
<form action="https://mybank.com/transfer/create" method="POST">
    <input type="hidden" name="csrfmiddlewaretoken" value="{{ csrf_token }}" />

    <label>Account Number</label>
    <input type="text" name="account_num" />

    <label>Transfer Total</label>
    <input type="text" name="money_total" />

    <button type="submit">Submit</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

如果`mybankk.com`提交它的表单，它将没有令牌或者令牌不正确，因此请求将被`mybank.com`拒绝。

大多数 web 框架都提供了 CSRF 令牌，但是这个特性在默认情况下并不总是开启的。在将应用程序投入生产之前，请始终验证 CSRF 令牌是否用于数据更改 HTTP 方法，如 POST、PATCH 和 DELETE。

## 结论

在 web 应用程序中维护适当的安全性是非常困难的。有大量的漏洞需要防范。我希望这篇文章能帮助您开始理解 web 安全中使用的一些技术。以下是在我的研究过程中对我有帮助的资源列表:

[类别:OWASP 十大项目-OWASP](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
[SQL 注入测试(OTG-InP val-005)-OWASP](https://www.owasp.org/index.php/Testing_for_SQL_Injection_(OTG-INPVAL-005))
[SQL 注入攻击实例](http://www.unixwiz.net/techtips/sql-injection.html)
[跨站脚本(XSS)-OWASP](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS))
[跨站请求伪造(CSRF) - OWASP](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))

如果你有任何安全提示，请在评论中留下！