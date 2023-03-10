# 发现 JavaScript:let、var 和 ifs

> 原文：<https://dev.to/iifeoluwa/discovering-javascript-lets-vars-and-ifs>

学习是美好的，过程更令人着迷。我们如何超越对某个学科的无知，达到不同程度的熟练程度，这简直令人震惊。我从事专业编程的时间相对较短，但我认为记录我在学习过程中发现的一些有趣的事情会很好。我很高兴能和你分享我的第一篇文章，希望能成为一个系列。

## [有](#let-amp-var)年&

> `let`和`var`的主要区别在于两个关键字的范围——`let`是块范围的，而`var`是函数范围的。
> [-来源](https://stackoverflow.com/questions/762011/whats-the-difference-between-using-let-and-var-to-declare-a-variable)

你看，我已经意识到了这个规则，但是直到最近我在从事一个项目功能时亲身经历了这个行为，它才真正有了意义。

这段代码片段演示了这种行为，有点类似于我灵光一现时所做的关于区分`let`和`var`的工作。

```
function checkProperty(userObject){
    if(userObject.hasOwnProperty('name') && userObject.hasOwnProperty('age')) {
        let name = userObject.name;
        let age = userObject.age;
    }
    //proceed to work with variables, in our case we log it to the console
    console.log('Name:', name);
    console.log('Age:', age);
}

let user = {name: 'Dev', age: 10};

checkProperty(user); //execute function: should throw Reference Error 
```

Enter fullscreen mode Exit fullscreen mode

运行这个代码片段应该抛出一个表示`name is not defined`的`ReferenceError`。这是因为`let`关键字被绑定到声明它的代码块。在这种情况下，用`let`声明的变量被绑定到我们的`if`块，因此在它之外是不可访问的。这句引用自 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var) 的话最好地解释了`var`关键字的行为；

> 用 var 声明的变量的范围是其当前执行上下文，它可以是封闭函数，或者对于在任何函数外部声明的变量，是全局变量

我做的下一件事，尽管是在验证了这不是一个输入错误之后，直觉地将`let`改为`var`，瞧，它就像它应该的那样工作了。这时，对这两个关键字的“范围”对我来说就有意义了。

在识别和理解了这种行为之后，我修改了我的代码，并在`checkProperty`函数范围内声明了我的变量，从而修复了错误。更新后的代码如下所示:

```
function checkProperty(userObject){
    let name, age;
    if(userObject.hasOwnProperty('name') && userObject.hasOwnProperty('age')) {
        name = userObject.name;
        age = userObject.age;
    }
    //proceed to work with variables, in our case we log it to the console
    console.log('Name:', name);
    console.log('Age:', age);
}

let user = {name: 'Dev', age: 10};

checkProperty(user); //execute function: Logs "Name: Dev \n Age: 10" to console 
```

Enter fullscreen mode Exit fullscreen mode

## 如果

我需要确保对象的某些属性在使用之前已经设置好了，我不想写太长的条件以至于不得不水平滚动，这不太好。所以我开始用`if`做实验，直到我找到一个既好用又漂亮的东西。ðŸ˜Š

我的第一部 iteration*ðÿ'€就有这样的东西:脸掌:

```
function checkProperty(user){

    if(user.name !== 'undefined' && user.age !== 'undefined' && user.email !== 'undefined' && user.level !== 'undefined') {

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我知道看到这很痛苦。一定有别的办法，我确实找到了一些东西！

```
function checkProperty(user){
    if((user.name && user.age && user.email && user.level) !== (undefined || null)) {
        console.log('It passed.')   
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用这种方法的注意事项是，它会传递对象上不存在的属性。这意味着在继续使用之前，必须使用“hasOwnProperty”函数确保属性存在。哦，太好了！:(

对于健壮的对象模式验证，您可能应该研究一下 [joi](https://github.com/hapijs/joi) 。

欢迎评论和指正。感谢阅读！