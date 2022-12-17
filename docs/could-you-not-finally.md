# 你能不能不要最后？

> 原文：<https://dev.to/dimitarnestorov/could-you-not-finally>

你知道有什么方法可以跳过一个`finally`块的执行吗？

```
function fn(){
    try{
        return 1;
    }finally{
        console.log(2);
    }
}
fn();
// Logs 2
// Returns 1 
```

Enter fullscreen mode Exit fullscreen mode

一个更奇怪的例子是，如果你在`finally` :
中使用`return`

```
function fn(){
    try{
        return 1;
    }finally{
        return 2;
    }
}
fn(); // 2 
```

Enter fullscreen mode Exit fullscreen mode

看来终于无法阻止了！