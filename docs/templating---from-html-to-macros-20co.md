# 模板-从 html 到宏

> 原文：<https://dev.to/htmlandbacon/templating---from-html-to-macros-20co>

每当我与其他开发人员配对时，我们似乎总是会碰到类似的话题；抽象。

这个问题有明确的答案，因为它需要代码的上下文和应用程序的状态。

过度抽象的代码会使修改变得困难和难以理解，而没有抽象会导致膨胀和重复。

下面是一个[文本输入](https://govuk-elements.herokuapp.com/form-elements/#ni-number)表格 GOV.UK 元素的例子。

下面的例子使用了[双节棍](https://mozilla.github.io/nunjucks/templating.html)。

```
<div class="form-group">
    <label class="form-label" for="ni-number">
        National Insurance number
        <span class="form-hint">
            It's on your National Insurance card, benefit letter, payslip or P60.
      </span>
    </label>
    <input class="form-control" id="ni-number" type="text" name="ni-number">
</div> 
```

Enter fullscreen mode Exit fullscreen mode

表单元素似乎总是我们应该做抽象的一个非常清楚的例子——它们有固定的结构，通常在一个站点上被多次使用。

当我们考虑制作一个可重用的表单元素时，我们考虑的是 HTML 的组成。

有几种方法可以解决这个问题，第一种方法是让代码的每个部分都有自己的宏——这意味着每个部分都可以在其他表单元素中使用。

这看起来像这样:

*   形式组包装器
*   标签
*   投入

使用双截棍，我们会得到这样的结果:

```
{% macro input(id, name, value='') %}
<input class="form-control" 
        id="{{ id }}" 
        name="{{ name }}"
        type="text"
        value="{{value}}">
{% endmacro %}

{% macro label(for, label, hint) %}
<label class="form-label" for="{{ for }}">
    {{ label }}
    <span class="form-hint">
        {{ hint }}
    </span>
</label>
{% endmacro %} 
```

Enter fullscreen mode Exit fullscreen mode

这将允许我们像这样编写我们的 UI，

```
{% from "forms.html" import field, label %}

<div class="form-group">
    {{ label('ni-number', 
            'National Insurance number',
            'It\'s on your National Insurance card, bene...') }}

    {{ field('ni-number',
             'ni-number',
                niNumberValue) }}
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这允许组合标签和字段以产生其他表单元素，它们也可以被包装在另一个函数中以产生一个表单元素，我稍后会谈到这一点。我们还可以考虑将表单组 div 移动到一个宏中。

这也可以作为单个宏来完成。

```
{% macro textInput(id, name, label, hint, value) %}
<div class="form-group">
    <label class="form-label" for="{{ id }}">
        {{ label }}
        <span class="form-hint">
            {{ hint }}
        </span>
    </label>
    <input class="form-control" 
        id="{{ id }}" 
        name="{{ name }}"
        type="text"
        value="{{value}}">
</div>
{% endmacro %} 
```

Enter fullscreen mode Exit fullscreen mode

这将前面的两个宏封装在一个调用中，这使得它不太容易组合，但也具有完整的元素。

如果我们需要添加额外的选项，例如样式、输入类型或额外的 HTML 属性，第二种方法会很快变得臃肿。

我们也可以把这两种方法混合起来，尽管当我们开始做多层的时候，我们开始让我们的代码可读性更差，当我们做改变的时候我们需要更加小心。

```
{% macro textInput(id, name, label, hint, value) %}
<div class="form-group">
    {{ label(id, 
            label,
            hint) }}

    <input class="form-control" 
        id="{{ id }}" 
        name="{{ name }}"
        type="text"
        value="{{value}}">
</div>
{% endmacro %} 
```

Enter fullscreen mode Exit fullscreen mode

事后看来，这似乎是正确的平衡，textInput 被封装，但标签可以在不同的宏中重用。

我的最终宏有以下输入

| 名字 | 描述 |
| --- | --- |
| 名字 | 设置输入的名称 |
| 身份证明（identification） | 设置输入的 id 和标签的 |
| 标签 | 设置标签的文本 |
| 暗示 | 在标签中设置提示文本 |
| 价值 | 设置输入的值 |
| 错误 | 设置错误消息 |
| maxlength | 设置输入的最大长度 |
| 班 | 用于样式元素的对象 |

它有各种状态，包括可选字段和错误状态。经过几次迭代后，这似乎是应用程序上下文中正确的抽象层次，尽管我认为将标签移动到它自己的宏中并在元素中被调用还有更多工作要做。

```
{% macro textInput(name, id, label, hint, value, error, maxlength, classes) %}
    {% if not id %}
        {% set id="input-" + name %}
    {% endif %}

    <div id="{{id}}-form" class="form-group{% if error %} form-group-error{% endif %}">
        <label for="{{ id }}">
            <span class="{% if classes.label %}{{ classes.label }}{% else %}form-label-bold{% endif %}">{{ label }}</span>
            {% if hint %}<span class="form-hint">{{hint}}</span>{% endif %}
            {% if error %}<span class="error-message">{{error}}</span> {% endif %}
        </label>
        <input
            class="form-control{% if classes.input %}  {{ classes.input }}{% endif %}"
            id="{{ id }}"
            {% if maxlength %} maxlength="{{maxlength}}"{% endif %}
            name="{{name}}"
            type="text"
            value="{{value}}">
</div> 
```

Enter fullscreen mode Exit fullscreen mode

无论什么时候，当你做出一个深思熟虑的决定，试着做可重用的代码时，在功能性、可读性和维护性方面总会有一个折衷。

保持事物的自包含性，可以在以后以最小的改动重构它。

请随意添加你会怎么做，或者在 [twitter](http://twitter.com/htmlandbacon) 上给我发消息

*   [现场模板渲染](https://codepen.io/htmlandbacon/full/jmoPyP/)
*   [英国政府元素](https://govuk-elements.herokuapp.com/)
*   [双节棍元素](https://www.npmjs.com/package/govuk-elements-nunjucks)