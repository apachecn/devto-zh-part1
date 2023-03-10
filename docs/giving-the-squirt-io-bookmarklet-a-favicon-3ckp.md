# 给 Squirt.io 书签添加一个图标

> 原文：<https://dev.to/adamkdean/giving-the-squirt-io-bookmarklet-a-favicon-3ckp>

今天我学习了关于 [squirt.io](http://www.squirt.io/) 的知识，这是一个开源的类似 Spritz 的小书签，可以帮助你快速阅读互联网。从每分钟 250 字一直到每分钟 950 字。bookmarklet 是一个 javascript 片段，它位于你的“收藏夹栏”上的书签内，允许你在你当前所在的任何网站上执行该脚本。

我主要使用 Chrome，我对 bookmarklets 的唯一不满是它们没有 favicons。作为一个在我的浏览器顶部有 1920 像素的收藏夹图标的人，我不能忍受看到默认的白色图标污染了一行精美的图标。

不过有一种方法可以设置图标！

确保您已经将 bookmarklet 保存到您的书签栏。然后，进入 [squirt.io](http://www.squirt.io/) 并将其加入书签。右键单击书签，转到`Bookmark manager`。在左上角你会看到一个菜单`Organise`。点按它，并将您的书签导出到 HTML 文件。

现在在你最喜欢的文本编辑器中打开它。我用的是 Sublime。搜索 squirt.io，您应该会在文件底部附近找到一行。它将有一个指向 squirt.io 的`HREF`，一个`ADD_DATE`，更重要的是，一个`ICON`属性。

```
<DT><A HREF="http://www.squirt.io/" ADD_DATE="1394700500" ICON="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAABRElEQVQ4jZXSPUtcURAG4MeY+BVi0EJsBD8gRZogsk0Eu8XKRiSQwgj5CbYRLGyTKk0gTZoUsfCjEMFS/4EgRME2WwXUoEHN7qa4czfXu3dZM3A458y8874z5wzF1hH7BzzP+dpaCiyjjvW4P/gfgl4coRokC/clSQErkXiFW5yiX5s20uAozoMgu9ZyuJbqnyOhgi/YilYuMNaKJHU8w298x3gmPhekn3JiTeofA/imQGkT1xjJxtLEGgbwKs4/IjaMoTgf4xFe5wk6w1HOgF8GUR+6I16KpPnA1NLSHsa+KnmsJclP9GTKfxK+97jE43zvIkDy6rN4ireYwSSmsI1f+JPJazzUi6igEv3CLpYxIZmNM3zL5d25fJX8QhU7OMEB9v0bqFJB9Q2SQexpnsJ6lL5YpJ63LrzDIW7wExuYLlL+C3jqSJn7EH8tAAAAAElFTkSuQmCC">Squirt</A> 
```

在这个书签上方，您应该会看到您的 bookmarklet，其中的`HREF`以`javascript:`开始。注意它为什么没有一个`ICON`属性集？好吧，那就这样吧。从 squirt.io 书签中提取出`ICON`属性，注入到 bookmarklet 中，像这样:

```
<DT><A HREF="javascript:(function(){if(window.sq){window.sq.closed&&window.document.dispatchEvent(new Event('squirt.again'));}else{window.sq={};window.sq.userId='63c3f43e-7a2e-4652-ae1b-c8b309fbad5f';s=document.createElement('script');s.src='http://www.squirt.io/bm/squirt.js';s.s=window.location.search;s.idx=s.s.indexOf('sq-dev');if(s.idx!=-1){s.ampIdx=s.s.indexOf('&');s.host=s.s.substring(s.idx+7,s.ampIdx==-1?s.s.length:s.ampIdx);s.src='http://'+(s.host?s.host:'localhost')+':4000/bm/squirt.js';}document.body.appendChild(s);}})();" ADD_DATE="1394699165" ICON="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAABRElEQVQ4jZXSPUtcURAG4MeY+BVi0EJsBD8gRZogsk0Eu8XKRiSQwgj5CbYRLGyTKk0gTZoUsfCjEMFS/4EgRME2WwXUoEHN7qa4czfXu3dZM3A458y8874z5wzF1hH7BzzP+dpaCiyjjvW4P/gfgl4coRokC/clSQErkXiFW5yiX5s20uAozoMgu9ZyuJbqnyOhgi/YilYuMNaKJHU8w298x3gmPhekn3JiTeofA/imQGkT1xjJxtLEGgbwKs4/IjaMoTgf4xFe5wk6w1HOgF8GUR+6I16KpPnA1NLSHsa+KnmsJclP9GTKfxK+97jE43zvIkDy6rN4ireYwSSmsI1f+JPJazzUi6igEv3CLpYxIZmNM3zL5d25fJX8QhU7OMEB9v0bqFJB9Q2SQexpnsJ6lL5YpJ63LrzDIW7wExuYLlL+C3jqSJn7EH8tAAAAAElFTkSuQmCC">Squirt</A> 
```

现在保存文件，回到 Chrome 的书签管理器，导入文件。你的书签将会很漂亮，世界将会变得美好。再次感谢 [Cameron Boehmer](https://twitter.com/camron) 提供了这个神奇的书签，也感谢[可读性](https://www.readability.com/)和 [Spritz Inc](http://www.spritzinc.com/) 的贡献！