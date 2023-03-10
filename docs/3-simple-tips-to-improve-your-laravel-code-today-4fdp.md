# 3 个简单的技巧来改善你的代码

> 原文：<https://dev.to/edmilsonrobson/3-simple-tips-to-improve-your-laravel-code-today-4fdp>

我使用 Laravel 已经有一段时间了，在过去的几个月里，我真正关注的是如何改进我的代码和项目结构。那么为什么不为拉勒维尔的学徒们分享这些知识呢？

让我们一起看看 Laravel 项目中的几个真实例子，以及我们如何重构并从中学习。准备好改进您的代码了吗？

## 1。重构你的收藏，孩子

想象一下，你正在开发一个网站，让学生参与项目并每周评分，你的工作是向他们的导师显示给定项目中所有学生的当前平均分数，有效地对项目的平均学生分数进行评分，以便跟踪进度。

你可能会想出这样一个项目类:

```
<?php

class Project extends Model{
    /** ... code omitted for brevity **/

    public function studentsAverageScore() {
        $participants = $this->participants;

        $sum = 0;
        $totalStudents = 0;
        foreach($participants as $participant) {
            if ($participant->isStudent()) {
                $totalStudents++;
                $sum += $participant->student->lastRating()->averageScore();
            }
        }

        return $sum / $totalStudents;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

我们的方法似乎非常有效。我们循环访问我们的参与者，检查参与者是否是学生(例如，一些参与者可以是他们的导师)，并不断总结他们的最后评级平均分(给定评级中每个标准的平均值)。

这里的问题是，如果有人因为一个 bug 修正或者一个变更的需求而不得不回到这个方法，你的队友(或者甚至你自己)在做任何事情之前将会在他的头脑中“编译”这个`foreach`。循环是通用的，在这种情况下，我们在每一次循环中做多件事情:我们检查他们是否是学生，然后将它添加到一个 sum 中，我们只在 return 语句中再次处理它。

当然，这是一个相对简单的例子，但是想象一下，如果我们做得更多呢？如果我们想把它过滤给仅仅**一些**学生或者给每个学生增加不同的权重会怎么样？也许考虑他们所有的收视率，而不仅仅是最后一次？这可能会很快失控。

那么如何才能更好的表达这些检查和计算呢？更语义化？幸运的是，我们可以通过雄辩术给我们的方法使用一点函数式编程。

使用`filter`方法可以只为我们返回学生:
，而不是手动检查给定的参与者是否是学生

```
<?php

public function studentsAverageScore() {
    $participants = $this->participants;

    $participants->filter(function ($participant) {
        return $participant->isStudent();
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`filter`函数，我们可以传递一个函数作为参数，只返回满足我们条件的参与者。在这种情况下，这个调用将返回`$participants`的一个子集:只有学生。

自然，我们也需要通过计算他们的平均分来完成这个。我们现在应该做一个`foreach`吗？它仍然是次优的。在我们返回的雄辩系列中，在另一个函数中有一个内置的解决方案，方便地称为`average`。它遵循类似于`filter`的规则，在这里我们只返回我们想要从整个集合中取平均值的值。最终的代码是这样的:

```
<?php

public function studentsAverageScore() {
    $participants = $this->participants;

    return $participants->filter(function ($participant) {
        return $participant->isStudent();
    })->average(function ($participant) {
        return $participant->student->lastRating()->averageScore();
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

因为 average 返回一个数字，这正是我们想要的。注意我们如何**链接**我们的调用，以及代码看起来有多好。您几乎可以像阅读自然语言一样阅读它:让参与者按照谁是学生进行筛选，然后平均他们最后一次评级的分数并返回值。我们代码的意图更清晰，我们的代码更干净，更有语义。

这不仅适用于 PHP 或口才，真的-你可以用 javascript 做类似的事情。这超出了本文的范围，但是如果您从未听说过 javascript 环境中的 filter、map 和 reduce，可以去看看。

## 2。注意 N+1 查询问题

让我们借用技巧 1 中的代码。注意我们如何在`average`函数中获取给定参与者的学生模型。这很成问题，因为我们通过一次加载一个学生模型，在“幕后”进行了额外的 SQL 查询。

对此，更好的解决方案是在我们的第一次查询中**立即加载**它们。当我们这样做时，我们可以大大减少数量，而不是有 N+1 个查询(这就是这个可怕问题的名字)。

用`with`方法很容易做到口若悬河。我们来重构一下上面的代码:

```
<?php

public function studentsAverageScore() {
    $participants = $this->participants()->with('student')->get();

    return $participants->filter(function ($participant) {
        return $participant->isStudent();
    })->average(function ($participant) {
        return $participant->student->lastRating()->averageScore();
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当我们调用`$participant->student`，与参与者相关的学生模型在我们第一次调用(`$this->participants()`)时就已经被缓存了。

(顺便说一下，在上面的代码中仍然有一个与这个技巧相关的非优化调用——您能发现它吗？留在评论里)

## 3。改进您的刀片文件

我爱刀锋。这是 Laravel 附带的一个强大的模板引擎，具有惊人的语法。但是你利用了它所有的潜力了吗？

我们都必须使用`@foreach`来向用户显示一些集合，但是如果集合是空的呢？一个简单的答案是在`@foreach`之前使用一个`@if`语句。原来有一种更好的写法:

```
<?php

@forelse ($participants as $participant)
    <li>{{ $participant->email }}</li>
@empty
    <p>No participants in this project :(</p>
@endforelse 
```

Enter fullscreen mode Exit fullscreen mode

`@forelse`与`@foreach`非常相似，但是增加了`@empty`部分。比用`@if`干净多了吧？

说到`@if`，刀锋还有另一个我很喜欢的指令:`@unless`和`@endunless`。它与`@if`正好相反，而且读起来比仅仅是一个带有负面条件的`@if`要好得多。如果你曾经使用过 Ruby，你应该知道它是如何工作的。

现在在 Laravel 5.5 中也有一些快捷方式用于认证:`@auth` / `@endauth`和`@guest` / `@endguest`。这真的很像只使用`@if(Auth::check())`，但是读起来更好。这里有一个来自 Laravel 5.5 docs 的简单例子:

```
<?php

@auth
    // The user is authenticated...
@endauth

@guest
    // The user is not authenticated...
@endguest 
```

Enter fullscreen mode Exit fullscreen mode

官方文件中有更多的内容，你也可以写自己的指令。我强烈推荐它——使你的模板文件更容易推理，而不是一堆毫无意义的如果。

## 得到了更多有用的提示？

暂时就这样了。如果你有一个很酷的建议，想在你的项目中使用，请留下你的评论！记住:更好的代码意味着花在重构和修复错误上的时间更少。你的团队会感激:)