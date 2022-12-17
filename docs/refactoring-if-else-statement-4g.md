# 重构 If-else 语句

> 原文：<https://dev.to/mervinsv/refactoring-if-else-statement-4g>

大家好！我只是想听听你对如何使用 if-else 语句使其更具可读性和可维护性的意见/想法，或者使用 if-else 语句的最佳实践是什么。所以我们开始吧！

### 第一

*嵌套 if 语句*

```
if(!isEmpty(object))
{
    if(IsString(object.number))
        log("Is a String"); 
    else if(IsNumber(object.number))
        log("Is a number");
    else
    log("Is a special character");      
} 
```

Enter fullscreen mode Exit fullscreen mode

或者可以这样:

```
var bool = !isEmpty(object);
if(bool && IsString(object.number))
{
    log("Is  a String");    
}
else if(bool && IsNumber(object.number))
{
    log("Is a number");
}
else if(bool)
{
    log("Is a special character");
} 
```

Enter fullscreen mode Exit fullscreen mode

你认为哪个更好？哪个容易维护和阅读？而如果这些 *IF 语句*变大会发生什么？

### 第二

*三元运算符 vs if-else-if 语句*

```
var tempVar = 0;
if(number == 1)
    tempVar = 1;
else if(number == 2)
    tempvar = 2;
else
    tempvar = 3; 
```

Enter fullscreen mode Exit fullscreen mode

还是这个

```
var tempVar = ((number == 1)? 1: (number == 2)? 2:3); 
```

Enter fullscreen mode Exit fullscreen mode

如果说可维护性，哪一个更好？