# 使用 jQuery 弹出表单

> 原文：<https://dev.to/nilanjana/sample-popup-form-using-jquery>

# 弹出窗口

弹出窗口是父窗口中的子窗口，它阻止与父窗口的交互。弹出窗口中也有一些选项可供选择。现在很多网站都使用这个功能。此窗口主要用于收集网站的评论或反馈。一些网站使用弹出框作为登录窗口。使用`jQuery`制作一个弹出框非常容易。

# jQuery

jQuery 是跨平台、免费、开源的`JavaScript library`。它被用作 html 的客户端编程语言。大多数浏览器都支持 jQuery。使用 jQuery 的主要目的是获得一个动态网站，jQuery 用于动画、 [DOM](https://www.w3.org/TR/WD-DOM/introduction.html) 、事件、ajax 等。

## 下面是制作一个简单 jQuery 反馈表单的代码

```
 <!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    popup
</head>  
<body style="background-image: url('aa.jpg');background-image: cover;">

    <!--review button-->
    <input type="button" id="reviews" value="reviews" name="" style="margin-left: 35%;margin-right: 20%;margin-top: 2%;margin-bottom: 2%;height: 50px;background-color: #884FF3;text-align: center;width: 10%;border-radius: 5%;
    position: fixed;">

    <!--feedback button-->
    <input type="button" id="feedback" value="feedback" name="" style="margin-left: 55%;margin-right: 20%;margin-top: 2%;margin-bottom: 2%;height: 50px;background-color: #884FF3;text-align: center;width: 10%;border-radius: 5%;
    position: relative;">
    <!--welcome message -->
    <div class="pop" style="z-index: 4;margin-left: 20%;margin-right: 20%;margin-top: 10%;margin-bottom: 2%;height: 30%;background-color: #768b8e;text-align: center;border-radius: 5%"><br/><h1>   WELCOME </h1><br></div>

    <!--review -->
    <div id="pop1" z-index="1"  style="margin-left: 20%;margin-right: 20%;margin-top: 5%;margin-bottom: 2%;height: 30%; background-color: #40858e;text-align: center; position: fixed; display: none; width: 60%;border-radius: 5%"><h2> REVIEWS </h2>
<h3 >&nbsp&nbsp&nbsp&nbsp&nbsp Name  :  <input type="TextBox" style="opacity: 0.5;width: 25%;" id="txtNamef" name="txt" placeholder=" you can't edit this field" value="" disabled="disabled"></h3>
 <h3>Feedback : <input type="TextBox"  style="opacity: 0.5;width: 25%;" id="txtf" name="txt" placeholder=" you can't edit this field " value="" disabled="disabled"></h3>
   <input type="button" class="close" value="close" width="1000px">

    </div>

<!--feedback-->
    <div class="pop-feed" style="background-color: #995892:; position:fixed;display: none; ">

    <div id="pop2" z-index="1"; style="margin-left: 20%;margin-right: 20%;margin-top: 5%;margin-bottom: 2%;height: 40%;background-color: #40858e;text-align: center;width: 60%;border-radius: 5%;
    position: fixed;">
    <h2> &nbsp&nbsp&nbspFEEDBACK</h2>
        <h3>  &nbsp&nbsp&nbsp&nbsp&nbsp  Name  :  &nbsp&nbsp&nbsp <input style="opacity: 0.5;width: 25%;" type="TextBox" id="txtName" class="txt" name="txt" placeholder="enter your name " value=""></h3>
    <h3>Feedback  : &nbsp&nbsp <textarea style="opacity: 0.5;width: 25%;" id="txt" class="txt" name="txtFeedback" value="" placeholder="enter your opinion"></textarea> </h3>
    <br>
        <input type="submit" " value="FEEDBACK" disabled="disabled" style="height: 15%;background-color: #884FF3;text-align: center;width: 15%;border-radius: 5%;
    ">&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
    <input type="button" class="close" value="CLOSE" style="height: 15%;background-color: #884FF3;text-align: center;width: 10%;border-radius: 5%;">
    </div>
    </div>

    <script type="text/javascript" src="jquery.js"></script>
    <script type="text/javascript" src="popup.js">
    </script>

</body>
</html> 
```

```
 $(document).ready(function(){
    $('.pop').delay('2000').fadeOut('5000');

    $('.close').click(function(){
        $('#pop1').hide('slow');
        $('#pop2').hide('slow');
    });

    $('.txt').change(function(){
    $(':submit').removeAttr('disabled');
    });
    $(':submit').click(function(){

        $('.txt').attr('value','nilanjana');
        alert('feedback submitted successfuly');
        var txtName =  $('#txtName').val();
        var txt =  $('#txt').val();
        alert(' NAME :'+txtName+' FEEDBACK :'+txt);
        $('#txtNamef').attr('value',txtName);
        $('#txtf').attr('value',txt);
        $('.pop-outer').fadeOut('slow');
        $('#pop1').fadeIn('slow');
        $('#pop1').delay('3000').fadeOut('slow');

    });

    $('.close').click(function(){
        $('.pop-outer').fadeOut('slow');
    });

    $('#reviews').click(function(){
        $('.pop-outer').hide();
        $('#pop1').fadeIn('slow');
        $('#pop1').fadeIn('fast').toggle();

    });

    $('#feedback').click(function(){
        $('#pop1').hide();
        $('#pop2').fadeIn('slow');
        $('.pop-outer').fadeIn('slow');
        $('.pop-outer').fadeIn('fast').toggle();

    });

}); 
```

## jQuery 的解释

*jQuery 基本语法:* `$(selector).action();`
$ sign:定义/访问 jQuery。要查找(查询)的(选择器)。

`delay()`是一种用来延迟某些任务执行的方法。
`click(function(){code})` :click 是一个事件处理程序，执行一些`function({code})`中定义的任务。
`show()`是用来表示某一特定元素的方法。它可以取`'slow'`、`'fast'`、`'milliseconds'`的值
、`hide()`是一种用来隐藏特定元素的方法。它可以取`'slow'`、`'fast'`、`'milliseconds'`的值
、`change()`是一个事件处理程序，用来了解一个元素的任何变化，(**仅对`textbox`、`select`、`input`、`textarea`、**有效)。
`removeAttr('attribute')`方法用于从一个元素中移除`attribute`。
`attr('value','changed value')`用于将特定属性的`value`变为`changed value`。
`val()`方法用于返回`value`属性的值。
`fadeIn()`方法将所选元素的不透明度从隐藏逐渐变为可见。
fadeout() `is a method used to vanish some element by fading that out;it gradually changes the opacity of the selected element from visible to hidden .`
`alert('message')`用于显示带有指定`message`的警告框。

在这里探索代码:在 [codepen](https://codepen.io/) 上使用 jQuery
弹出[表单](https://codepen.io/nilanjana9/pen/Bdxeox?editors=1000#0)