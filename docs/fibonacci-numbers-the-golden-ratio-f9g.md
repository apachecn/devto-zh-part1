# 斐波那契数&黄金分割率

> 原文：<https://dev.to/adamkdean/fibonacci-numbers-the-golden-ratio-f9g>

所以当我想到，嗯，也许我会用黄金分割率来设计，斐波那契螺旋等等，我正在做一些设计，但是要解决大量繁琐的工作..(必须使用铅笔..沉闷的...你看到的身体运动..)于是我写了一个剧本。我不太喜欢用 C#写脚本，因为它不是脚本，而是..有时你只是想要一些东西，比如你手中的油灰，来快速测试一个假设或诸如此类的东西，PHP 非常适合这种情况。大项目，ASP.NET 但是一个有点多才多艺的，虽然不能真正报告错误的非常好的脚本语言，PHP 是唐。

所以，斐波那契数列，很好也很简单:

```
<?php

$numbers[] = 0;
$numbers[] = 1;

$a = 0;
$b = 1;

for ($i = 0; $i < 10; $i++)
{
 $c = $a + $b;
 $a = $b;
 $b = $c;

 $numbers[] = $c;
}

// output
foreach($numbers as $n) echo "$n, ";

?> 
```

Enter fullscreen mode Exit fullscreen mode

然后是黄金比例，一个稍微大一点的剧本。

```
<?php

error_reporting(E_ALL);

$width = 1024;
$height = 768;

$phi = 0.6180339887;

$boxes[] = array($width, $height);

for($i = 1; $i < 10; $i++)
    {
    $w = round($boxes[$i - 1][0] * $phi, 4);
    $h = round($boxes[$i - 1][1] * $phi, 4);
    $boxes[] = array($w, $h);
}

echo "<pre>";
for($i = 0; $i < count($boxes); $i++)
{
    $box = $boxes[$i];
    $w = sprintf("%-10s", $box[0]);
    $h = sprintf("%-10s", $box[1]);

    echo "W: $w H: $h "; 
    if ($i > 0)
    {
        $pbox = $boxes[$i - 1];
        $tw = sprintf("%-10s", $box[0] + $pbox[0]);
        $th = sprintf("%-10s", $box[1] + $pbox[1]);
        echo "TW: $tw TH: $th";
    }
    echo "\r\n";
}
echo "</pre>";

?> 
```

Enter fullscreen mode Exit fullscreen mode

你会看到我提供了三个变量，初始的宽度，高度，然后是 phi，它们实际上不需要改变。这个脚本给了我们一个很好的输出，它计算出每个新框的宽度和高度，让您可以快速编码，而不是计算出来..单调乏味的工作..

```
W: 1024       H: 768        
W: 632.8668   H: 474.6501   TW: 1656.8668  TH: 1242.6501 
W: 391.1332   H: 293.3499   TW: 1024       TH: 768       
W: 241.7336   H: 181.3002   TW: 632.8668   TH: 474.6501  
W: 149.3996   H: 112.0497   TW: 391.1332   TH: 293.3499  
W: 92.334     H: 69.2505    TW: 241.7336   TH: 181.3002  
W: 57.0656    H: 42.7992    TW: 149.3996   TH: 112.0497  
W: 35.2685    H: 26.4514    TW: 92.3341    TH: 69.2506   
W: 21.7971    H: 16.3479    TW: 57.0656    TH: 42.7993   
W: 13.4713    H: 10.1036    TW: 35.2684    TH: 26.4515 
```

Enter fullscreen mode Exit fullscreen mode

简单，只需取你的新的宽度和高度(W & H)并检查值相加(TW & TH 相加当前和以前的宽度和高度)，你就设置好了。

享受吧。