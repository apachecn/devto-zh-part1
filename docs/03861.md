# JavaScript 回调到底是什么？

> 原文：<https://dev.to/faradayyg/understanding-javascript-callbacks-58i>

想象一下，你有一辆车，车坏了，但是这辆车对你真的很重要，因为你第二天要用它去上班。所以你打电话给你的朋友约翰，约翰是一个汽车修理工，约翰把你的车拖到他的车间。但是在他离开之前，你让他在完成维修后给你打电话(同样，因为这辆车非常重要)。约翰最终在 2 小时内完成了维修，然后打电话给你。

## 介绍 JavaScript 回调。

假设上面的例子是一个 JavaScript 程序，其中`repairCar()`是程序中的一个函数，它要求约翰来拖你的车，当他完成修理时，向他发出的给你打电话的指令可以被比作回调。怎么会？因为你只是向约翰发出了一个 sub 指令，要求在修理完成时得到通知。
该回调是现有指令中的另一条指令，在主指令之后执行。😅

回调函数被定义为传递给另一个函数 G 的函数，比如 F，导致 F 在 G 内部执行🤓

使用默认 javascript `setTimeout()`函数的例子，我们将检查回调是如何使用的。`setTimeout()`函数有两个参数，一个回调和回调执行前的时间(以毫秒为单位)。

```
setTimeout( function(){console.log('callback run')}, 3000 ) 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码块演示了在`setTimeout()`函数中回调的使用。正确分析后，我们可以看到只有两个参数被传递给了`setTimeout()`函数:回调和以毫秒为单位的时间。

是的，回调是非常酷的，但是我能定义我自己的需要回调的函数吗？当然，你当然可以。我将直接举一个例子。

```
function needCallback(parameter, callBackEntry = function(){}){

        //perform computation with parammeter one

        const result = parameter * 100;

        //execute callback function
        callBackEntry(result);
} 
```

Enter fullscreen mode Exit fullscreen mode

让我解释一下，在上面的代码块中，我们简单地定义了一个带有两个参数的函数，第二个参数是一个回调函数。在函数体中，我们用第一个参数执行了一次计算，并将结果传递给回调函数，这是第二个参数。

要使用上述函数，我们只需这样做:

```
needCallback(20, function(result){
console.log(result)
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以清楚地看到，第一个参数是用于计算的数字，第二个参数是回调函数。很漂亮吧？

回调非常重要，尤其是对于那些依赖于执行流程中其他计算结果的进程，比如说一个网络调用。

但是如果我有一个依赖于另一个函数的函数，而另一个函数又依赖于第三个函数来工作呢？别担心，JS 会搞定你的。

## 回车:回调链接。

回调可以被链接起来，以便函数按时间顺序执行。为了实现这一点，可以将回调嵌套在其他回调中。以下面的例子为例:

```
//call functions in order

first('hello', function(){
        second('my',function(){
                third('friend',endFunction)
        })
})

//Define the functions used below:

// the first function to be executed

function first(data,cb){
        console.log(data);
        cb();
}

//second function to be executed

function second(data,cb){
        console.log(data);
        cb();
}

//third function to be executed

function third(data, cb){
        console.log(data);
        cb();
}

//Last function to be executed

function endFunction(){
        console.log('ended')
} 
```

Enter fullscreen mode Exit fullscreen mode

我知道这很令人困惑，但这里发生的是`endFunction()`被拴到了`third()`上，而`third()`又被拴到了`second()`上，后者最终被拴到了`first()`上。这确保了函数按照编写的顺序执行。

值得庆幸的是，没有太多的情况下你需要这样做，但如果你绝对必须这样做，有另一个概念叫做承诺，它简化了整个链接过程。

现在你已经理解了回调，(希望)让我们写出我之前作为 JavaScript 程序给出的例子。

```
function repairCar(callback = function(){}){
    //do the car repairs
    callback();
}

function callMe()
{
    //do the function that calls me
}

//car breaks down, so You call John

repairCar(function(){
    callMe
})

//John repairs the car and then calls me. 
```

Enter fullscreen mode Exit fullscreen mode

这绝不是关于回调的详尽教程，但我希望它足以让你开始。