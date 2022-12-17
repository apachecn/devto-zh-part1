# 一切都在于时机:对假人的时机攻击

> 原文：<https://dev.to/yechielk/its-all-in-the-timing-timing-attacks-for-dummies-cd6>

## 对定时攻击以及如何保护自己的(过度)简化解释

作为一名对安全性感兴趣的开发人员；每隔一段时间，我就会了解到一个新的漏洞，它如此阴险，如此聪明，以至于让我毛骨悚然。今天是时间攻击。

这篇文章过分简化了许多概念。如果安全性是你的应用程序的一个问题(如果你处理客户信息，那么它应该是！)，请自学不同安全漏洞和攻击媒介的含义。
您可以在以下链接中了解更多关于定时攻击的信息:

[https://thisdata . com/blog/timing-attacks-against-string-comparison/](https://thisdata.com/blog/timing-attacks-against-string-comparison/)

假设您有一个客户端可以用来获取信息的 API。为了确保信息只发送给预定方，您需要您的客户端使用 API 密钥进行身份验证，API 密钥是一个随机字符串，它们随每个请求一起发送。当一个请求进来时，你将他们发送的 API 密匙与你的文件中的密匙进行比较，如果密匙匹配，你就知道客户就是他们所说的那个人。

对于有耐心的黑客来说，定时攻击是一种聪明的变通方法，可以在没有密钥的情况下获取这些私人信息。他们是怎么做到的？运用数学和统计学，以及一些精确的测量。

你看，比较发送的 API 密钥和文件上的 API 密钥的函数的核心是用于比较这两个字符串的算法。该算法通常看起来像这样(用伪代码):

> 浏览发送的字符串，检查第一个字符，如果它与文件中字符串的第一个字符匹配，检查下一个字符，如果匹配，继续下一个字符，等等。直到你看到最后一个字符。如果最后一个字符匹配，则返回“真”，如果检查的任何字符不匹配，则返回“假”。

漏洞在最后一行。假设 API 密钥有 15 个字符长，攻击者将使用类似于`a00000000000000`、`b00000000000000`、`c00000000000000`等几百个字符串发送几百个请求。每种发送几百个。然后他们会非常仔细地分析服务器拒绝他们的请求需要多长时间。使用统计数据，他们会发现他们的字符串的一个版本平均只需要几毫秒的时间来拒绝。略微延迟的原因很简单；大多数字符串在第一个字符被比较时就被拒绝，而一个字符串(假设是`g00000000000000`)通过了第一个字母，直到第二个字母时才被拒绝。黑客们现在知道 API 密匙的第一个字母是字母`g`。

黑客们现在将使用字符串`ga0000000000000`、`gb0000000000000`、`gc0000000000000`等重复这个过程。直到他们找到钥匙上的下一个字母。他们将不断重复这一过程，直到他们弄清楚 API 密钥的所有 15 个字符，攻击者现在可以冒充合法的客户端并获得对该客户端可用的任何信息的访问。

绕过这种攻击的方法相对简单；将检查字符串是否相等的算法替换为无论哪个字符不匹配都花费相同时间的算法。

一个实现(用伪代码)如下:

```
let indicator = 0

for (each char in the string) {
   if (char is not equal to the corresponding char in the string on file) {
     change the value of indicator by one
   }
}

return true if the value of indicator is 0 //i.e. it was never changed due to a mismatched character

otherwise return false 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，上面的函数每次都遍历整个字符串，最后只返回 true 或 false。因此，不管传入的是什么字符串，都需要相同的时间。

对于那些使用 Rails 的人来说，ActiveRecord 实际上有一个内置的方法来实现这个功能:
`ActiveSupport::SecurityUtils.secure_compare(string1, string2)`
这个方法结合了上面的功能来在常量时间内比较两个字符串。

这篇文章是从我的博客[代码 Hassid](https://blog.yechiel.me/all-about-timing-attacks-f71238a13936) 交叉发布的。
你可以在那里了解更多关于我的编程之旅，或者在 Twitter 上关注我 [@yechielk](https://twitter.com/yechielk)