# 附带说一句

> 原文：<https://dev.to/yechielk/parenthetically-speaking-2-502>

#### 加上对栈的简要介绍

几周前[我写了](https://dev.to/yechielk/parenthetically-speaking-1b)一个关于检查字符串以确保所有括号格式正确的标准面试问题，每个左括号都有一个匹配的右括号。

我以 JavaScript 函数的形式提供了解决方案，该函数记录了打开了多少个括号。如果当函数到达字符串末尾时，计数器小于零(右括号太多)或大于零(左括号太多)，函数将返回 false。

我的解决方案的一个缺点是它只处理括号；它没有处理其他字符，如括号、花括号和引号。这篇博客文章将扩展逻辑以包括那些字符。

这篇文章中的解决方案是用 Ruby 写的。

#### 定义问题。

提醒一下，这是我之前贴出的问题:

> 假设您有一个很长的文本字符串，有许多括号，所有的括号都嵌套在一起(例如一个很长的 LISP 程序)。您需要确保句子的格式正确，并且每个左括号都有相应的右括号。

解决方案包括命名一个名为 counter 的变量，然后遍历我们的字符串。对于字符串中的每个左括号，我们将 counter 递增 1，对于字符串中的每个右括号，我们将 counter 递减 1。这个过程让我们可以计算在任何给定的时间有多少括号是打开的。如果计数器小于零，这意味着我们刚刚结束了一个没有相应的左括号的括号，如果在字符串结束时计数器大于零，这意味着有一个我们从未结束的左括号。

我们现在面临的挑战是调整函数，使其能够跟踪开括号、花括号和引号。

乍一看，似乎我们要做的就是让计数器也跟踪这些字符。如果我们仔细想想，我们会发现我们很容易遇到像“([)]”这样的字符串问题；它们有相同数量的左括号和右括号，但是括号在括号内是开的，在括号外是关的，这是不允许的。

#### 书库

该解决方案使用一种称为堆栈的数据结构。

为了理解堆栈，让我们想想这个美味的堆栈:

[![stack of pancakes](img/fbf3ce333dc7ea67f7ccdf038c48c827.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XuDGeGNw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/590/0%2AIL4aD7k5ncyn-gYO.)

如果你正在做煎饼当早餐，你可能旁边有一个盘子。当每块煎饼做好后，你把它从煎锅里翻出来放到盘子里。慢慢地，盘子里的煎饼堆慢慢地成长为一个漂亮、整齐的堆。当你饥饿的家庭成员随着天堂般的气味飘进厨房时，他们看到了你旁边的一堆煎饼，然后他们每个人都走过来，从最上面拿走了一张煎饼。

这就是视觉效果。这里需要注意的重要一点是，任何与煎饼堆叠的交互都发生在堆叠的顶部。厨师把薄煎饼放在最上面，而家庭成员从上面拿走薄煎饼，所以最后放进去的薄煎饼先被拿走。

[![illustration of a stack](img/50bd027d9cb13b6474722c2e5f74f35c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H4wunlSC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/546/0%2A6s_vEA48GbnFkRGw.png)

类似地，在计算机科学中，堆栈是一种数据结构，在这种结构中，您只能将数据位添加到堆栈的顶部，然后只能从顶部移除它们，因此最后添加的数据将首先被移除(技术术语是 LIFO 或后进先出)。

从堆栈中添加和删除数据的技术术语是推和弹出，所以我们将一点数据推到堆栈的顶部，然后当我们需要它时，我们将它从顶部弹出。

那么堆栈将如何帮助我们平衡圆括号、方括号和引号呢？很简单，我们将使用堆栈来帮助我们跟踪打开的顺序。

每次遇到左括号或中括号，我们都会把它压入堆栈。然后，每当我们遇到一个右括号或中括号，我们将检查堆栈中的最后一个元素是什么；如果它匹配我们拥有的结束元素(例如，结束括号的开始括号)，我们将知道结束元素是合法的。然后，我们将从堆栈顶部弹出最后一个开始元素，以表示该开始元素已经关闭，然后继续下一个元素。

在 Ruby 中是这样的:

```
OPENERS = ["(", "[", "{"]
CLOSERS = [")", "]", "}"]
VERSATILES = ["\"", "`"]
MATCHERS = {
    "(" => ")",
    "[" => "]",
    "{" => "}"
}

    def paren_checker(string)

    stack = []

    string.split("").each do |char|
        if OPENERS.include?(char)
            stack.push(char)

        elsif CLOSERS.include?(char)

            if MATCHERS[stack.last] == char 
                stack.pop
            else
                return false
            end

        elsif VERSATILES.include?(char)

            if stack.last == char
                stack.pop
            else
                stack.push(char)
            end 

        end

    end
    # Return true if the stack is empty (no opening parentheses left)
    stack.size == 0
end 
```

Enter fullscreen mode Exit fullscreen mode

让我们一行一行地走一遍。

在前三行中，我们定义了开始元素、结束元素以及像引号这样没有单独的开始和结束元素的元素的数组。

在第 4 行中，我们定义了一个散列表，它将帮助我们将开始元素匹配到它们的结束元素。

然后，我们定义一个名为 paren_checker 的方法，该方法将一个字符串作为参数。

在方法的开始，我们将堆栈定义为一个空数组。

然后，我们将字符串分割成一个字符数组，并遍历该数组。

对于每个字符，我们进行以下检查:

如果在开始元素的数组中找到这个字符，我们就把它放入堆栈。

如果在我们的结束元素数组中找到这个字符，我们检查；它是否匹配堆栈中的最后一个开始元素？如果是，我们从堆栈中弹出最后一个开始的元素，如果不是，我们中断循环，因为显然有人试图关闭一个还没有打开的元素(或者已经在当前打开的元素之外打开了)。

如果在我们的通用元素数组中找到这个字符，那么我们检查；如果字符与堆栈中的最后一个元素不匹配，我们假设它是一个开始元素，并将其推入堆栈，如果它与堆栈中的最后一个元素匹配，我们假设它是一个结束元素，并从堆栈中弹出最后一个元素。

我在这个方法中没有包括的一个元素是单引号，因为它也可以用作撇号，这会打乱我们的计算。

* * *

*这篇文章是从我的博客 [Rabbi On Rails](https://blog.yechiel.me/parenthetically-speaking-2-db6386eb9db3) 交叉发布的。
你可以在那里了解更多关于我的编程之旅，或者在 Twitter 上关注我 [@yechielk](https://twitter.com/yechielk)*