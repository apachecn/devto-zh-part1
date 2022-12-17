# 内容选项卡

> 原文：<https://dev.to/eecms/content-tabs-2i9c>

首先，我们需要一些 HTML 标签！

```
<div class="tabs-wrap">
    <ul class="tabs">
        <li><a class="act" href="" rel="t-0">Tab 1</a></li>
        <li><a href="" rel="t-1">Tab 2</a></li>
        <li><a href="" rel="t-2">Tab 3</a></li>
    </ul>
    <div class="tab-content t-0 tab-open">
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
    </div>
    <div class="tab-content t-1">
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
    </div>
    <div class="tab-content t-2">
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.</p>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我们使用数组索引编号方案，所以第一个选项卡是`t-0`，然后是`t-1`，依此类推。`tab-open`表示默认，打开标签页。你可以将这个移动到任何一个`tab-content`，但是一次只能选择一个`tab-content`。正如你在标签中看到的，每个标签都被分配了一个`rel=`，与它应该打开和关闭的`tab-content`相匹配。

现在我们需要一些浅色的样式来给它一个标签式的外观。首先，我们需要一个标签 CSS 对象。

```
.tabs{
    list-style-type: none;
    margin: 0;
    overflow: hidden;
    padding: 0;

        li{
            float: left;
        }
}

.tab-content{
    display: none;

        p, ul, ol{
            &:last-child{
                margin-bottom: 0;
            }
        }
}

.tab-open{
    display: block;
} 
```

Enter fullscreen mode Exit fullscreen mode

对于视觉效果，选项卡组件 CSS。

```
.tabs{
    margin-top: 20px;

        a{
            background-color: #F9F9F9;
            display: inline-block;
            line-height: 1;
            padding: 10px;

                &.act,
                &:hover{
                    background-color: #EEEEEE;
                }
        }
}

.tab-content{
    background-color: #EEEEEE;
    margin-bottom: 20px;
    padding: 1px 20px 20px;

        .tab-content{
            background-color: #FFFFFF;
            margin-bottom: 0;
        }

        .tabs{
            a{
                &.act,
                &:hover{
                    background-color: #FFFFFF;
                }
            }
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后也是最重要的一点，我们需要一些 javascript 来让标签按预期运行。

```
$('.tabs-wrap > .tabs a').on('click',function(){
    var tabClassIs = $(this).attr('rel');

    $('.tb-act').removeClass('tb-act');

    $(this)
        .closest('ul')
        .closest('.tabs-wrap')
        .addClass('tb-act');

    // close OTHER .tab(s), ignores the currently open tab
    $('.tb-act > .tabs a')
        .not(this)
        .removeClass('act');

    // removes the .tab-open class from any open tabs, and hides them
    $('.tb-act > .tab-content')
        .not('.tab-content.'+tabClassIs+'.tab-open')
        .removeClass('tab-open');

    $(this).addClass('act');

    $('.tb-act > .tab-content.'+tabClassIs).addClass('tab-open');

    // stop THIS from reloading
    // the source window and appending to the URI
    // and stop propagation up to document
    return false;
}); 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里演示这段代码:[https://jsfiddle.net/jmathias/spktuvff/](https://jsfiddle.net/jmathias/spktuvff/)

您也可以将`tab-wrap`嵌套在`tab-content`中，并且每个 HTML 文档有多个`tab-wraps`。

就是这样，一个快速简单的方法来添加选项卡式内容到您的下一个 ExpressionEngine 站点！

-

最初发表于 u.expressionengine.com[的](https://u.expressionengine.com//article/content-tabs)[詹姆斯·马蒂亚斯](https://u.expressionengine.com/author/james-mathias)