# 求职申请代码挑战-一个 PHP 解决方案

> 原文：<https://dev.to/ripsup/job-application-code-challenge---a-php-solution-e3i>

今天早些时候，我读了本·格林伯格的 T2 求职挑战，觉得这看起来是一个有趣的小挑战。我找到了我的 PHP 语言“go to ”,很快就把它删除了，并注意到提供的测试字符串有一个错误。

我没有想到分享我的结果，直到我看到另一个由 Ryan Palo 写的帖子，他分享了他对这个问题的 Ruby 解决方案。这是我对这个问题的 PHP 解决方案。

## 挑战

挑战是这样的(复制自原帖):

> 对以下字符串中的字符进行排序:
> abcdefghijklmnopqrstuvwxyz_
> 
> 按字符在以下文本中出现的次数(降序):
> 
> …[为简洁起见，省略了字符串]
> 
> 现在获取排序后的字符串，并删除所有在 _ 后面的字符。剩下的一个字就是答案。

您可以在这里的获得示例字符串来运行您的代码。

## 我的解决方案

首先，我发现需要一个数组来记录所有字符。PHP 的一个好处是字符串已经是一个字符数组了，所以我不需要对它做任何转换。为此，我只是通过一个 for 循环来运行它，遍历整个数组并对每个数组进行计数。

```
<?php
$charCount = array();

for ($i=0; $i<strlen($longString); $i++) {
  if (!isset($charCount[$longString[$i]])) {
    $charCount[$longString[$i]] = 0;
  }
  $charCount[$longString[$i]]++;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我有了一个数组，它的键是字符，值包含计数，我们需要对数组进行排序。这是通过 arsort()简单完成的。

```
<?php
arsort($charCount);
?> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要使用 implode()将数组中的所有项目连接成一个字符串。然后我突然想到我需要为键而不是数组的值做这件事，所以我结合了内爆()和 array_keys()来完成这件事。

```
<?php
$sortedString = implode(array_keys($charCount));
?> 
```

Enter fullscreen mode Exit fullscreen mode

最后一步是删除包括“_”在内的所有内容，所以我使用了 explode()，因为这将返回一个字符串数组，所以只返回该数组中的第一个字符串，这将是我们要查找的单词。

## 最终解决

```
<?php
$charCount = array();

for ($i=0; $i<strlen($longString); $i++) {
  if (!isset($charCount[$longString[$i]])) {
    $charCount[$longString[$i]] = 0;
  }
  $charCount[$longString[$i]]++;
}

arsort($charCount);
$sortedString = implode(array_keys($charCount));
$theWord = explode("_",$sortedString)[0];
?> 
```

Enter fullscreen mode Exit fullscreen mode

我可以想到其他方法(比如使用类似于 Ben 的 JS 解决方案的 reduce)来解决这个问题，但这是我想到的第一个方法。如果你有一个你更喜欢的不同版本，请在下面分享(以及为什么),因为我很想看。

## 别解

当我点击 publish 的时候，我想“我敢打赌有一个 PHP 函数可以代替 for 循环使用，这样会更简洁”, 5 秒钟后我就有了它——array _ count _ values()

我在尝试使用这个函数时遇到的一个问题是，它会将$longString 视为一个字符串，而不是一个数组。为了解决这个问题，我把它和 str_split()结合起来，现在我们有了下面这个更简单的解决方案。

```
<?php
$charCount = array_count_values(str_split($longString));

arsort($charCount);
$sortedString = implode(array_keys($charCount));
$theWord = explode("_",$sortedString)[0];
?> 
```

Enter fullscreen mode Exit fullscreen mode

### 性能提升

在大多数情况下，当使用内置的 PHP 函数而不是自己编写函数时，这样做可以提高性能。

我拼凑了一个快速测试脚本，每个版本都运行了 1000 次，第二个解决方案的速度是第一个的两倍多。所以只要有可能，就用 PHP 给你的。:)