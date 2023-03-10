# 位掩码

> 原文：<https://dev.to/theopensourceu/bit-masks-1omp>

[![Bit Masks](img/164282137bd921a1a8205c9f9d2f91ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Pbiw021T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theopensourceu.org/conteimg/2017/08/binary-code-475664_1280.jpg)

位掩码是一个计算机科学概念；简而言之，它们代表了可以以多种方式组合的选项。

一个非常基本的颜色示例。蓝色和绿色合成黄色。这可以用*位屏蔽*来表示。

下面的例子来源于维基百科上的[位域文章；它很好地解释了这个概念。](https://en.wikipedia.org/wiki/Bit_field)

考虑下面的并注意注释中的二进制版本。

```
// primary colors
#define BLUE 4 /* 100 */
#define GREEN 2 /* 010 */
#define RED 1 /* 001 */ 
```

这将创建基本选项。下一个例子使用上面的方法进一步定义了更多的颜色，而没有给它们分配特定的数字，尽管它们会以数字的形式输出。

```
// mixed colors
#define BLACK 0 /* 000 */
#define YELLOW (RED | GREEN) /* 011 */
#define MAGENTA (RED | BLUE) /* 101 */
#define CYAN (GREEN | BLUE) /* 110 */
#define WHITE (RED | GREEN | BLUE) /* 111 */ 
```

注意`WHITE`是所有的颜色-所有的位都是“开”的，黑色是没有颜色的-所有的位都是关的。

虽然我必须承认，这种用法我见多了，但我不像以前那样经常碰到它了。放心，现在还在用，还挺有用的。

* * *

1.  给出的代码是伪代码。它并不完整。 [↩︎](#fnref1)