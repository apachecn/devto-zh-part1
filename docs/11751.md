# 在 C 语言中复制 c# params 关键字(GNU-C99)

> 原文：<https://dev.to/adamkdean/replicating-c-params-keyword-in-c-gnu-c99-l2j>

就像朱利安·阿桑奇从一个国家跳到另一个国家，从一个妓院跳到另一个妓院，像一个异教徒一样被追捕，我的正常运行时间项目让我从一个工作换到另一个工作，编程到网页设计到服务器管理到 flash 设计..flash 设计还没有出现，我不期待那一天的到来！

在过去的 5 年里，我主要是用 C#编写代码，在大多数网站上使用 ASP.NET。我确实学过一些 PHP，但不多，我花了一周时间读了一本 C 语言书(K & R ),但还是把它放下了。所以当我决定用 C 重写我的节点服务器时，这不是一个小任务。

我做得很好，进展顺利，我很容易就学会了。下面是一些在 linux 上工作的 C 代码(我运行的是 CentOS，用 GNU-C99 编译)，它复制了可爱的 C# params 关键字的动作，允许你给一个函数传递多个参数。我将用它来创建一个字符串。替换复制。注意我没有使用 C++，尽管它有这些...我不喜欢语法变化，太多冒号。

```
#include <stdarg.h>

void *passparams(int argc, char *args, ...)
{
    va_list marker;
    va_start(marker, *args);

    for(int i = 0; i < argc; i++)
    {
        printf("arg: %s\n", args);
        args = va_arg(marker, char *);
    }

    va_end(marker);
} 
```

Enter fullscreen mode Exit fullscreen mode

我不会多做解释，只是说在使用 va_list/va_arg 时要确保有某种终止符，否则会得到 segfaults。有些人有像{ 1，2，3，4，-1 }这样的 int 列表，但它很难看，我更喜欢一个参数计数列表。

享受