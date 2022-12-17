# Getters 和 Setters

> 原文：<https://dev.to/burdettelamar/getters-and-setters-934>

不久前我“继承”的一些测试自动化处理了一个文本框，标签为`Brick`(不完全是它的真名)，它的 getter 方法被称为`GetCurrentBrick`。

花一点时间来猜测对应的 setter 被调用了什么。

它叫做`Brk`。

不错吧。Getter/setter 对:`GetCurrentBrick`和`Brk`。

现在，如果有一个 getter/setter 对(我一会儿会谈到这个问题)，这两者至少应该有一致的命名。

开始是`GetCurrentBrick`和`SetCurrentBrick`。

然而实际上，该页面没有关于砖块的其他内容，所以最好只说`GetBrick`和`SetBrick`。

但是为什么有两种方法呢？setter 应该总是返回以前的值，以防调用者需要恢复它。因此，拥有一个总是可以获取并可以(可选地)设置的方法是简单而有用的。

因此:

*   `Brick()`不带值的参数是纯 getter。
*   `Brick("in the Wall")`是返回前一个值的设置器。

这意味着只需要学习一个方法名，非常直观的是，无值调用是 getter，而有值调用是 setter。