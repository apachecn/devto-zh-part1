# 快速 Javascript 技巧 1:用 jQuery 获得 enter 键事件的最简单方法。

> 原文：<https://dev.to/rsmelo92/quick-javascript-tips-1-easiest-way-to-get-enter-key-event-with-jquery-29a7>

这是我在日常 Javascript 编码中使用的一系列快速技巧中的第一个。

这一个是关于得到回车键事件的真正不酷的东西。

让我们假设你必须在一个网站的顶部建立一个搜索栏。

要进行搜索，用户应该按回车键或点击搜索按钮。听起来很容易。因为我用的是 jQuery，所以大概是这样的。

```
$('input').on('keypress', (event)=> {
        if(event.which === 13){
            //Do Something
        }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这基本上就是说:当用户输入时，如果他按下的键有代码 13(通常是回车键),做一些事情。

如果你在 chrome 上测试，它会工作得很好。但当你在 Android 设备上测试时，地狱将降临人间:每个键都有代码 229！

[![alt text](img/f7219cad15480d71d3af0663f0c792d4.png "HellCameToEarth")](https://res.cloudinary.com/practicaldev/image/fetch/s--PHJA7dWT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a0olu51o12j02rwh33we.jpeg) 
*每把钥匙都有代码 229！！*

但是不要绝望！事情是这样的:229 是浏览器在处理用户输入的内容时发出的关键事件，由于自动更正功能，这在 Android 上经常发生。

因为自动更正正在处理事件以预测用户正在键入的内容，所以这段代码不会工作，因为它总是获得代码 229，而不是每个键的单独代码...这可不酷。

但是要克服这一点，你可以在每个浏览器的最广泛支持下做最原生的选择。

用一个**表单**标签包装你的搜索栏。

**真的简单易行。**

只需将所有内容放在一个表单标签中...

```
 <form>
    <input type="text">
    <button type="submit">
    Search!
    </button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

...并处理提交事件来做你想做的事情！

```
$('form').on('submit', (event)=>{
    event.preventDefault();
    // Do what you want here
    // In this case get input value
    // Do a search
    // Redirect to results page
}); 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记 preventDefault()，这样页面就不会刷新，因为这是本机行为。还记得把按钮放在表单里面，这样它会提交你的数据！

就是这样。这是我第一次尝试教一些东西，所以我希望我能以一种清晰的方式告诉它。

快速提示 2 中再见。