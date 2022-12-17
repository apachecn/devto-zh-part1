# 如何优雅地展平列表

> 原文：<https://dev.to/ycmjason/how-to-elegantly-flatten-a-list-61c>

# 问题

给定任何列表`xs`，其中`xs`可能包含其他列表或任何非列表值，我们希望提取`xs`中的所有值。例如:

1.  `flatten([1, [3], [[6, 7], [[[]], 8]]]) => [1, 3, 6, 7, 8]`
2.  `flatten([{a: 3}, 1, [[''], 2]]) => [{a: 3}, 1, '', 2]`

# 递归求解

当我们希望得到递归解时，我们必须避免递归思维。永远不要在递归调用中跟踪你的代码！正确的方法是假设您想要定义的函数已经在输入的一个更小的结构上工作，在这种情况下，这个更小的结构显然是`xs`的尾部，即`xs.slice(1)`。这种假设被称为`the leap of faith`。

那么现在如果`flatten(xs.slice(1))`将正确工作，我们如何使用它来构造`flatten(xs)`的完整正确答案呢？现在显然我们缺少了`xs[0]`。`xs[0]`可以是非列表元素，也可以是另一个列表。

如果`xs[0]`是一个非列表元素，我们简单的把`xs[0]`加回`flatten(xs.slice(1))`的第一位，就大功告成了！

如果`xs[0]`是另一个列表，我们取一个`leap of faith`，递归调用`flatten(xs[0])`。然后我们可以将`flatten(xs[0])`连接到`flatten(xs.slice(1))`。

```
function flatten(xs){
  if(Array.isArray(xs[0])) return [...flatten(xs[0]), ...flatten(xs.slice(1))];
  else return [xs[0], ...flatten(xs.slice(1))];
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们缺少的是一个基础案例。我们能展平的最小列表一定是`[]`，答案显然是`[]`。

所以最后的代码是

```
function flatten(xs){
  if(xs.length === 0) return [];

  if(Array.isArray(xs[0])) return [...flatten(xs[0]), ...flatten(xs.slice(1))];
  else return [xs[0], ...flatten(xs.slice(1))];
} 
```

Enter fullscreen mode Exit fullscreen mode