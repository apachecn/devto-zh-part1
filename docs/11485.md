# 将事件绑定到除特定元素及其所有内容之外的所有元素。

> 原文：<https://dev.to/forforeach/binding-event-to-all-elements-except-specific-element-and-all-its-contents-3c29>

最近，我遇到了一个挑战，除了点击和触摸菜单区域，每次触摸或点击都要关闭导航菜单。我尝试了很多方法来解决它，最后，我找到了对我来说最佳的解决方案。解决方案分为两步，并依赖于事件冒泡。

首先，我在

that on every click/touch closes the menu.

```
function clickOnBody(event){
 // you can close here the menu
 alert(‘clicked everywhere except the menu area’);
}
document.body.addEventListener(‘mousedown’, clickOnBody, false);
document.body.addEventListener(‘touchstart’, clickOnBody, false); 
```

其次，我在一个特定的元素上附加了另一个点击/触摸事件，在这个事件中，我将阻止事件传播(冒泡),这样元素区域内的每一次点击/触摸都将在父元素上停止。

```
function clickOnMenu(event){
 event.stopPropagation();
}
var menu = document.getElementById(‘menu’);
menu.addEventListener(‘mousedown’, clickOnMenu, false);
menu.addEventListener(‘touchstart’, clickOnMenu, false); 
```

您可以在[js dild](http://jsfiddle.net/dima_k/d9JRu/1/)中查看解决方案

* * *