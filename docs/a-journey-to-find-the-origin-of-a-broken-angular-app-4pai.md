# 寻找一个破碎的有棱角的应用程序的起源的旅程

> 原文：<https://dev.to/callmemagnus/a-journey-to-find-the-origin-of-a-broken-angular-app-4pai>

最近，我收到警报，我们网站上的一个表单行为不当，无法工作。我们得到报告说提交被“忽略”,参数在地址栏结束。它开始看起来像一些 javascript 爆炸。

DynaTrace 能够提供一些信息，帮助我们确定调查方向。

## 收集到的错误

我们开始在 Web 请求部分搜索症状(url 中的参数)。由于我们只对一小部分用户进行了抽样，并不是所有存储的 web 请求都与客户端收集的数据相关联。由于在这种情况下 DynaTrace 的搜索/过滤能力很差，因此创建链接需要手动操作。

我们能够收集到一些与不当行为有关的客户端错误。

有趣的是，与其他错误收集工具一样，DynaTrace 按原样保存堆栈，这意味着消息是本地化的。

**加载错误**

```
TypeError: L'objet ne gère pas la propriété ou la méthode << load >>
   at Anonymous function (Unknown script code:1:79)
   at Global code (Unknown script code:1:2)
   at Anonymous function (https://www.googletagmanager.com/gtm.js?id=GTM-XXXX&l=gtmDataObject:79:380)
   at bi (https://www.googletagmanager.com/gtm.js?id=GTM-XXXXXX&l=gtmDataObject:81:188)
   at zf (https://www.googletagmanager.com/gtm.js?id=GTM-XXXXXX&l=gtmDataObject:49:38)
   at Anonymous function (https://www.googletagmanager.com/gtm.js?id=GTM-XXXX&l=gtmDataObject:120:792)
   at Fk (https://www.googletagmanager.com/gtm.js?id=GTM-XXXXXX&l=gtmDataObject:116:192)
   at Tk (https://www.googletagmanager.com/gtm.js?id=GTM-XXXXXX&l=gtmDataObject:120:1565)
   at gg (https://www.googletagmanager.com/gtm.js?id=GTM-XXXXXX&l=gtmDataObject:121:201)
   at tg (https://www.googletagmanager.com/gtm.js?id=GTM-XXXXXX&l=gtmDataObject:60:128) 
```

有人在未定义的对象上使用了`load`。

**访问被拒绝**

```
Error: Accès refusé.

   at Anonymous function (https://www.example.com/libs.js?v=16.14.14:10:24895)
   at Global code (https://www.example.com/libs.js?v=16.14.14:10:24864) 
```

这是一个棘手的问题，见下文

**配额超过**

```
Error: Mémoire insuffisante pour cette opération.

   at Anonymous function (https://www.example.com/libs.js?v=16.14.14:10:24895)
   at Global code (https://www.example.com/mosaic/libs.js?v=16.14.14:10:24864) 
```

法语和荷兰语版本的英文“Quota exceeded”错误变成了“此操作内存不足”，这显然更好。

这与`localStorage`或`sessionStorage`受限有关参见[维基百科网络存储](https://en.wikipedia.org/wiki/Web_storage)。

*角度误差*

```
Error: [$injector:modulerr] Failed to instantiate module userIsLogged due to:
Error: [$injector:modulerr] Failed to instantiate module RequestUtils due to:
Error: [$injector:nomod] Module 'RequestUtils' is not available! You either misspelled the module name or forgot to load it. If registering a module ensure that you specify the dependencies as the second argument.
http://errors.angularjs.org/1.2.28/$injector/nomod?p0=RequestUtils
   at Anonymous function (https://www.example.com/libs.js?v=16.14.14:2:9891)
   at e (https://www.example.com/libs.js?v=16.14.14:2:9564)
   at Anonymous function (https://www.example.com/libs.js?v=16.14.14:2:9811)
   at Anonymous function (https://www.example.com/libs.js?v=16.14.14:2:23748)
   at w (https://www.example.com/libs.js?v=16.14.14:2:1663)
   at r (https://www.example.com/libs.js?v=16.14.14:2:23683) 
```

嗯...我们处理表单的 angular 应用程序的一部分抱怨注入的模块是`undefined`。但是为什么呢？被注入的模块是`undefined`，整个表单逻辑死了，被简化为带有默认行为的 HTML 标签。

表单行为不当的原因现在很清楚了:表单标记既没有默认的方法也没有动作，单击 submit 的结果是当前页面被查询字符串中的表单参数再次请求。

我现在必须能够在本地重现问题，以便进一步调查并修复它。

## 再现失败

为了重现，如果可能的话，我们必须隔离浏览器、页面、动作，试图找到导致表单失败的非平凡事件的祸根。

表单行为不当的原因现在很清楚了:表单标记既没有默认的方法也没有动作，提交的结果是当前页面被查询字符串中的表单参数再次请求。

第一个错误(load)被排除在研究之外，因为它发生在一个单独的内联脚本上，因此不会破坏页面的其余脚本。

“拒绝访问”故障主要与 IE (Trident 和 Edge)有关。

“超过限额”影响了几乎所有浏览器。

最后两个错误与 Web 存储有关，在`sessionStorage`和`localStorage`中实现。

我知道几个浏览器(例如 Safari 在私人模式下)的问题只是破坏了存储。我们使用了类似下面的简化示例:

```
 var tools = (function() {
  var storage = window.localStorage;

  try {
    storage.setItem('test', 'test');
    storage.removeItem('test');
    return storage;
  }
  catch(e) {
    return {
      getItem: function(key) {},
      setItem: function() {},
      // you get the picture
    }
  }
})(); 
```

这涵盖了大多数问题:

1.  当网络存储被禁用时(则值为`null`)；
2.  当`setItem`抛出错误时(如 Safari 私密模式)；
3.  当`setItem`抛出配额错误时(大多数浏览器)

但有一个不是: **IE 有时在你“提及”`window.localStorage`** 时抛出“拒绝访问”。抛出的行`var storage = window.localStorage;`导致文件停止执行。

只是需要找到时间。

深入挖掘 IE/黑暗面，我发现可以通过策略完全禁用网络存储。对于感兴趣的，你可以在 windows 中用下面的命令行来做(见 [windows 保护模式](https://msdn.microsoft.com/en-us/library/bb250462(v=vs.85).aspx)):

```
icacls %userprofile%\Appdata\LocalLow /t /setintegritylevel (OI)(CI)M 
```

最后，我们能够重现与报告的故障类似的故障。

修正是增强 Web 存储周围的包装器，以便捕捉所有故障情况，并从`localStorage`到`sessionStorage`再到`dummyStorage`(存储在`window`)。

```
/**
 * The aim of this is to expose safe localStorage and sessionStorage
 * The cases are:
 * - "Access Denied" on mention of window.localStorage (IE in secure mode)
 * - null returned when requesting window.localStorage or window.sessionStorage (DOM storage disabled)
 * - error on usage of .setItem (e.g. Safari sometimes or Quota exceeded)
 */
/**
 * yes, this dummy storage does not expose the complete Storage API but it
 * should suite most of our use-cases
 * @returns {Storage}
 */
var dummyStorage = function(w) {
  var localWindow = w || window;
  var keyName = '__dummyStorage__';
  localWindow[keyName] = localWindow[keyName] || {};
  return {
    getItem: function(id) {
      return localWindow[keyName][id] || null;
    },
    setItem: function(id, value) {
      localWindow[keyName][id] = value;
    },
    removeItem: function(id) {
      delete localWindow[keyName][id];
    }
  };
};
/**
 * @returns {Storage}
 */
var safeSessionStorage = function(w) {
  var localWindow = w || window;
  var now = Date.now();
  try {
    localWindow.sessionStorage.setItem('test-' + now, '1234');
    localWindow.sessionStorage.removeItem('test-' + now);
    return localWindow.sessionStorage;
  }
  catch (e) {
    return dummyStorage(localWindow);
  }
};
/**
 * @returns {Storage}
 */
var safeLocalStorage = function(w) {
  var localWindow = w || window;
  var now = Date.now();
  try {
    localWindow.localStorage.setItem('test-' + now, '1234');
    localWindow.localStorage.removeItem('test-' + now);
    return localWindow.localStorage;
  }
  catch (e) {
    // this will catch any error
    return safeSessionStorage(localWindow);
  }
}; 
```

## 总结起来

这次调查的结果

1.  始终确保您对代码的默认 HTML 行为感到满意，以防 javascript 失败(即使没有 javascript 也能工作！)
2.  使用浏览器提供的功能时要非常小心——事后再说很容易；-)
3.  在生产中，你的脚本中的问题比你想象的要多得多，只要有一个度量标准，就有一些简单的工具来收集它。
4.  第三方虽然不总是罪魁祸首，但在 bug 搜索团队中引入了许多噪音(加载错误是第三方脚本不太谨慎)

## 参考文献

*   [维基百科网络存储](https://en.wikipedia.org/wiki/Web_storage)
*   [windows 保护模式](https://msdn.microsoft.com/en-us/library/bb250462(v=vs.85).aspx)
*   [始终捕捉本地存储...](http://crocodillon.com/blog/always-catch-localstorage-security-and-quota-exceeded-errors)
*   [使用网络存储 MDN](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API)