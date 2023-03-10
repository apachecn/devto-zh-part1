# creamdo widgets para odoo _ parte 1)

> 原文：<https://dev.to/yorodm/creando-widgets-para-odoo-parte-1-15jj>

# 为 odo(第 1 部分)创建小部件。

几个星期的空闲后，我带着一个[odo 10](http://www.odoo.com)回来，因为那里已经有很多网站专门解释从*后端*可以用 **Python** 做什么的原因和原因，所以我将集中精力处理

在本文中，我们将创建一个新的*小部件*来处理视图(MV*的 v 部分)中的整数和浮点字段。虽然这可能看起来是一个简单的例子，但它源于以明确、简单和用户友好的方式提供有限范围内的数字字段的实际需要。

## 开始项目。

为了能够重用构件，我们将创建 *addon* ，其中包含使其运行所需的库和代码。让我们先创建模块框架:

```
$ odoo-bin scaffold widget-slider 
```

Enter fullscreen mode Exit fullscreen mode

我们添加了对 add on 的描述，将其定义为不可安装，放置了有效的类别，并将* *web* 作为依赖项。继续前进。

## 外部图书馆

我们的[小部件只有一个外部依赖项:](https://github.com/seiyria/bootstrap-slider/)[引导滑块](https://github.com/seiyria/bootstrap-slider/)。我们通常会使用 **Bower** 或 **NPM** 来处理项目依赖项 **Javascript** ，但对于如此小的东西，则不需要，只需从库中下载版本即可

为了使这些文件在后端上可用，我们将扩展 _ web . assets _ 后端 _。修改自行生成的文件 *templates.xml*

```
<template id="assets_backend"
       name="web_widget_slider_assets"
       inherit_id="web.assets_backend">
        <xpath expr="." position="inside">
            <link rel="stylesheet"
                href="/widget-slider/static/lib/css/bootstrap-slider.css"/>
            <script type="text/javascript"
                src="/widget-slider/static/lib/js/bootstrap-slider.js"/>
        </xpath>
    </template> 
```

Enter fullscreen mode Exit fullscreen mode

## Javascript dentro de Odoo(para novatos)。

我们已经有了从我们的 *widget* 开始的必要条件，现在只剩下编写初始化外挂程式所需的程式码了。

在**odo**中使用 **Javascript** 首先需要知道的是，在 release 10 中使用了与**要求 js** 非常相似的模块概念。假设我们有一个模组*【验证】*，其中我们包括了用于剩馀*附件的公用程式，然后汇出功能*【check _ int】*。* 

```
odoo.define('validar.enteros, function(require){
    "use strict";

    var check_int = function(val){
        if(!Number.isInteger(val)){
            throw new TypeError("El valor no es entero");
        }
        else {
            return val;
        }
    };
    return {
        check_int: check_int
    };
}); 
```

Enter fullscreen mode Exit fullscreen mode

在另一个 *addon* (或干脆另一个模块)中，我们会有类似于:
的东西

```
odoo.define(otro_addon.otro_servicio', function(require){

    valida_enteros = require('web_validar.enteros').check_int;
    // Resto del código
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果你超过*后端*把这一切想成:

1.  **Javascript** 的一个版本
**   宣告相依性的可靠方式，不论档案的载入顺序为何。*

 *该系统的缺点是，它是访问‘t1’框架提供的组件的唯一途径，因此经常需要围绕简单的功能创建‘T2’包装器’。

## 类、继承、小部件

另一个需要考虑的问题是面向对象的编程。类、对象和继承等熟悉的概念在 **Javascript** 中都有一个重点。**odo**的开发者遵循 **BackboneJS** 实施[John resig](http://ejohn.org/)传承模式的想法

1.  班级是通过继承 *Class* 或其任何女儿来定义的。
2.  *extend()* 用于继承类，作为参数接受对象(或相同的词典)。
3.  *init()* 充当构造函数。
4.  *包括()*允许修改班级(至*猴子补丁*
5.  使用 *extend()* 或 *include()* 时，每种重新定义的方法都可以使用*。_super()* 以存取原始实作。

以气味代码
为例

```
// Definimos una clase Person
 var Person = Class.extend({
  init: function(isDancing){
     this.dancing = isDancing;
    },
  dance: function(){
     return this.dancing;
    }
 });

// Y ahora una clase Ninja
 var Ninja = Person.extend({
  init: function(){
    this._super(false);
  },
  swingSword: function(){
    return true;
  }
 }); 
```

Enter fullscreen mode Exit fullscreen mode

按照这种模式，每个构件是从*web . widget _ 和其他 _mixin* 继承而来的类。

## 创建整数值滑块。

与其从头开始创建我们的 *slider* ，不如利用有一个类来处理数字领域并从中继承。我们创建了文件*【static/src/js/widget _ slider . js】*，内容如下。

```
odoo.define('web_slider.form', function(require){
    "use strict";

    var core = require('web.core');
    var FieldFloat = require('web.form_widgets').FieldFloat;

    // Heredamos de esta clase para aprovechar toda la
    // maquinaria de inicialización.
    var FieldSlider = FieldFloat.extend({
        // Método que se invoca cuando se va a mostrar
        // el widget
        initialize_content: function(){
            this._super();
            this.wrapped = this.$el.slider(this.options);
            // Desabilitar el slider si está en modo
            // solo lectura
            if (this.get("effective_readonly")){
                this.wrapped.slider("disable");
            }
        },

    });
    // Registramos nuestro widget como disponible para
    // las vistas de formulario
    core.form_widget_registry
        .add('slider', FieldSlider);
    return {
        FieldSlider : FieldSlider
    };
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们修改我们的“*assets*模板以加载此文件。

```
<script type="text/javascript"
                src="/widget-slider/static/src/js/widget-slider.js"/> 
```

Enter fullscreen mode Exit fullscreen mode

安装 addon，激活开发人员模式，并尝试通过向声明中添加“*widget = slider*”来更改表单视图中的任何字段。

## 总结。

创建新的“*widget*”不是一个复杂的过程，每个 _framework_ 组件的设计都是为了使扩展、修改或添加功能对于任何熟悉“**JavaScript**的人来说都是一个简单的过程。

尽管如此，尽管有一份官方指南，但它一点也不完整，而且在大多数情况下，问题的唯一解决办法是使用源代码，直到达到我们所寻求的目的。**