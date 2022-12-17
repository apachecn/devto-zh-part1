# FizzBuzz？多一行不算！

> 原文：<https://dev.to/adamkdean/fizzbuzz-more-than-one-line-doesn-t-count-31ap>

在[做*研究*的时候，我偶然发现](http://www.reddit.com/r/cscareerquestions/)[一个关于 FizzBuzz](http://www.reddit.com/r/cscareerquestions/comments/1ap4ev/do_people_really_fail_fizzbuzz_during_interviews/) 的帖子。

我听说过它，但是我实际上从未做过它。我决定去做，并让自己接受考验。

基本上规则是:

> 写一个程序打印从 1 到 100 的数字。但是对于三的倍数打印“嘶嘶”而不是数字，对于五的倍数打印“嗡嗡”。对于同时是 3 和 5 的倍数的数字，打印“FizzBuzz”。

似乎很简单:

```
static void EasyFizzBuzz()
{
    for (int i = 1; i <= 100; i++)
    {
        if (i % 3 == 0 && i % 5 == 0) Debug.WriteLine("FizzBuzz");
        else if (i % 3 == 0) Debug.WriteLine("Fizz");
        else if (i % 5 == 0) Debug.WriteLine("Buzz");
        else Debug.WriteLine(i);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但那太容易了。我相信每个人都会想出那个解决方案。如果有人问我，我宁愿站出来，没有什么比一大串三元运算符更突出的了。(如果我为了美观把它放到下一行，它算不算一行？只有一个分号！)

```
static void OneLineFizzBuzz()
{
    for (int i = 1; i <= 100; i++) Debug.WriteLine((i % 3 == 0 && i % 5 == 0) ?
        "FizzBuzz" : (i % 3 == 0) ? "Fizz" : (i % 5 == 0) ? "Buzz" : i.ToString());
} 
```

Enter fullscreen mode Exit fullscreen mode

也许我可以想出一些更有创造性的东西，只是这么简单的问题你能有多大的创造力呢？