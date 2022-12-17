# 我们的 5 大电子邮件模板黑客

> 原文：<https://dev.to/sparkpost/our-top-5-email-template-hacks>

今天，我想我会指出一些我在创建模板时最喜欢的技巧。让我们称之为电子邮件模板黑客。我在以前的博客中已经简要地提到了其中的一些，所以今天我将扩展这些想法，并添加一些用例及例子。

### 1。利用 CSS 中的替换字段

第一个技巧是使用[替换字段](https://www.sparkpost.com/blog/template-substitutions-white-labeling/)作为 CSS 值。颜色、字体、高度，几乎任何东西都可以替代。许多电子邮件客户端使用标题样式，而其他客户端则不使用。无论您选择如何处理不同客户之间的电子邮件格式，您都可以利用内嵌替换、头块替换或两者兼而有之。

如果您想在 header 块中插入 CSS，以保持所有模板的标准，有几种不同的方法可以使用。第一种方法是简单地用替换数据替换 CSS 值。例如，如果在

```
a {
  text-decoration:none;
  color:{{a_normal_color}};
} 
```

然后，在转换 JSON 调用中，我会有相应的替换字段:

```
"a_normal_color" : "#378cd2" 
```

这是修改外观和感觉的最简单的方法，而不必翻遍数百个模板并进行更改。这还假设您将这些值保存在服务器上的某个地方，并且可以很容易地检索它们来创建传输 JSON。

构建这个例子，你可以使用一个动态 html 和/或动态文本块。这允许您通过引入可能存储在标准库中的大型 CSS 块来进行大规模的更改。更改存储库中的块，就更改了所有获取动态 html/text 引用的模板。

请记住，dynamic_html/text 块保存在全局 substitution_data 块中，现在是收件人 substitution_data 块。Transmissions dynamic_html 结构中的 CSS 块可能如下所示:

```
"substitution_data": {
 "dynamic_html": {
"css" : "<style type='text/css'> /* resets + globals */
* { 
    margin:0;
    padding:0;
    font-family: arial, sans-serif;
}

body {
    -webkit-font-smoothing:antialiased;
    -webkit-text-size-adjust:none;
    width: 100%;
    height: 100%;
}

img { 
    max-width: 100%; 
}

/* general styles */
body {background-color:#fff;}

a {
  text-decoration:none;
  color:#378cd2;    /* blue */
}
a:hover {color:#82b450; /* green */}

/* body styles */
p {
font-family: arial,helvetica,sans-serif; font-size: 1.2rem; line-height: 1.6rem; color: #3e3e3e; font-weight: normal; margin-top: 0px; margin-bottom: 1.8rem; max-width: 500px;
}

li {
font-family: arial,helvetica,sans-serif; font-size: 1.2rem; line-height: 1.4rem; color: #3e3e3e; font-weight: normal; margin-top: 0px; margin-bottom: .5rem;
}

ul {
padding-left:1.3rem; margin-top:0px; margin-bottom:2rem;
}

h3 {
font-family: arial,helvetica,sans-serif; font-size: 1.3rem; line-height: 1.6rem; color: #378cd2; font-weight: bold; margin-top: 1.1rem; margin-bottom: 1.8rem;
}"}} 
```

这个代码块和你把它放在 HTML 模板本身没有什么不同。现在，当通过从中央存储库获取的传输引用到模板中时，更新变得更加容易。模板本身将使用这个代码块，在 html

<header>块中的某个位置包含以下条目:</header>

```
{{render_dynamic_content(dynamic_html.css)}} 
```

如果你想全力以赴，动态渲染甚至允许递归替换字段。这意味着动态代码块也可以有替换字段。这方面的一个很好的使用案例是当您 OEM 或贴牌生产您的产品。在下面的例子中，CSS 块与上面的相似，但是有 CSS 值的替换字段。这些替换字段然后被放置在传输的全局替换块中。例如:

```
"substitution_data": {
"dynamic_html": {
"css" : "<style type='text/css'> /* resets + globals */
* { 
    margin:{{global_margin}};
    padding:{{global_padding}};
    font-family: {{global_font_family}};
}

body {
    -webkit-font-smoothing:antialiased;
    -webkit-text-size-adjust:none;
    width: {{body_width}};
    height: {{body_height}};
}

img { 
    max-width: {{image_max_width}}; 
}

/* general styles */
body {background-color: {{body_background_color}};}

a {
  text-decoration:none;
  color:{{a_normal_color}}; /* blue */
}
a:hover color:{{a_hover_color}}; /* green */}

/* body styles */
p {
{{p_style}};
}

li {
{{li_style}};
}

ul {
{{ul_style}};
}

h3 {
{{h3_style}};
}"},

"global_margin" : 0,
"global_padding": 0,
"global_font_family" : "arial, sans-serif",
"body_width" : "100%",
"body_height" : "100%",
"image_max_width" : "100%",
"body_background_color" : "#378cd2",
"a_normal_color" : "#378cd2",
"a_hover_color" : "#82b450",
"p_style" : "font-family: arial,helvetica,sans-serif; font-size: 1.2rem; line-height: 1.6rem; color: #3e3e3e; font-weight: normal; margin-top: 0px; margin-bottom: 1.8rem; max-width: 500px",
"li_style" : "font-family: arial,helvetica,sans-serif; font-size: 1.2rem; line-height: 1.4rem; color: #3e3e3e; font-weight: normal; margin-top: 0px; margin-bottom: .5rem",
"ul_style" : "padding-left:1.3rem; margin-top:0px; margin-bottom:2rem",
"h3_style" : "font-family: arial,helvetica,sans-serif; font-size: 1.3rem; line-height: 1.6rem; color: #53e00d; font-weight: bold; margin-top: 1.1rem; margin-bottom: 1.8rem
}, 
```

但是等等，如果我想使用内联 CSS 呢，你会问？好吧，只要改变 CSS 设置的那些大块，让它们成为替换字段。

### 2。如果某些数据不存在，则根本不发送电子邮件

随着电子邮件变得越来越个性化，它们开始看起来和感觉起来像交易邮件。这是一个伟大的趋势，但它为未完成的电子邮件打开了更大的可能性。假设一个工作公告板每天早上向所有活跃成员发送工作机会列表。如果没有匹配的用户，希望有检查和平衡来阻止应用程序请求发送电子邮件，但是作为一个模板设计者，我不想依赖于此。所以我使用的一个小技巧是检查一个必须存在的特定替换字段或数组；如果它不存在，我在模板的某个地方用下面一行调用一个不存在的函数(我倾向于把这个放在底部)。这将自动阻止该电子邮件的生成，从而使不良电子邮件无法发送出去。在下面的例子中，我正在检查 ProductList 数组，如果它不存在，我就用参数“onpurpose”调用函数“crash”。

```
{{if !ProductList}} {{ crash(onpurpose) }}{{end}} 
```

### 3。在为数组创建表之前验证数据

SparkPost 有一个非常强大的特性，它允许模板遍历一个数组来显示一组信息，比如工作、房地产列表或产品。事实上，SparkPost 甚至支持循环中有循环。通常，会创建多个表、行和列，以便以内容创建者希望的方式显示这些信息。但是，如果在其中一个循环中没有数据显示，但您仍然希望电子邮件发出去，只是没有空行或表格，该怎么办呢？在我的许多零售模板中，我显示了一个产品列表，每个产品有“x”个特征。如果没有特性，我仍然希望显示产品，但是我可能不需要为空的特性列表创建一些相应的 html 代码。为了让电子邮件看起来更好，我在构建不必要的表、行或列来存放这些信息之前，会检查数组的存在和值。这个技巧可以通过一个简单的两部分 if 语句来解决:

```
{{ if empty(myarray) or !myarray}} 
```

### 4。在主题行中使用替换域

所以你建立了你的模板并把它放到了 SparkPost 服务器上。不要忘记创建一个动态的主题行。让你的邮件被 Gmail 夹住或隐藏在一大串邮件中的最简单的方法之一就是反复使用相同的主题。因此，继续使用[替换字段](https://www.sparkpost.com/blog/personalize-emails-substitution-data-and-templates/)作为你的主题。我经常把人名和主题放在一起。我在 SparkPost UI 中的主题字段被设置为类似于:

```
{{first_name}}, {{subject}} 
```

### 5。对页眉和页脚使用 dynamic_html

因此，继续标准的主题，我喜欢通过传输 API 以 dynamic_html 条目的形式将我的页眉和页脚发送给每个模板。这使我可以保持一个页眉和页脚库，可以很容易地修改，而不必改变每个模板。因为 SparkPost 无法将这些页眉和页脚存储在服务器上，然后将它们插入到目标模板中，所以在另一个内容管理系统中拥有这些标准页眉和页脚是一个很好的做法，营销人员可以在没有开发帮助的情况下对其进行更改。在构建传输 json 时，将适当的页眉和页脚放入 dynamic_html 部分，然后在发送之前将其放入模板。

### 即将发布的电子邮件模板黑客攻击

这些是我最喜欢的模板技巧。在我的下一篇博客中，我将解决验证传输负载是否包含模板期望接收的所有数据的问题。该博客将附有这个验证步骤的完整 PHP 代码示例。

幸福的结局

*本帖最初发表于[sparkpost.com](https://www.sparkpost.com/blog/email-template-hacks/)T3】*