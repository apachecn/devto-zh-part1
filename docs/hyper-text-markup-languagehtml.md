# 超文本标记语言(html)

> 原文：<https://dev.to/bristi123/hyper-text-markup-languagehtml>

什么是 HTML

html 代表用于创建网页和网络应用程序的超文本标记语言。与层叠样式表(CSS)和 javascript 一起，它形成了万维网的三元组 coenerstone 技术。WEB 浏览器从 Web 服务器或本地存储器接收 HTML 文档，并将其呈现为多媒体网页，它还在语义上描述网页的结构，并且最初包括文档外观的提示

在 html 的帮助下，我们可以构建图像和其他对象，例如可以嵌入到渲染页面中的交互式表单。它提供了一种通过表示文本的结构语义来创建结构化文档的方法，例如标题、段落、列表、链接、引用和其他 items.Html 元素由使用括号编写的标签来描述。  等
标签，直接将内容引入页面。其他如

...

surround and provide information about document text and may include other tagsas sub-elements. Browsers do not display the HTML tags,but use them to interpret the content of the page.

##以下是一些 html 代码示例:

***到达时间***
参见@[codepen](https://codepen.io/)by Bristi Bal->[到达时间](https://codepen.io/bristi123/pen/prZyPq)

```
 <html>
<body>
<table border="1">
<tr><th>Source</th><th>Destination</th><th>Arrival Time</th></tr>
<tr><td>Howrah</td><td>Ranchi</td><td>19:00</td></tr>
</table>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

***到达时间***
参见@[codepen](https://codepen.io/)by Bristi Bal->[Arrival Time](https://codepen.io/bristi123/pen/vJaGgg)

```
<html>
<body>
<table border="1">
<tr><th>Source</th><th>Destination</th><th>Arrival Time</th></tr>
<tr><td>Ranchi</td><td>Howrah</td><td>3:00</td></tr>
</table>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

***感谢页面***
参见@[codepen](https://codepen.io/)by Bristi Bal->[感谢](https://codepen.io/bristi123/pen/ZJjWOP)

```
<!DOCTYPE>
<html>
<head>
cancel page
<size="12">
</head>
<body>
<p>Thank You.</p>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

***一个简单的登录页面:***
参见@[codepen](https://codepen.io)by Bristi Bal->[log in page](https://codepen.io/bristi123/pen/BdPjgZ)

```
<!DOCTYPE>
<html>
<body>
<label for ="email">Email-Id:<br/><input type="email" value""name="emailid"size="30"
placeholder="Enter a valid email address"><br/><br/>
<label for="password">Password:<br/><input type="text" value="" name="pswrd"size="30"
maxlength="10"placeholder="Enter a valid password" pattern="[0-9]{10}">
<button><a href="cancel.html">Cancel</a></button>
<button><a href="submit.html">Submit</a></button>
<br/><br/>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

***显示列车时刻表:***
参见@[codepen](https://codepen.io)by Bristi Bal->[列车时刻表](https://codepen.io/bristi123/pen/QMBNwr)

```
<!DOCTYPE>
<html>
<body>
<table border="1">
<tr><th>Train no.</th><th>Train name</th><th>Source</th><th>Destination</th></tr>
<tr><td><a href="18628.html"><18628/a></td>
<td>RcHw</td><td>Ranchi</td><td>Howrah</td></tr>
<tr><td><a href="18627.html"><18627/a></td>
<td>HwRc</td><td>Howrah</td><td>Ranchi</td></tr>
</table>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode