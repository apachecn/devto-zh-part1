# AB 测试 jQuery 性能备忘单

> 原文：<https://dev.to/ipasqualito/ab-test-jquery-performance-cheat-sheet-41i0>

[![cheating](img/6d205b5cb20b3bdad52e6b0292b58928.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dCstyvQU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://static.recoveryarea.nl/uploads/2016/10/Cheating-In-Exams-16.jpg)

如果你用 jQuery 编写 AB 测试，你必须确保尽可能优化你的代码。每减少一毫秒，就减少了不必要的重画或回流闪烁的机会。

更新:提示 1:不要使用 jQuery！为什么要等待库的加载，现在普通的 JS 可以做 jQuery 所做的一切，而且更快。

### 使用更快的选择器。

了解哪个选择器执行速度最快，以优化您的代码！

```
 // ID selector - very fast (document.getElementById)
$("#id");
// TAG selector - fast (document.getElementsByTagName)
$("p");, $("input");, $("form");
// CLASS selector - performs well in modern browsers (document.getElementsByClassName)
$(".class");
// ATTRIBUTE SELECTOR - slow - needs document.querySelectorAll to perform OK-ish
$("[attribute=value]");
// PSEUDO selector - slowest - needs document.querySelectorAll to perform OK-ish
$(":hidden");

// also, instead of this:
$("#id p");
// do this:
$("#id").find("p"); // --> limit the scope that has to be searched: more than twice as fast! 
```

Enter fullscreen mode Exit fullscreen mode

### 使用缓存。

基本上每次用

```
 $('someselector') 
```

Enter fullscreen mode Exit fullscreen mode

你遍历 dom。如果你需要一个元素两次以上，你应该存储元素引用！

```
 // instead of this:
$('a.contactus').css('padding', '10px');
$('a.contactus').css('margin', '4px');
$('a.contactus').css('display', 'block');
// do this:
var myvar = $('a.contactus');
myvar.css({
padding: '10px',
margin: '4px',
display: 'block'
}); // element stored, CSS passed as object 
```

Enter fullscreen mode Exit fullscreen mode

### 使用链接。

链式方法将比在缓存选择器上创建的多个方法稍快，并且两种方式都将比在非缓存选择器上创建的多个方法快得多。

```
 // instead of this
$("#object").addClass("active");
$("#object").css("color","#f0f");
$("#object").height(300);
// do this
var myvar = $('a.contactus');
myvar.addClass("active").css("color", "#f0f").height(300); 
```

Enter fullscreen mode Exit fullscreen mode

### 使用事件委托。

事件侦听器消耗内存。

```
 // instead of this: (an event listener for EVERY table cell)
$('table').find('td').on('click',function() {
$(this).toggleClass('active');
});
// do this: (an event listener for only the table that gets fired by its 'td' children)
$('table').on('click','td',function() {
$(this).toggleClass('active');
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 使用更智能的 DOM 操纵。

每次操作 DOM 时，浏览器都必须重新绘制和回流内容，这可能成本极高。

```
 // instead of this:
const arr = [reallyLongArrayOfImageURLs];
$.each(arr, function(count, item) {
let newImg = '<li><img src="'+item+'"></li>';;
$('#imgList').append(newImg); // aargh a selector in a loop! and we're adding an element here, too!
});

// do this
var arr = [reallyLongArrayOfImageURLs],
tmp = '';
$.each(arr, function(count, item) {
tmp += '<li><img src="'+item+'"></li>'; // no selector and the HTML to add is stored in a variable...
});
$('#imgList').append(tmp); // ..that will be added once when the loop has finished 
```

Enter fullscreen mode Exit fullscreen mode

帖子[AB Test jQuery Performance Cheat Sheet](https://www.recoveryarea.nl/ab-testing/ab-test-jquery-performance-cheat-sheet/)首先出现在 [recoveryArea](https://www.recoveryarea.nl) 上。