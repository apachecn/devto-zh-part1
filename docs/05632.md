# 脚注！

> 原文：<https://dev.to/monknomo/footnotes>

我正在网上看书，突然看到一个很酷的东西。脚注，即当你将鼠标悬停在小<sup id="fnr-footnotes-1">上时弹出的[1](#fn-footnotes-1)T5】注释。</sup>

卢卡斯·马西斯(Lukas Mathis)慷慨地向公众提供了他的代码，所以我借用了他的书签中的代码来制作 [Daring Fireball](https://daringfireball.net) 。我没有做代码中假定的 Gruber 风格的脚注。

## 格鲁伯风格的脚注

格鲁伯风格的脚注有两个部分:

1.  上标链接
2.  带有返回链接的脚注

Markdown 中的上标链接是这样的:`<sup id="fnr-footnotes-1">[1](fn-footnotes-1)</sup>`。重要的部分是，`<sup>`元素有一个以“fnr”开头的 id(我假设是“脚注返回”)，以及一个以“fn”开头的脚注链接。

带有返回链接的脚注看起来像:`<a id="fn-footnotes-1">1: </a> This is an example footnote here - see how it has a return link [âŽ](#fnr-footnotes-1)`。重要的部分是 id 以“fn”开头的链接和到上标链接的`<sup>`元素的返回链接。

我借用的脚本假设您拥有 Gruber 风格的脚注的两个部分，以及适当的前缀和返回链接。

唉，dev.to 不是我的网站，所以如果你想看到它的运行，你必须查看 Lukas 的网站或我的网站进行演示。Lukas 的 bookmarklet 应该也能在这个页面上工作。

我稍微调整了一下剧本的颜色，以符合我的主题，但这就是，和卢卡斯·马西斯的原著差不多没变:

```
$(document).ready(function() {

  var sups = document.getElementsByTagName("sup");

  var footnotehtml = [];

  for (var i = 0; i < sups.length; i++) {

    var sup = sups[i];

    if (sup["id"] && sup["id"].substr(0, 3) == "fnr") {

```
 var footnr = sup["id"].substr(3);
  console.log(footnr);
  var footnote = document.getElementById("fn" + footnr);
  console.log(footnote);
  if (!footnote) continue;
  console.log("asdfasdfaf");
  footnotehtml[i] = footnote.parentNode.innerHTML;
  console.log(sup);
  sup.setAttribute("footnoteindex", i);
  sup.onmouseover = function(event) {
    var footnotepopup = document.getElementById("footnotepopup");
    if (footnotepopup) footnotepopup.parentNode.removeChild(footnotepopup);
    var index = parseInt(this.getAttribute("footnoteindex"));
    var popup = document.createElement("div");
    popup.innerHTML = footnotehtml[index];
    popup.id = "footnotepopup";
    popup.style.position = "absolute";
    popup.style.left = event.pageX - 125 + "px";
    popup.style.top = event.pageY + 25 + "px";
    popup.style.width = "15em";
    popup.style.textAlign = "left";
    popup.style.backgroundColor = "Gainsboro";
    popup.style.border = ".1em solid black";
    popup.style.borderRadius = "6px";
    popup.style.padding = "1em";
    document.body.appendChild(popup);
  };
  sup.onmouseout = function(event) {
    var footnotepopup = document.getElementById("footnotepopup");
    if (footnotepopup) footnotepopup.parentNode.removeChild(footnotepopup);
  };
} 
```

    Enter fullscreen mode 

    Exit fullscreen mode 

}
})； 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
脚注

 这是一个脚注的例子——看看它是如何有一个返回链接

## 学分

[每月收到一封电子邮件，其中包含来自网络的优秀技术和技术领导文章](http://www.gunnargissel.com/pages/email-signup-1.html)

*感谢 [Erica Schoonmaker](https://www.flickr.com/photos/_erica/) 提供的[书籍](https://flic.kr/p/9EUVrx)T5】的图片*

如果你喜欢这个，[访问我的博客了解更多信息](http://www.gunnargissel.com)