# 控制台对象的乐趣

> 原文：<https://dev.to/duchienvuong/fun-with-console-object>

> 控制台对象提供对浏览器调试控制台(例如 Firefox 中的 Web 控制台)的访问。它的具体工作方式因浏览器而异，但实际上通常会提供一组功能。
> [MDN](https://developer.mozilla.org/en/docs/Web/API/console)的控制台- Web API

以下是您可能不知道的控制台对象的一些功能列表:

1.清空控制台

使用`console.clear()`简单地清除您的输出。

2.样式输出

您可以使用`"%c"`指令为输出
应用 css 样式

```
 console.log('%c Make console greate again!', 'font-size:50px; background:red;') 
```

Enter fullscreen mode Exit fullscreen mode

3.显示表格数据

使用`console.table(object)`将提供的对象显示为表格。

让我们试试:

```
persons = [ { name: 'Hien Vuong', city: 'Ho Chi Minh' }, { name: 'Donald Trump', city: 'New York' }]

console.table(persons) 
```

Enter fullscreen mode Exit fullscreen mode

4.分组在一起

使用`console.group(message)`和`console.groupEnd()`将所有日志分组到下拉列表中。

让我们试试上面的人物对象

```
persons.forEach(p => {
    console.group(); 
    console.log(`This is ${p.name}`);
    console.log(`He comes from ${p.city}`);
    console.groupEnd();
}); 
```

Enter fullscreen mode Exit fullscreen mode

编码快乐！

*最初发布在我的博客上[这里](http://hienvd.com/2017/02/17/Fun-with-Console-object/)T3】*