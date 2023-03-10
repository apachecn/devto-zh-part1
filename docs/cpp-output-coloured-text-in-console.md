# CPP 在控制台中输出彩色文本

> 原文：<https://dev.to/deebuls/cpp-output-coloured-text-in-console>

# 彩色调试输出

Print 语句是任何编程中最广泛使用的代码调试方法。为调试准备好打印语句的方法包总是有用的。

方法之一是在屏幕上给输出着色。彩色调试语句比普通的白色语句有效得多。例如，绿色表示肯定陈述，红色表示错误陈述。

在 cpp 中，有很多做事的方法。我一直喜欢亲吻(保持简单愚蠢)的方法。下面的代码似乎是最简单的方法。

```
#include <iostream>
#include <string>

int main()
{
    const std::string red("\033[0;31m");
    const std::string green("\033[1;32m");
    const std::string yellow("\033[1;33m");
    const std::string cyan("\033[0;36m");
    const std::string magenta("\033[0;35m");
    const std::string reset("\033[0m");

    std::cout << yellow << " Hello color yellow " << reset << std::endl;
    std::cout << red << " Hello color red " << reset << std::endl;
    std::cout << green << " Hello color green " << reset << std::endl;
    std::cout << cyan <<" Hello color cyan " << reset << std::endl;
    std::cout << magenta << " Hello color magenta " << reset << std::endl;
     return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

显然它有它的缺点。我现在不知道，但你可以随意评论✏️的《它对你不起作用》。