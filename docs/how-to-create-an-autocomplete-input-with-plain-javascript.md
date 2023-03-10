# 如何用普通 Javascript 创建自动完成输入

> 原文：<https://dev.to/stephenafamo/how-to-create-an-autocomplete-input-with-plain-javascript>

现在，大多数建立网站的人都包含了表单。常规文本元素、数字、选择(下拉菜单)、复选框和单选按钮。

## 问题

我经常遇到的一种情况是，我们需要用户从很长的选项列表中进行选择。最简单的方法是将所有选项放入一个 select 元素中。然而，这是不切实际的，因为...

1.  这会导致非常糟糕的用户体验
2.  如果所有的选项都很大(数以千计)，那么就没有实际的理由将所有这些都加载到 select 选项中。

## 一解

自动完成的表单。我们可以有一个庞大的列表，只显示那些与用户相关的内容。我们不需要将所有的结果加载到 HTML 中，我们可以在用户输入时简单地加载我们需要的结果。

[![](img/2b3ae0fb6565c3250b554124cffd5c0d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--odR9X5Fy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stephenafamo.com/autocomplete.png)

这是许多网站使用的技术，那里有很多选项可供选择。电子商务，市场，分类广告，社交媒体，等等

看起来实现很复杂，尤其是像 [typeahead.js](https://github.com/twitter/typeahead.js/) 这样的库，然而，它实际上相当简单。

## 一个实现

### 首先，我们的 html 元素

`index.html`

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    
        Demo auto complete
    
</head>
<body>
    <h2>The form</h2>
    <form>
        <input type="text" name="name" id="name_input">Name
        <br/>
        <input type="submit">
    </form>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

为了创建一个自动完成的表单，我们将使用 HTML5 [`datalist`](https://developer.mozilla.org/en/docs/Web/HTML/Element/datalist) 标签，所以我们将其修改如下。

`index.html`

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    
        Demo auto complete
    
</head>
<body>
    <h2>The form</h2>
    <form>
        <input type="text" name="name" id="name_input" list="huge_list">Name
        <datalist id="huge_list">
        </datalist>
        <br/>
        <input type="submit">
    </form>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### 用 AJAX 自动填充 Javascript

我们要做的是，我们将检查用户何时在我们的字段中键入内容，然后仅用必要的内容填充 datalist 元素。代码中添加了注释

`index.js`

```
window.addEventListener("load", function(){

    // Add a keyup event listener to our input element
    var name_input = document.getElementById('name_input');
    name_input.addEventListener("keyup", function(event){hinter(event)});

    // create one global XHR object 
    // so we can abort old requests when a new one is make
    window.hinterXHR = new XMLHttpRequest();
});

// Autocomplete for form
function hinter(event) {

    // retireve the input element
    var input = event.target;

    // retrieve the datalist element
    var huge_list = document.getElementById('huge_list');

    // minimum number of characters before we start to generate suggestions
    var min_characters = 0;

    if (input.value.length < min_characters ) { 
        return;
    } else { 

        // abort any pending requests
        window.hinterXHR.abort();

        window.hinterXHR.onreadystatechange = function() {
            if (this.readyState == 4 && this.status == 200) {

                // We're expecting a json response so we convert it to an object
                var response = JSON.parse( this.responseText ); 

                // clear any previously loaded options in the datalist
                huge_list.innerHTML = "";

                response.forEach(function(item) {
                    // Create a new <option> element.
                    var option = document.createElement('option');
                    option.value = item;

                    // attach the option to the datalist element
                    huge_list.appendChild(option);
                });
            }
        };

        window.hinterXHR.open("GET", "/query.php?query=" + input.value, true);
        window.hinterXHR.send()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在服务器上，我们将监听并返回一个 JSON 格式的值数组。PHP 脚本的一个例子是。

`query.php`

```
<?php
$query = $_GET['query'];

// These values may have been gotten from a database.
// We'll use a simple array just to show this example.
$values = ['Neo',
            'Ibiyemi',
            'Olayinka',
            'Jonathan',
            'Stephen', 
            'Fisayo', 
            'Gideon',
            'Mezie',
            'Oreoluwa', 
            'Jordan', 
            'Enkay', 
            'Michelle', 
            'Jessica'];

if ($query) {
    foreach ($values as $key => $value) {
        if (stripos($value, $query) === false) {
            unset($values[$key]);
        }
    }
}

echo json_encode(array_values($values));
?> 
```

Enter fullscreen mode Exit fullscreen mode

### 验证

这种方法的一个问题是，没有默认的方法来确保输入来自列表。为了解决这个问题，我们将为 submit 动作设置一个自定义函数，如果在列表中找不到输入的值，就阻止提交。

中的

 **`index.html`

*变*

```
<h2>The form</h2>
<form>
    <input type="text" name="name" id="name_input" list="huge_list">Name 
```

Enter fullscreen mode Exit fullscreen mode

*到*

```
<h2>The form</h2>
<form onsubmit="return validateForm()">
    <input type="text" name="name" id="name_input" list="huge_list">Name 
```

Enter fullscreen mode Exit fullscreen mode

中的

 **`index.js`

*添加*

```
function validateForm(){

    // Get the input element
    var input = document.getElementById('name_input');
    // Get the datalist
    var huge_list = document.getElementById('huge_list');

    // If we find the input inside our list, we submit the form
    for (var element of huge_list.children) {
        if(element.value == input.value) {
            return true;
        }
    }

    // we send an error message
    alert("name input is invalid")
    return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！！！

现在，如果用户试图提交一个无效的名字，他会看到一条错误消息。

所有示例代码都可以在[这个 GitHub 库](https://github.com/stephenafamo/javascript-autocomplete-demo)中找到****