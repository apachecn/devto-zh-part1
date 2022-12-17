# 正则表达式匹配带有前缀的所有单词

> 原文：<https://dev.to/adamkdean/regex-match-all-words-with-a-prefix-1b05>

下面是匹配以指定前缀开头的所有单词的代码片段。

```
/\bprefix\S+/g 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 实现:

```
"test tbl_test1 tbl_test2 test".match(/\btbl_\S+/g) 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
/\btbl_\S+/g.exec("test tbl_test1 tbl_test2 test") 
```

Enter fullscreen mode Exit fullscreen mode

这与以下内容相同:

```
var regex = /\btbl_\S+/g;
    matches = [],
    match;

while (match = regex.exec(line)) {
    matches.push(match[0]);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果需要动态前缀，请使用 RegExp:

```
var regex = new RegExp('\\b' + prefix + '\\S+', 'g'),
    matches = [],
    match;

while (match = regex.exec(line)) {
    matches.push(match[0]);
} 
```

Enter fullscreen mode Exit fullscreen mode