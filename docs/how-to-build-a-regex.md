# 如何构建正则表达式

> 原文：<https://dev.to/benaryorg/how-to-build-a-regex>

# 更新

我可能会在某个时候更新这篇文章，但是在[我自己的博客](https://benaryorg.github.io)。

# 如何构建一个正则表达式

我几乎每天都看到有人滥用正则表达式。
如果你真的擅长正则表达式，那么当你在不适当的地方看到`.*`或类似的构造时，你肯定会感到某种痛苦
。

所以这是我的正确做法指南。

## 备注

我将在所有地方使用 PCRE。
我的首选语法是`m{something}`和`s{a}{b}g`，所以我将坚持使用它们。

您可以使用
尝试所有示例

```
# for searches, just start typing, quit using ^D
perl -ne 'm{a(.)c} && print "$1\n"'
# for replacements
perl -pe 's{a.c}{abc}g' 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么不用`.*`？

你在大海捞针。一个实际的例子:你在房间里寻找你最喜欢的毛绒玩具。

一个很好的正则表达式应该是`m{\bplushie\b}`。
它会将你的单词作为一个单词来查找，这意味着只有在
的两边都以单词结尾时，它才会匹配。
亦见[字
界限](http://www.regular-expressions.info/wordboundaries.html)。

我看到人们所做的，完全荒谬的，是
`m{^.*\bplushie\b.*$}`。

让我解释一下:

*   即使不需要，它们也会匹配行首。
*   它们匹配线的末端。
*   他们让解析器检查所有的字符，即使他们已经找到了他们想要的。

如果你在某个地方寻找一个事件，那不需要在某个特定的地方，那么你为什么要寻找其他的东西呢？
你不会走进你的房间，开始依次从一边看向另一边
。
你要做的就是看看你的房间，看到床上放着的棉絮，然后拿走
。

## 复杂的例子

让我们选择 *fail2ban* 作为例子。
我们希望阻止每个 IP 每五秒钟发送超过 1000 个 HTTP 请求。
我将忽略如何配置 *fail2ban* ，因为它与 regex
无关。

第一次尝试，甚至不看日志的格式:`m{^.*<HOST>.*$}`

你可能在这里搞砸了。
为了解释你为什么搞得这么糟糕，让我们来看一行日志:

```
10.0.0.1 - - [16/Jul/2017:15:38:54 +0200] "GET /robots.txt HTTP/1.0" 404 319 "-" "Mozilla/5.0 (compatible; AhrefsBot/5.2; +http://ahrefs.com/robot/)" "-" 
```

Enter fullscreen mode Exit fullscreen mode

我把那行从我的服务器中取出来， *10.0.0.1* 是我们想要的部分
(请忽略它是一个内部 IP)。

`m{.*}`确实贪心匹配，所以上面的 regex 会非常容易破。
只需在用户代理中输入一个 IP 即可。
用户代理包含空格，您可能已经注意到了，这很好，因为
字符串被引用了(并且 *nginx* 对包含的字符串进行了一些转义)。
现在，如果我在用户代理中输入一个 IP 地址，会发生什么？
右，由于贪婪匹配最右边的`<HOST>`会匹配。这当然会导致一些严重的问题。

作为一名恶意黑客，我可以:

*   通过将`127.0.0.1`放入我的用户代理，完全避开*失败 2 禁止*。这将有效地关闭我的请求的*失败 2 禁止*，只要`127.0.0.1`被列入白名单。如果那个 IP 没有被列入白名单，你会遇到更糟糕的问题(假设 *fail2ban* 使用 *iptables* ，这将破坏至少一半的服务器软件，想想在 *localhost* 上访问 *MySQL* )。
*   以同样的方式阻止任意 IP 访问您的网站。

那么，我们如何构建一个正则表达式，只匹配我们在这里寻找的
的**？**

嗯，IP 在开始是正确的，所以我们将采取`m{^<HOST>.*$}`对吗？

技术上是对的，但我不会这样写。【regex 将再次匹配 IP 之后的所有内容，但是*我们真的不关心
关于那个*。

我们应该做的是一个简单的`m{^<HOST>}`。
这和预期的一样，它只查看前几个字符。
如果你想确定它后面有一个空格，请继续。

所以我们最终得到了一个简单的正则表达式:`m{^<HOST>\s}`。这肯定会满足我们所有的需求。

老实说，这个例子很简单，因为 IP 从一开始就是正确的。
让我们假设一些其他的格式，这样我们可以为这个
找到一个更通用的工作方式。

### 复制、粘贴、替换

我们将对一行文本进行 CPR。如上所述，这一次我们想要在
开始时得到*而不是*的东西。
我们来看看下面一行:

```
[16/Jul/2017:16:27:41 +0200] openbsd.cloud.bsocat.net - - 66.133.109.36 "GET /.well-known/acme-challenge/tTRnUGY9gZEVz2llGWqn1m3mHznMDOFH3zCXsgelh7w HTTP/1.1" 200 87 
```

Enter fullscreen mode Exit fullscreen mode

这是一个稍微修改过的 OpenBSD httpd 日志格式。
由*略*我的意思是:

*   日期和时间移到前面
*   主机向后移动了一点

现在让我们这样做:

```
# copy the line, verbatim
[16/Jul/2017:16:27:41 +0200] openbsd.cloud.bsocat.net - - 66.133.109.36 "GET /.well-known/acme-challenge/tTRnUGY9gZEVz2llGWqn1m3mHznMDOFH3zCXsgelh7w HTTP/1.1" 200 87

# remove everything after the needle (except for the delimiter), we don't need it
[16/Jul/2017:16:27:41 +0200] openbsd.cloud.bsocat.net - - 66.133.109.36\s

# add needed meta-characters (start of line)
^[16/Jul/2017:16:27:41 +0200] openbsd.cloud.bsocat.net - - 66.133.109.36\s

# escape all the characters that need escaping
^\[16/Jul/2017:16:27:41 \+0200\] openbsd\.cloud\.bsocat\.net - - 66\.133\.109\.36\s

# replace the host
^\[16/Jul/2017:16:27:41 \+0200\] openbsd\.cloud\.bsocat\.net - - <HOST>\s

# replace everything that is not static by their possible values
# this requires a lot of in depth knowledge about the log format
# let's do this the easy way and just replace using dots for the date
^\[../.../....:..:..:.. .....\] openbsd\.cloud\.bsocat\.net - - <HOST>\s

# for the other fields we just specify them to "not contain spaces" as these
# are used for delimiting, so they will not occur in the fields
^\[../.../....:..:..:.. .....\] [^\s]+ [^\s]+ [^\s]+ <HOST>\s

# fail2ban needs spaces escaped I think
^\[../.../....:..:..:..\s.....\]\s[^\s]+\s[^\s]+\s[^\s]+\s<HOST>\s 
```

Enter fullscreen mode Exit fullscreen mode

### 我们如何检查？

如果是 *fail2ban* ，就运行那个。
其他一切:

```
perl -ne 'm{^\[../.../....:..:..:..\s.....\]\s[^\s]+\s[^\s]+\s[^\s]+\s([^\s]+)\s} && print "$1\n"' 
```

Enter fullscreen mode Exit fullscreen mode

这应该只输出我们正在寻找的 IP。