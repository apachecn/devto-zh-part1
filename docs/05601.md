# 我如何用 JavaScript 找到女朋友

> 原文：<https://dev.to/jsalvador/how-i-get-a-girlfriend-using-javascript>

我不喜欢约会网站，但在每个人的生活中，你需要伴侣的那一天总会到来。所以我决定创造我自己的方式来得到最适合我的女孩。我用一个 HTML 表单和一个简单的 JavaScript 脚本制作了它。

### 列表

在开始编码之前，我写了一个清单，上面列有我想要送给我完美女孩的所有东西。我把他们分成三组:

1.  重要！(+15 分)
2.  需要，但我不介意如果没有(+7 分)
3.  不需要，但如果她有，那就更好了(+3 分)

如果我给我的女孩写一张我不想要的东西的清单可能会更好，但这使测试变得非常漫长和无聊。事实上，我的清单上大约有 55 个元素。

### 表单

这部分很简单。我们只需要用不同的类写一堆复选框。

*   重要的课程
*   需要的类
*   不需要的类

每个类用于设计每个响应的值。

```
 <form name="areyoumyperfectgirl">
    <input type="checkbox" class="element important"> Loves JavaScript
    <input type="checkbox" class="element needed">Loves Japanese food
    <input type="checkbox" class="element notneeded">Loves coffee
    [...]
  </form>
  <button class="success" type="button" onClick="getMyScore();">Let's go!</button> 
```

Enter fullscreen mode Exit fullscreen mode

对列表中的每个元素重复以上步骤。

这里我们需要的第二件事，是写一些 CSS 来使它变得漂亮。我用了[野餐 CSS](https://picnicss.com/) 因为它很酷。

### 脚本

好消息是。

我的脚本需要读取表单中的所有复选框，读取它们的类并计算总分。要在这次考试中获得 A 级，你至少需要获得最高分的 70%。

因此，首先，我们需要计算最大可能得分。

```
 function maxScore() {

    let important = (document.getElementsByClassName("important").length) * 15;
    let needed    = (document.getElementsByClassName("needed").length) * 7;
    let notneeded = (document.getElementsByClassName("notneeded").length) * 3;

    return important + needed + notneeded;

} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了它，下一步就是计算用户分数。

```
 function userScore() {

    let important_checkbox = document.getElementsByClassName("important");
    let need_checkbox = document.getElementsByClassName("needed");
    let notneed_checkbox = document.getElementsByClassName("notneeded");

    let score = 0;

    for (var x = 0; x < important_checkbox.length; x++) {
        if (important_checkbox[x].checked) {
            score = score + 15;
        }
    }

    for (var x = 0; x < need_checkbox.length; x++) {
        if (need_checkbox[x].checked) {
            score = score + 7;
        }
    }

    for (var x = 0; x < notneed_checkbox.length; x++) {
        if (notneed_checkbox[x].checked) {
            score = score + 3;
        }
    }

    return score;

} 
```

Enter fullscreen mode Exit fullscreen mode

和...最终比分。

```
 function getMyScore() {

    let userScore = userScore();
    let maxScore = maxScore();

    return (userScore / maxScore) * 100;

} 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

用几行和一个简单的网站，你可以得到你的完美女孩(或男孩！).只需分享你的测试链接，等待它。

干杯！