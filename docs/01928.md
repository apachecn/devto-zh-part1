# 获取列表中与条件匹配的第一个索引

> 原文：<https://dev.to/amay077/gets-first-index-of-match-to-condition-in-list-6c7>

## 扩展方法

```
inline fun <T> Iterable<T>.firstIndexOrNull(predicate: (T) -> Boolean): Int? {
    return this.mapIndexed { index, item -> Pair(index, item) }
            .firstOrNull() { predicate(it.second) }
            ?.first
} 
```

Enter fullscreen mode Exit fullscreen mode

只用`.mapIndexed`和`.firstOrNull`。

## 用法

```
val arr = arrayListOf("a", "b", "c")

arr.firstIndexOrNull { it == "b"} // -> 1
arr.firstIndexOrNull { it == "z"} // -> null 
```

Enter fullscreen mode Exit fullscreen mode