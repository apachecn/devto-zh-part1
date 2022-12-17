# 我需要 jQuery

> 原文：<https://dev.to/munieru_jp/i-need-jquery-51dj>

你说 **[“不需要 jQuery”](https://github.com/nefe/You-Dont-Need-jQuery)**已经很久了，但是在用户脚本等中做 DOM 操作还是有用的。<sup id="fnref1">T5 1T7】</sup>

## 获取元素

在 native 中，元素 getter 方法的种类太多，名称太长。
在 jQuery 中，`$()`就是全部。

### 通过选择器获取元素

#### 原生

```
const viewport = document.querySelector('meta[name=viewport]'); 
```

Enter fullscreen mode Exit fullscreen mode

#### jQuery

```
const $viewport = $('meta[name=viewport]'); 
```

Enter fullscreen mode Exit fullscreen mode

### 通过选择器获取元素

#### 原生

```
const externalLinks = document.querySelectorAll('a[target=_blank]'); 
```

Enter fullscreen mode Exit fullscreen mode

#### jQuery

```
const $externalLinks = $('a[target=_blank]'); 
```

Enter fullscreen mode Exit fullscreen mode

### 按类获取元素

#### 原生

```
const articles = document.getElementsByClassName('article'); 
```

Enter fullscreen mode Exit fullscreen mode

#### jQuery

```
const $articles = $('.article'); 
```

Enter fullscreen mode Exit fullscreen mode

### 按 id 获取元素

#### 原生

```
const main = document.getElementById('main'); 
```

Enter fullscreen mode Exit fullscreen mode

#### jQuery

```
const $main = $('#main'); 
```

Enter fullscreen mode Exit fullscreen mode

### 按名称获取元素

#### 原生

```
const buttons = document.getElementsByName('button'); 
```

Enter fullscreen mode Exit fullscreen mode

#### jQuery

```
const $buttons = $('[name=button]'); 
```

Enter fullscreen mode Exit fullscreen mode

### 通过标签名称获取元素

#### 原生

```
const links = document.getElementsByTagName('a'); 
```

Enter fullscreen mode Exit fullscreen mode

#### jQuery

```
const $links = $('a'); 
```

Enter fullscreen mode Exit fullscreen mode

## 设置样式

在 native 中，不能一次为元素设置多个样式。
在 jQuery 中，`jQuery.css()`可以那个。

### 原生

```
element.style['background-color'] = '#000000';
element.style['color'] = '#ffffff'; 
```

Enter fullscreen mode Exit fullscreen mode

### jQuery

```
$element.css({
  'background-color': '#000000',
  'color': '#ffffff'
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 从 HTML 字符串创建元素

在 native 中，您需要使用长拼写来从 HTML 字符串创建元素。
在 jQuery 中，`$()`可以那个。

### 原生

```
const foo = document.createRange().createContextualFragment(`<div id="foo">Foo</div>`); 
```

Enter fullscreen mode Exit fullscreen mode

### jQuery

```
const $foo = $(`<div id="foo">Foo</div>`); 
```

Enter fullscreen mode Exit fullscreen mode

## 设置显示状态

在 native 中，您需要重写 display 属性的值来更改元素的显示状态。
在 jQuery 中，有几种直观的方法。

### 显示元素

#### 原生

```
element.style.display = 'block';  //or 'inline', 'inline-block', 'inline-table'... 
```

Enter fullscreen mode Exit fullscreen mode

#### jQuery

```
$element.show(); 
```

Enter fullscreen mode Exit fullscreen mode

### 隐藏元素

#### 原生

```
element.style.display = 'none'; 
```

Enter fullscreen mode Exit fullscreen mode

#### jQuery

```
$element.hide(); 
```

Enter fullscreen mode Exit fullscreen mode

### 切换显示

#### 原生

```
const elementIsShowing = element.ownerDocument.defaultView.getComputedStyle(element, null).display !== 'none';
element.style.display = elementIsShowing ? 'none' : 'block';  //or 'inline', 'inline-block', 'inline-table'... 
```

Enter fullscreen mode Exit fullscreen mode

#### jQuery

```
$element.toggle(); 
```

Enter fullscreen mode Exit fullscreen mode

## 其他语言

*   [日语](https://qiita.com/munieru_jp/items/69b15e6344186206abe8)

* * *

1.  在我自己的应用程序中，我将使用 Vue.js 等。 [↩](#fnref1)