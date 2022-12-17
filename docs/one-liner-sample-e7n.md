# 一个衬垫样品

> 原文：<https://dev.to/teckl/one-liner-sample-e7n>

*   顶级 IP (awk)

```
$ cat logs/access_log  | awk {'print $1'} | sort | uniq -c | sort -nr | head 
```

Enter fullscreen mode Exit fullscreen mode

*   顶级 IP (perl)

```
$ cat logs/access_log  | perl -lane 'print $F[0]' | sort | uniq -c | sort -nr | head 
```

Enter fullscreen mode Exit fullscreen mode

*   顶级用户代理(awk)

```
$ cat logs/access_log  | awk -F '"' {'print $6'} | sort | uniq -c | sort -nr | head 
```

Enter fullscreen mode Exit fullscreen mode

*   顶级用户代理(perl)

```
$ cat logs/access_log  | perl -F'"' -lane 'print $F[5]' | sort | uniq -c | sort -nr | head 
```

Enter fullscreen mode Exit fullscreen mode

*   分钟访问计数

```
$ cat logs/access_log  | perl -lne 'm{2017:(\d+):(\d+)} and print "$1:$2"' | sort | uniq -c | head 6559 05:06
   5642 05:07
   5774 05:08
   5592 05:09
   4010 05:10 
```

Enter fullscreen mode Exit fullscreen mode

*   `%G`格式问题

```
$ perl -MTime::Piece -E 'say Time::Piece->strptime("2014-12-30", "%Y-%m-%d")->strftime("%Y")'
2014
$ perl -MTime::Piece -E 'say Time::Piece->strptime("2014-12-30", "%Y-%m-%d")->strftime("%G")'
2015
$ perl -MTime::Piece -E 'say Time::Piece->strptime("2014-12-28", "%Y-%m-%d")->strftime("%G")'
2014 
```

Enter fullscreen mode Exit fullscreen mode

*   objdump 版本

```
$ objdump -s peco | perl -lanE 'm{(go/?\d.+)} and say $1' | head -1
go/1.3/libexec 
```

Enter fullscreen mode Exit fullscreen mode

*   有魔力的俏皮话([http://search.cpan.org/dist/Mojolicious/lib/ojo.pm](http://search.cpan.org/dist/Mojolicious/lib/ojo.pm))

```
$ perl -Mojo -E 'say g("abehiroshi.la.coocan.jp")->dom->at("title")->text' | nkf -w
阿部寛のホームページ

$ perl -Mojo -E 'say g("abehiroshi.la.coocan.jp/top.htm")->dom("td")->[4]->text' | nkf -w
生年月日 1964 年 6 月 22 日 
```

Enter fullscreen mode Exit fullscreen mode

这是第一次测试。

这是一个测试