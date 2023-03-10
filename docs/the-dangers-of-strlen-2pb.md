# strlen 的危险

> 原文：<https://dev.to/michaelmior/the-dangers-of-strlen-2pb>

我会保持这篇文章的简短，但是我想分享一些我刚刚发现的关于 C `strlen`函数的东西。很慢。它可以大大缩短程序执行时间。

例如，我在一个外部函数中使用了一个字符串，所以我每次在循环中调用`strdup`来确保我在下一次迭代中有一个副本。

在我的例子中，我调用的函数已经将字符串的长度作为参数传入。这意味着`s2 = strdup(s1);`可以重写为

```
s2 = (char*) malloc(length);
memcpy(s2, s1, length); 
```

Enter fullscreen mode Exit fullscreen mode

注意，我使用了 memcpy 而不是 strcpy。因为我们已经知道了字符串的长度，所以这也避免了 strcpy 中的循环，这是查找字符串结尾所必需的。这些类型的优化可能看起来像是吹毛求疵，但是它们可以在你的代码的整体运行时产生很大的不同。例如，通过上面显示的优化，我能够将特定循环的运行时间减少 15%左右。

还知道其他人们可能没有意识到的有性能问题的常见函数吗？在评论中分享你的内幕消息吧！

旁白:在研究 strlen 的性能时，我偶然发现了这个有趣的论坛帖子，它提供了一个来自 Knuth 的高效 strlen 实现。

## 更新时间:2012 年 6 月 14 日

GCC 4.7.1 有一个优化来解决这个问题。请参见发行说明中的以下片段。

> 增加了一个字符串长度优化通道…该通道可以优化
> 
> ```
> char *bar (const char *a)
> {
> size_t l = strlen (a) + 2;
> char *p = malloc (l); if (p == NULL) return p;
> strcpy (p, a); strcat (p, "/"); return p;
> } 
> ```
> 
> 变成:
> 
> ```
> char *bar (const char *a)
> {
> size_t tmp = strlen (a);
> char *p = malloc (tmp + 2); if (p == NULL) return p;
> memcpy (p, a, tmp); memcpy (p + tmp, "/", 2); return p;
> } 
> ```