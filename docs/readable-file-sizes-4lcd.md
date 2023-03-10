# 可读文件大小

> 原文：<https://dev.to/adamkdean/readable-file-sizes-4lcd>

为了用代码填满这个博客，我将在接下来的几周回顾我的片段，找到有用的方法并张贴出来。今天的特性是一个简单的文本格式化程序，它将一个 long(代表字节)转换成一个字符串，给你一个可读的文件大小。

所以事不宜迟:

```
private static string GetReadableBytes(long lSize)
{
    double size = lSize;
    if (size >= 1024 && size < 1048576)
        return string.Format("{0:#,0.00} KB", (size / 1024));

    else if (size >= 1048576 && size < 1073741824)
        return string.Format("{0:#,0.00} MB", (size / 1048576));

    else if (size >= 1073741824 && size < 1099511627776)
        return string.Format("{0:#,0.00} GB", (size / 1073741824));

    else if (size >= 1099511627776)
        return string.Format("{0:#,0.00} TB", (size / 1099511627776));

    else return string.Format("{0} B", size);
} 
```

Enter fullscreen mode Exit fullscreen mode

这里你可以看到输出。

```
/* Bytes                    Readable Size
 * =====                    =============
 * 436                  ->  436 B
 * 2159                 ->  2.11 KB
 * 36236                ->  35.39 KB
 * 2362893              ->  2.25 MB
 * 276227272            ->  263.43 MB
 * 62367298764          ->  58.08 GB
 * 3623647473473        ->  3.30 TB
 * 352635208736296      ->  320.72 TB
 * 236363536873629626   ->  214,971.38 TB
 **/ 
```

Enter fullscreen mode Exit fullscreen mode

尽情享受吧！