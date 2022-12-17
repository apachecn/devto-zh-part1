# 跟踪那个！

> 原文：<https://dev.to/ipasqualito/track-that-5f0>

这个函数跟踪每个事件及其在调用函数中设置的所有属性。如果您需要为 AB 测试定制跟踪，这很方便。

```
 var trackThat = function() {

    var t = {},
        r = {},
        a = window.event ? window.event.srcElement : i.target,
        c = document.getElementsByTagName(a.tagName),
        k = "";
    try {
        for (var d = 0; d < c.length; ++d) c[d] == a && (r.tag = a.tagName, r.index = d, r.text = a.hasChildNodes() ? a.childNodes[0].data : "null"); for (var l, d = 0, o = a.attributes, g = o.length; g > d; d++) l = o[d], t[l.nodeName] = l.nodeValue;
        r.attrs = t, k = JSON.stringify(r);
    } catch (i) {
        k = "error: " + i.message;
    } finally {
        return k;
    }
};

$( 'div.main-holder' ).on('click', function(e){
    console.log(trackThat(e));
}); 
```

Enter fullscreen mode Exit fullscreen mode

帖子[追踪那个！](https://www.recoveryarea.nl/javascript/track-that/)最早出现在[恢复区](https://www.recoveryarea.nl)。