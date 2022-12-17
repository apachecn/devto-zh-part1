# Mithril.js 的初学者尝试

> 原文：<https://dev.to/andrewchou/a-beginners-attempt-at-mithriljs-6km>

大约半年前我开始自学 web 开发，我创建的第一个“从零开始”的前端项目之一是一个彩色按钮。基本上，输入一个有效的颜色名称(大部分可以在这里找到)或十六进制值，点击按钮后，它的颜色会变成输入的颜色。

我想出这个主意，作为在 JavaScript 中使用事件监听器的一个练习，因为我当时在使用它们时遇到了麻烦，所以我在创建它时专注于使用普通的 JavaScript。结果如下:

[https://codepen.io/andrew_chou/embed/mMNerL?height=600&default-tab=result&embed-version=2](https://codepen.io/andrew_chou/embed/mMNerL?height=600&default-tab=result&embed-version=2)

这非常简单，而且我确信使用普通 JS 有更有效的方法来做到这一点(注意:自从我完成它之后，我还没有试图改变它)。

作为初学者，很容易被现有的所有前端框架所淹没。我想“升级”，尝试一些不熟悉但又不太陌生的东西。我在寻找能够引入更先进概念的东西，但又不会偏离我已经理解的东西太远。此外，我对极简框架和库非常着迷，所以在寻找可以尝试的东西时，这也是一个考虑因素。

于是我找到了 [Mithril.js](https://mithril.js.org) 。众所周知它是极其微小的(< 8kb gzip！)并且它有一个简单的 API，可以像[反应](https://reactjs.org/)一样使用。它的文档中包含的关于如何使用 Mithril 的内容肯定比它实际的 API 要多，所以我向初学者强烈推荐它。

所以在这篇文章的剩余部分，我基本上要重写我之前做的颜色按钮——使用 Mithril。请随意使用 [CodePen](https://codepen.io) 或您喜欢的任何沙盒工具进行操作！

### 第一步:创建一些组件

如果你熟悉 React，那么你就会明白我说的我们可以把这个应用程序的每个元素看作是一个*组件*的意思。输入、颜色按钮(也称为大按钮)和重置按钮都是一个组件，当它们放在一起时，就构成了页面的内容。Mithril 中的组件基本上只是带有`view`属性的对象，这是一个返回一些标记节点的函数。例如，让我们首先为输入创建一个组件:

```
const InputComponent = {
  view: function() {
    return m("div", "This is the input container")
  }
};

// The view function is essentially returning this HTML element:

// <div>This is the input container</div> 
```

Enter fullscreen mode Exit fullscreen mode

`view`中的函数返回的是 Mithril 称之为 *vnode* 的东西，本质上是一个 HTML 元素。`m()`函数是一个 hyperscript 函数，它允许用 JavaScript 语法编写任何 HTML 结构——所以在这种情况下，第一个参数表示元素的类型(T2 ),第二个参数是元素中包含的文本。

现在，输入组件只包含我用于样式目的的容器元素。为了添加必要的元素，我们可以将元素嵌套到这个`div`中，如下所示:

```
const InputComponent = {
  view: function() {
    return m("div", { id: "input" }, [
    m("label", "input color: "),
    m("input", {
        id: "color-input",
        type: "text",
        onkeydown: submit,
        autofocus: "autofocus"
      })
    ]);
  }
};

// Now the view function renders the following HTML:
/*
<div id="input">
    <label>input color: </label>
    <input id="color-input" type="text" onKeyDown="submit" autofocus="autofocus">
</div>
*/ 
```

Enter fullscreen mode Exit fullscreen mode

乍一看可能很复杂，所以我来解释一下我添加的内容:

1.  我们注意到现在`m()`函数的第二个参数是一个包含不同属性的对象。在 Mithril 中，我们可以在这里定义 HTML 标签的属性。所以我的容器`div`元素现在在渲染时有了`id="input"`。这同样适用于已定义的`input`元素。

2.  `div`元素的最后一个参数是其他元素的数组。这就是我们如何在秘银中嵌套元素。所以现在我们的`div`元素包含了一个`label`元素和一个`input`元素。

3.  注意到`input`元素有属性`onkeydown: submit`是很重要的。现在，`submit`是一个我们还没有定义的函数，但是由于 Mithril 的[自动提取系统](https://mithril.js.org/autoredraw.html)，你不想将该属性设置为`submit()`，即调用该函数。

现在我们已经完成了整个输入组件。让我们快速创建颜色按钮和重置按钮:

```
const ColorButtonComponent = {
  view: function(vnode) {
    return m("div", { id: "color" }, 
        m("button", {
            id: "color-btn",
            style: `background-color: ${vnode.attrs.color.background}`,
            onclick: submit
          })
        );
  }    
};

const ResetButtonComponent = {
  view: function(vnode) {
    return m("div", { id: "reset" },
        m("button", {
          id: "reset-btn",
          style: `border-color: ${vnode.attrs.color.border}`,
          onclick: submit
          },
          "reset"
          )
        );
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意一些事情:

1.  每个组件的`view`函数现在都有一个`vnode`参数。当我们一起渲染组件时，我们将看到如何使用它。

2.  每个按钮都包含一个`onclick`属性，而不是我们在输入组件中看到的`onkeydown`属性，但是它们仍然调用相同的`submit`函数。

3.  属性`style`引用了`view`函数中`vnode`参数的一些属性。这是一种访问数据的方式。在这种情况下，我们引用一些`vnode`来判断颜色按钮的背景和重置按钮的边框应该变成什么颜色。

### 第二步:添加状态变量和必要的函数

所以我们终于创建了我们的组件！但是我们仍然需要定义一些函数来帮助我们改变颜色:

```
// This acts as our global state for the component color
// Our components will access this whenever the buttons are clicked or the correct keys are pressed.
let State = {
  background: "#ffffff",
  border: "#000000",
  defaultBackground: "#ffffff",
  defaultBorder: "#000000"
};

function changeColor(val) {
  State.background = State.border = val;
}

function resetToDefault(element) {
  State.background = State.defaultBackground;
  State.border = State.defaultBorder;
  element.value = "";
}

// This is the submit function that we saw in the components before
function submit(event) {
  let inputElement = document.getElementById("color-input");
  let currentValue = inputElement.value;

  switch (event.type) {
    case "keydown":
      switch (event.keyCode) {

        // If the Enter key is pressed...
        case 13:
          changeColor(currentValue);
          break;

        // If the Escape key is pressed...
        case 27:
          resetToDefault(inputElement);
      }
      break;

    case "click":
      if (event.target.id.includes("reset")) {
        resetToDefault(inputElement);
      } else {
        changeColor(currentValue);
      }
      break;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

又一次，看起来我们做了很多。以下是概要:

1.  我们创建了一个对象`State`,作为我们应用程序的全局状态。老实说，我不确定这是不是最好的方法，但是对于像这样的小东西来说，它是有效的。组件访问`State`的`background`和`border`属性，稍后我们会看到。

2.  我们创建了前面在组件中看到的`submit`函数。我们还创建了两个助手函数，`changeColor`和`resetToDefault`。`submit`函数监听一个事件，即鼠标点击或按键，并调用助手函数，助手函数根据事件改变`State`的`background`和`border`属性。这随后在它发生时被传达给元素(很快会有更多的介绍)。

### 第三步:把所有东西放在一起

现在我们已经有了所有的组件和必要的变量和函数，但是我们如何才能在屏幕上显示一个可用的应用程序呢？解决这个问题的方法是秘银中的`m.mount`方法。它获取一个组件并将其“附加”到 DOM 的某个部分，无论是 HTML 元素还是窗口的其他部分。在这种情况下，我们将创建一个组件*，它包含我们制作的所有组件*，然后将它附加到`document.body`:

```
const App = {
  view: function() {
    return m("div",
      { id: "flex-container" },
      m(inputComponent),
      m(ColorButtonComponent, { color: State }),
      m(ResetButtonComponent, { color: State })
    );
  }
};

m.mount(document.body, App); 
```

Enter fullscreen mode Exit fullscreen mode

这一开始可能会有点混乱。简单地说，我们的`App`组件正在基于我们之前定义的组件创建元素。换句话说，`App`是包含组件的组件。从这些元素中呈现的内容取决于输入、颜色按钮和重置按钮包含的`view`函数。

记住颜色按钮和重置按钮都有这样的属性:

```
style: `border-color: ${vnode.attrs.color.border}` 
```

Enter fullscreen mode Exit fullscreen mode

这实际上引用了作为我们的应用程序组件中嵌套元素的属性参数传入的对象，即`{ color: State }`。该属性可在我们的颜色按钮和重置按钮组件的`view`函数中访问，如`vnode.attrs.color`。这解释了我们之前看到的`view: function(vnode){...}`，因为`{ color: State }`是作为`vnode`参数传入的。

我们的按钮组件现在可以访问我们的全局变量`State`。我们看到它们具体指的是`vnode.attrs.color.background`(颜色按钮)和`vnode.attrs.color.border`(重置按钮)，分别翻译成`State.background`和`State.border`。因此，当一个事件被成功触发时，新的颜色(基于输入值)被分配给按钮。当 Mithril 检测到组件的颜色变化时，UI 会立即更新。

下面是最终结果:

[https://codepen.io/andrew_chou/embed/qVedOJ?height=600&default-tab=result&embed-version=2](https://codepen.io/andrew_chou/embed/qVedOJ?height=600&default-tab=result&embed-version=2)

### 第四步:最终想法

我知道这个帖子很难理解，但是我尽力让初学者容易理解。概括地说，我第一次实现这个应用程序时没有那么多 JavaScript，但是我必须写一些 HTML 样板文件。重写后的版本包含了更多的 JavaScript，但是没有 HTML。很难理解像这样一个非常小的应用程序的权衡，但在这种情况下，使用 Mithril 和组件的思想是合理的，并且实现起来相对简单，当您试图创建更复杂的应用程序时，它肯定是有用的。

希望你从中学到了一些东西，或者至少喜欢阅读我的过程。如果你对我有什么建议(或者想指出我做得不好的地方)，请告诉我！这实际上是我的第一个技术帖子，所以我欢迎反馈。我希望在不久的将来有更多的机会写更多:)

感谢阅读！