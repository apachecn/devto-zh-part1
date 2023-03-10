# 重构函数

> 原文：<https://dev.to/sebastianr1982/refactor-of-a-function-5nk>

在开始之前，读者认为这是我的个人观点，我对事情的看法，而不是解决问题的唯一方法。

我正在开发一个验证器库，其中一个实现了 HTML 转义的过滤器(用正确的 HTML 实体替换特殊字符)。对于转义特殊字符，我需要知道 html 实体的名称或编号。不是所有的实体都有名字，那么使用这个数字是不合理的。

为了得到 char 的个数，PHP 提供了一个名为 ord()的函数。不幸的是，ord()不能处理 UTF 8 多字节字符，如果多字节函数不可用，就需要寻找另一种解决方案。

老实说，我不喜欢在过滤器上使用多字节函数，因为我不能假定传递给过滤器的所有字符串或字符都是多字节的。在这种情况下，防御性编程帮助了我们，我们认为所有通过的都可能是危险的。

在 order()函数文档的注释中，我找到了一小段代码，似乎可以解决我的问题。它返回所有多字节字符的字符数。

# 原始代码

这是函数的原始版本(它是五年前的代码)，存在于 PHP 文档注释中:

```
<?php

function ordutf8($string, &$offset)
{
    $code = ord(substr($string, $offset, 1));

    if ($code >= 128) {//otherwise 0xxxxxxx

        if ($code < 224)
            $bytesnumber = 2;//110xxxxx
        else if ($code < 240)
            $bytesnumber = 3;//1110xxxx
        else if ($code < 248)
            $bytesnumber = 4;//11110xxx

        $codetemp = $code - 192 - ($bytesnumber > 2 ? 32 : 0) - ($bytesnumber > 3 ? 16 : 0);

        for ($i = 2; $i <= $bytesnumber; $i++) {
            $offset ++;
            $code2 = ord(substr($string, $offset, 1)) - 128;//10xxxxxx
            $codetemp = $codetemp * 64 + $code2;
        }

        $code = $codetemp;
    }

    $offset += 1;

    if ($offset >= strlen($string))
        $offset = -1;

    return $code;
} 
```

Enter fullscreen mode Exit fullscreen mode

代码被认为是管理一个短语，而不是一次管理一个字符。在这个函数中，有两个 IF、五个 IF-ELSE 和一个 FOR 语句。太复杂了。

# 第一步

我们在 2018 年和 PHP7 (7.2 当前主要版本)提供了很多新功能，我最喜欢的是类型检查！接下来，我需要一次管理一个字符，不再需要偏移参数。

```
<?php

function ordutf8(string $char) : int
{
    $code = ord(substr($char, 0, 1));

    if ($code >= 128) {

        if ($code < 224)
            $bytesnumber = 2;
        else if ($code < 240)
            $bytesnumber = 3;
        else if ($code < 248)
            $bytesnumber = 4;

        $codetemp = $code - 192 - ($bytesnumber > 2 ? 32 : 0) - ($bytesnumber > 3 ? 16 : 0);

        $offset = 0;

        for ($i = 2; $i <= $bytesnumber; $i++) {
            $offset ++;
            $code2 = ord(substr($char, $offset, 1)) - 128;
            $codetemp = $codetemp * 64 + $code2;
        }

        $code = $codetemp;
    }

    return $code;
} 
```

Enter fullscreen mode Exit fullscreen mode

两个 IF 语句之一已被移除。

# 第二步

这段代码包含两个使用三元运算符编写的 IF-ELSE:

```
<?php

$codetemp = $code - 192 - ($bytesnumber > 2 ? 32 : 0) - ($bytesnumber > 3 ? 16 : 0); 
```

Enter fullscreen mode Exit fullscreen mode

我们用静态值替换它，直接提供正确的数字，而不用每次都检查它。

```
<?php

function ordutf8(string $char) : int
{
    $code = ord(substr($char, 0, 1));

    if ($code >= 128) {
        $count = 0;

        if ($code < 224) {
            $bytesnumber = 2;
        } else if ($code < 240) {
            $bytesnumber = 3;
            $count = 32;
        } else if ($code < 248) {
            $bytesnumber = 4;
            $count = 48;
        }

        $codetemp = $code - 192 - $count;

        $offset = 0;

        for ($i = 2; $i <= $bytesnumber; $i++) {
            $offset ++;
            $code2 = ord(substr($char, $offset, 1)) - 128;
            $codetemp = $codetemp * 64 + $code2;
        }

        $code = $codetemp;
    }

    return $code;
} 
```

Enter fullscreen mode Exit fullscreen mode

五个 IF-ELSE 语句中的两个被删除。

# 第三步

我们可以对语句进行重构，删除变量并减少代码行。

```
<?php

function ordutf8(string $char) : int
{
    $code = ord(substr($char, 0, 1));

    if ($code >= 128) {
        $count = 0;

        if ($code < 224) {
            $bytes = 2;
        } else if ($code < 240) {
            $bytes = 3;
            $count = 32;
        } else if ($code < 248) {
            $bytes = 4;
            $count = 48;
        }

        $temp = $code - 192 - $count;

        for ($i = 1; $i < $bytes; $i++) {
            $code = $temp = $temp * 64 + ord(substr($char, $i, 1)) - 128;
        }
    }

    return $code;
} 
```

Enter fullscreen mode Exit fullscreen mode

详情由此而来:

```
<?php

$codetemp = $code - 192 - $count;

$offset = 0;

for ($i = 2; $i <= $bytesnumber; $i++) {
    $offset ++;
    $code2 = ord(substr($char, $offset, 1)) - 128;
    $codetemp = $codetemp * 64 + $code2;
}

$code = $codetemp; 
```

Enter fullscreen mode Exit fullscreen mode

对此:

```
<?php

$temp = $code - 192 - $count;

for ($i = 1; $i < $bytes; $i++) {
    $code = $temp = $temp * 64 + ord(substr($char, $i, 1)) - 128;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 第四步

现在我们需要试着去掉一点 IF-ELSE 语句。这一步似乎比前一步更复杂，但是让我们思考一下 IF 语句中使用的条件。

我们现在有四个条件:

*   > =128 2 字节
*   < 224 2 字节
*   < 240 3 字节
*   < 248 4 字节

我们可以只用三种方法重写它们:

*   > 127 2 字节
*   > 223 3 字节
*   > 239 4 字节

不使用 IF-ELSE 语句，也不在下一个条件为真时覆盖变量。

```
<?php

function ordutf8(string $char) : int
{
    $code = ord(substr($char, 0, 1));

    if ($code > 127) {

        $bytes = 2; 
        $count = 0;

        if ($code > 223){
            $bytes = 3; 
            $count = 32;
        }

        if ($code > 239){
            $bytes = 4; 
            $count = 48;
        }

        $temp = $code - 192 - $count;

        for ($i = 1; $i < $bytes; $i++) {
            $code = $temp = $temp * 64 + ord(substr($char, $i, 1)) - 128;
        }
    }

    return $code;
} 
```

Enter fullscreen mode Exit fullscreen mode

五个 IF-ELSE 语句中的五个被删除。保留 3 个 IF 和 1 个 FOR 语句。

# 第五步

我们现在试着把 FOR 语句去掉，全部重写为数学公式。

```
<?php

function ordutf8(string $char) : int
{
    $code = ord(substr($char, 0, 1));

    if ($code > 239){
        return ((/*($code - 240) * 64 + */ord(substr($char, 1, 1)) - 128) * 
                64 + ord(substr($char, 2, 1)) - 128) * 
                64 + ord(substr($char, 3, 1)) - 128;
    }

    if ($code > 223){
        return (($code - 224) * 64 + ord(substr($char, 1, 1)) - 128)
                * 64 + ord(substr($char, 2, 1)) - 128;
    }

    if ($code > 127) {
        return ($code - 192) * 64 + ord(substr($char, 1, 1)) - 128;
    }

    return $code;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在只剩下三个 IF 语句。
更新:第一个 IF 语句中的注释代码总是返回零。无用的代码。

# 结论

我希望这篇文章可以帮助您理解，您不必在代码运行的那一刻就停下来。代码总是可以改进、简化和变得更有效。

在[https://3v4l.org/TWFLX](https://3v4l.org/TWFLX)上运行一点测试，就能体会到代码的改进。

我的[要点](https://gist.github.com/s3b4stian/2e31fe85b45f03eb1a016fd74e12f336)上也有代码