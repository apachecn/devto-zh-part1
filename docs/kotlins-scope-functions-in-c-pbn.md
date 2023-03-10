# C#中的 Kotlin 作用域函数

> 原文：<https://dev.to/amay077/kotlins-scope-functions-in-c-pbn>

科特林的范围函数(`.let`、`.also`、`.apply`等)超级有用。

*   [let - Kotlin 编程语言](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/let.html)
*   [亦-科特林编程语言](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/also.html)
*   [apply - Kotlin 编程语言](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/apply.html)

我想在 C#中使用这些方法。
所以我只是定义一下！

```
static class ObjectExtensions 
{
    // Kotlin: fun <T, R> T.let(block: (T) -> R): R
    public static R Let<T, R>(this T self, Func<T, R> block) 
    {
        return block(self);
    }

    // Kotlin: fun <T> T.also(block: (T) -> Unit): T
    public static T Also<T>(this T self, Action<T> block)
    {
        block(self);
        return self;
    }   
} 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

```
var text = resultCode.Let(x => {
    switch (x) {
        case 0:
            return "success";
        default:
            return $"error({x})";
    }
});

var model = new MyModel().Also(m => {
    m.Initialize();
    m.Load(path);
}); 
```

Enter fullscreen mode Exit fullscreen mode

我希望如此。NET 框架提供了这些方法。