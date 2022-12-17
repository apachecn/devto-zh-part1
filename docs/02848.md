# 计算出至今为止在乐天购物的总额

> 原文：<https://dev.to/ganmosan/-8nh>

因为亚马逊的那个再次变热了，乐天市场的人也是。

参考 http://blog.kantan-life.com/article/412662083.html
[にしたサイト](http://blog.kantan-life.com/article/412662083.html)

即使是这个网站上记载的代码也能充分发挥作用，但关键的总额不被计算在内。 另外，如果是 Chrome 的话就不动作。
因此，对这个网站上登载的代码进行了改良，并加入了书签。
书签化使用了“Closure Compiler”。
[http://closure-compiler.appspot.com/home](http://closure-compiler.appspot.com/home)

-以下ブックマークレット
JavaScript:(function(){ function c(a){ a = " number "！= =的类型？0:a；if(0===a & & ($("

").css({position:"fixed",left:0,top:0,width:"100%",height:"100%",zIndex:1E3,backgroundColor:"rgba(0,0,0,.7)",color:"#fff",fontSize:30,textAlign:"center",paddingTop:"15em"}).attr("id","***overlay").text("\u697d\u5929\u5e02\u5834\u3044\u304f\u3089\u4f7f\u3063\u305f\uff1f").appendTo("body"),e=2012,!/<sup>[0-9]+$/.test(e)))return</sup> alert("\u6b63\u3057\u3044\u6570\u5024\u3092\u5165\u529b\u3057\u3066\u304f\u3060\u3055\u3044"),$("#***overlay").remove(),
!1;var b=h(a+1);$("#***overlay").text("\u96c6\u8a08\u4e2d\u2026 / "+(a+1)+"\u30da\u30fc\u30b8\u76ee");b.done(function(k){f+=k;c(a+1)}).fail(function(){for(var a=alert,b=(new String(f)).replace(/,/g,"");b!=(b=b.replace(/<sup>-?\</sup>+)(\d{3})/,"$1,$2")););a("\u3042\u306a\u305f\u306f\u5408\u8a08"+b+"\u5186\u5206\u306e\u8cb7\u3044\u7269\u3092\u697d\u5929\u5e02\u5834\u3067\u3057\u307e\u3057\u305f\uff01");$("#***overlay").remove()})}function h(a){var b=$.Deferred();l(a).done(function(a){a=$.parseHTML(a);var c=
0;$(a).find(".price").each(function(){var a=$(this).text().match(/[0-9]/g),b=0;null!=a&&(b=Number(a.join("")));c+=b});0===c?b.reject():b.resolve(c)});return b.promise()}function l(a){var b=$.Deferred();$.ajax({url:"[https://order.my.rakuten.co.jp/?page=myorder&fidomy=1&display_month=0&search_item=&page_num="+a+"&search_sender=&search_shop=",success:function(a){b.resolve(a)}});return](https://order.my.rakuten.co.jp/?page=myorder&fidomy=1&display_month=0&search_item=&page_num=%22+a+%22&search_sender=&search_shop=%22,success:function(a)%7Bb.resolve(a)%7D%7D);return) b.promise()}var f=0,e="2012";if("function"!==typeof $){var g=document,d=g.createElement("script");d.src="//ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js";
d.onload=c;g.body.appendChild(d)}else c()})();﻿
－－－以上ブックマークレット

之后移动到乐天购买记录的网站，
[https://order.my.rakuten.co.jp](https://order.my.rakuten.co.jp)
将上述书签粘贴到 URL 栏中，在头上添加 javascript
顺便说一下，我被计算出不到 300 万。 好可怕。

亚马逊也是如此，但出乎意料的是，这种东西通过对现有的东西稍加改进(只是更改类名等)，可以计算出各种各样的东西。
试着做面向各种网站的东西吧。