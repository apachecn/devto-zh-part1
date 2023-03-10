# 代码中的 TODO

> 原文：<https://dev.to/razbakov/todo-in-code-b>

例如，你写了一个代码，你想以后改进它。现在你的简单函数可以解决这个问题。
但是后来你想用一些库来计算所有。

```
// TODO: replace with vendor
function calculateTax($price, $country) {
    return $price;
} 
```

Enter fullscreen mode Exit fullscreen mode

将`TODO`注释留在代码中，并将这段代码合并到 master 中，是个好主意吗？

#### 这里引用几个

否:

*   [我如何处理代码](https://stevenschwenke.de/howIDealWithTODOCommentsInCode)中的 todo 注释
*   [被认为有害的 Todo 评论](http://wiki.c2.com/?TodoCommentsConsideredHarmful)

是:

*   [Github 机器人](https://github.com/apps/todo)
*   [imdone.io](https://medium.com/imdoneio/the-imdone-pitch-feedback-welcome-386430accf01)
*   TODO 注释有意义吗？
*   [评论中的 TODO 标签有标准吗？](https://stackoverflow.com/questions/3071732/is-there-a-standard-for-todo-etc-tags-in-comments)