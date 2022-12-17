# 在 Java 中查找 Null 或空字符串检查

> 原文：<https://dev.to/monknomo/finding-null-or-empty-string-checks-in-java>

我有很多这样的代码:

```
if( null == myString || "".equals(myString))
    doAThing(); 
```

Enter fullscreen mode Exit fullscreen mode

代码很好，真的。很惯用，完成任务。

最近，我一直对谓词感兴趣。我喜欢用描述条件语句意图的谓词来代替我的条件语句。我还可以用几个常见的谓词替换我的许多样板代码。这是我经常使用的一个:

```
 public static final Predicate<String> NULL_OR_EMPTY = (in) -> null==in || "".equals(in); 
```

Enter fullscreen mode Exit fullscreen mode

是真的；如果代码库是用可选的或者其他的空避免技术制作的，我可以忽略这个检查。我的代码库有历史、权重和外部接口，所以有很多这样的检查和其他类似的检查。

我一直在使用这个正则表达式来寻找可以应用我的谓词的地方:

```
 /\(\s*null\s*==\s*(\S*)\s*\|\|\s*""\.equals\(\s*\1\s*\)\s*\)|\(\s*(\S*)\s*==\s*null\s*\|\|\s*""\.equals\(\s*\2\s*\)\s*\)|\(\s*null\s*==\s*(\S*)\s*\|\|\s*\3\.equals\(\s*""\s*\)\s*\)|\(\s*(\S*)\s*==\s*null\s*\|\|\s*\4\.equals\(\s*""\s*\)\s*\)|\(\s*""\.equals\(\s*(\S*)\s*\)\s*\|\|\s*null\s*==\s*\5\s*\)|\(\s*(\S*)\.equals\(\s*""\s*\)\s*\|\|\s*null\s*==\s*\6\s*\)|\(\s*""\.equals\(\s*(\S*)\s*\)\s*\|\|\s\7\s*==\s*null\s*\)|\(\s*(\S*).equals\(\s*""\s*\)\s*\|\|\s*\8\s*==\s*null\s*\)|\(\s*null\s*==\s*(\S*)\s*\|\|\s*""\s*==\s*\9\s*\)|\(\s*(\S*)\s*==\s*null\s*\|\|\s*""\s*==\s*\10\s*\)|\(\s*null\s*==\s*(\S*)\s*\|\|\s*\11\s*==\s*""\s*\)|\(\s*(\S*)\s*==\s*null\s*\|\|\s*\12\s*==\s*""\s*\)|\(\s*""\s*==\s*(\S*)\s*\|\|\s*null\s*==\s*\13\s*\)|\(\s*(\S*)\s*==\s*""\s*\|\|\s*null\s*==\s*\14\s*\)|\(\s*""\s*==\s*(\S*)\s*\|\|\s*\15\s*==\s*null\s*\)|\(\s*(\S*)\s*==\s*""\s*\|\|\s*\16\s*==\s*null\s*\)/g 
```

Enter fullscreen mode Exit fullscreen mode

这是一个丑陋的例子，但是它符合很多检查字符串是否为 null 或空的常见模式。我希望人们能建议一种更好的方法来查找 null 或空支票，或者帮我添加到我的可怕的正则表达式中！

[每月收到一封电子邮件，其中包含来自网络的优秀技术和技术领导文章](http://www.gunnargissel.com/pages/email-signup-1.html)

*[感谢 Jan Tik 为头像](https://flic.kr/p/7Jth8)*

关注我的博客吧！