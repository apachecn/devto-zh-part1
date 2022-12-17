# 非阻塞控制台。ReadKey()

> 原文：<https://dev.to/adamkdean/non-blocking-console-readkey-236k>

只是今天的一个小片段。

你是否曾经编写了一个快速控制台应用程序来测试一些东西，并希望它一直循环，直到你按下一个退出按钮？但是控制台。ReadKey()块，所以不能真正使用它...还是可以？

```
while (true)
{
    while (!Console.KeyAvailable)
    {
        // do work
    }

    Console.WriteLine("Press Q to quit or any other key to continue..");
    if (Console.ReadKey().Key == ConsoleKey.Q) break;
} 
```

Double while 循环对我来说似乎是错误的，当我看到它时，我不禁感到一种错误的感觉，因为我知道这是我的代码，并且我的心率确实在每天 WTF 和编码恐怖的前景中增加，但是当你的工作不管它以什么顺序被调用时，它工作得很好。

我使用这个特殊的循环一遍又一遍地写一个文件，以查看文件的大小是否对 StreamWriter 有任何性能问题，我可以报告它没有..无论如何，6GB 的 Lorem Ipsum 是不行的。

所以，你去那里，控制台。KeyAvailable 只是检查一个密钥是否可以读取，如果可以，它就读取它并检查它是否是退出者的 Q。很简单。

更多片段如下。

圣诞快乐！