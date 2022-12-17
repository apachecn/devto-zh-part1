# C# Int。ToString()。长度单位为 C

> 原文：<https://dev.to/adamkdean/c-int-tostring-length-in-c-4ii0>

这里还有一个小的帮助函数，它计算如果是一个字符串，int 的长度，就像计算 Int 的长度一样。ToString()。C#中的长度。

```
/*
 * File:   int_strlen.c
 * Author: Adam K Dean
 * Description: get length of an int as if it was a string
 *
 * Created on 11 January 2011, 13:01
 */

int int_strlen(int val)
{
    int v = val / 10;
    int i = 1, count = 1;

    while(v > i - 1)
    {
        i *= 10;
        count ++;
    }

    return count;
} 
```

Enter fullscreen mode Exit fullscreen mode