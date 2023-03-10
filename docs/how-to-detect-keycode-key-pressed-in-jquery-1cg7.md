# 如何检测 jquery 中按下的键码/键

> 原文：<https://dev.to/moreonfew/how-to-detect-keycode-key-pressed-in-jquery-1cg7>

如果你想给你的访问者一个用户友好的体验，跟踪一个键码会很有帮助。例如，如果一个页面上打开了一个覆盖图，我通常会按 ESC(退出)键来关闭覆盖图，现在大多数网站都会响应并关闭覆盖图。如果键码没有被追踪，它就不会工作。我们没有类似“onEscKeyPressed”、“onEnterKeyPressed”等事件，所以处理这类功能的唯一方法是跟踪被按下的键的键码。

> KeyCode 是按下的字符键的 Unicode 值。

## 使用 jQuery 如何识别键码/按下的键？

您可以使用 jQuery 的 **event.which** 来标识按下的键。它基本上规范了键盘按键输入的 event.keyCode 和 event.charCode。因此建议使用 event.which，除非你真的想使用 event.keyCode。让我们看几个例子来理解 **event.which** 的用法。

### 按 Esc / Escape 键如何关闭叠加？

Esc 或 Escape 键的键码是 27。你可以通过在你的页面上放置一个提示或者使用这个工具来找出一个键的键码。以下示例在按下 ESC 键时隐藏 ID 为“overlay”的 div。

```
$(document).ready(function(){

    $(document).keydown(function(event){
            if(event.which == 27){
                $("#overlay").hide();
            }
    });

}); 
```

Enter fullscreen mode Exit fullscreen mode

类似地，我们可以使用 keyCode 来限制特定字段中允许的字符类型。例如，假设我们有一个只包含数字的输入字段，我们不希望用户在该字段中输入除整数/数字之外的任何内容。在这种情况下，我们可以使用 **event.which** API 来检测键码并限制输入。

#### 如何限制键盘字符输入为数字？

以下示例将键盘字符输入限制为数字。

```
$(document).ready(function(){
  $("input").keydown(function(event){ 

       //KeyCode for digits 0-9 is 96 to 105 on the numeric pad.
       //KeyCode for digits 0-9 is 48 to 57 on the regular digit keys on a qwerty keyboard.
       //keyCode 8 is for backspace

    if((event.which >= 48 && event.which <= 57) || (event.which >= 96 && event.which <= 105) || (event.which == 8))
    {
        return true;
    }else{
        return false;
    }

  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

使用键码/字符码识别所按下的键是有帮助的，并且它还改善了最终用户的用户体验。你可以通过检测按键来做很多有创意的事情。

编码快乐！

关于 jquery 中[如何检测键码/按键的帖子最早出现在](https://www.moreonfew.com/how-to-identify-keycode-or-key-pressed-in-jquery/) [MoreOnFew](https://www.moreonfew.com) 上。