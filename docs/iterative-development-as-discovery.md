# 作为发现的迭代开发

> 原文：<https://dev.to/circumlocutions/iterative-development-as-discovery>

在一堂关于 shell 脚本的课上，我的教授问管道是否比临时文件更好。我知道的还不够多，不能一概而论，但是 pipes 通常适合于这种不断发展的对问题的思考，在这种情况下，你不能完全确定答案是什么，因为你仍然在制定和重新制定问题。

比方说，我注意到在我的服务器上有几次失败的 SSH 尝试。他们从哪里来？要打印 IP 地址，我可以运行:

```
awk '/Failed password for/ {print $11}' /var/log/auth.log 
```

但是想象一下，这会返回一长串 IP 地址，其中一些是重复的，因为我还没有安装`fail2ban`。所以我用管道将它传递给`sort`来获取唯一的值。

```
awk '/Failed password for/ {print $11}' /var/log/auth.log | sort -u 
```

这给了我一个没有重复的列表。但是，也许我想知道这些 IP 地址在哪里，所以我对每个 IP 地址都进行了`whois`查找。

```
awk '/Failed password for/ {print $11}' /var/log/auth.log | sort -u \
    | while read address; do whois "$address" \
    | grep "Country: "; done 
```

我想这很好。但现在我想看看每个国家的尝试次数。所以我删除了`sort -u`，并对每个 IP 地址进行了`whois`查找，留下了所有的重复。

```
awk '/Failed password for/ {print $11}' /var/log/auth.log \
    | while read address; do whois "$address" \
    | grep -m 1 -i "Country: "; done 
```

(`grep`参数表示“仅匹配第一个实例，不区分大小写。某些`whois`信息在“Country”中使用小写字母“c”。)但是输出不是很有帮助。

```
Country:       US 
Country:       US 
country:       JP 
country:       JP 
country:       JP 
```

啊，我知道。我将在`while`循环中用实际的 IP 地址替换单词“country ”,然后通过管道将其传递给`awk`,以替换国家代码和 IP 地址的位置，甚至可能在它们之间添加一个 ASCII 箭头。

```
awk '/Failed password for/ {print $11}' /var/log/auth.log \
 | while read address; do whois "$address" \
     | grep -m 1 -i "Country: " \
     | sed "s/[Cc]ountry:/$address/"; done \
 | awk '{print $2 " --> "$1}' 
```

示例输出(使用随机编造的 IP):

```
JP --> 219.47.220.30 
JP --> 219.47.220.30 
NL --> 139.47.220.30 
US --> 149.47.31.192 
```

这几乎是我想知道的，除了我想统计重复 IP 地址的数量，然后根据尝试次数对它们进行排序，数字越大越好。我也不喜欢那个 ASCII 箭头。

```
awk '/Failed password for/ {print $11}' /var/log/auth.log \
 | while read address; do whois "$address" \
     | grep -m 1 -i "Country: "\
     | sed "s/[Cc]ountry:/$address/"; done \
 | awk '{print $2 " " $1}' \
 | uniq -c \
 | sort -r 
```

示例输出(使用随机编造的 IP):

```
5 JP 219.47.220.30 
4 KR 59.150.234.132 
1 US 99.62.218.217 
1 US 9.41.14.160 
```

至此，我找到了问题和答案。按尝试次数排序，失败的登录尝试来自哪些 IP 地址和国家？既然我已经回答了我的问题，我将把输出写到一个文件中，供以后分析和处理(如果有的话)。

管道将数据的处理与存储分开，类似于 HTML 和 CSS 将内容的结构和表示分开。在这个类比中，临时文件是数据处理的内联 CSS。

但是，当您仍然在等待代码回答的问题出现时，管道也很有用。

*本文原载于[媒体](https://medium.com/@circumlocutions/pipes-vs-temporary-files-55061a1141ad#.93y7oow4p)。*