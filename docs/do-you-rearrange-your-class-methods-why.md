# 你重新安排你的班级方法了吗？为什么？

> 原文：<https://dev.to/ope/do-you-rearrange-your-class-methods-why>

我浏览过的许多(开源)库似乎都以一种特定的方式(重新)安排了成员类的方法，例如，任何要被该类中的其他方法使用的类方法都是首先定义的。

例如:

```
<?php
class Dispatcher
{
    public function isDispatchable()
    {
        $number = rand(0, 200);

        return ($number < 100) ? true : false;
    }

    public function dispatch()
    {
        if ($this->isDispatchable()) {
            // implementation goes here...
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，`isDispatchable()`是在使用它的方法之前定义的。

我通常只是在类的末尾添加新的方法，不管使用它的其他方法是在类的上面还是下面。我应该被送到长城以北吗？

你是做什么的？