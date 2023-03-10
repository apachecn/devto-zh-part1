# Auld Lang 重构

> 原文：<https://dev.to/warrend/auld-lang-refactoring-3enk>

新年充满了锻炼、节食、锻炼和变得更有条理的决心。今年，我想让我的膝盖恢复健康，这样我就可以再次跑步，继续我的素食努力，保持我们的公寓整洁，因为我们有了一个婴儿。

整理工作让我开始思考代码和重构。像 React 和 Rails 这样的框架提供了一些结构来鼓励代码在使用模块和组件时保持整洁和紧凑。但有时从头开始编写 JavaScript 时，重构和保持代码整洁会很快失控。我想使用普通 JavaScript 创建一个 before 和 after 示例作为重构的练习。

程序很简单:使用一个输入将一个项目添加到一个数组中，并在页面上显示结果。

以下是“之前”启动代码:

HTML:

```
 <div class="container">
  <h1>Add Item to Array</h1>
  <form id="form">
    <input type="text" id="item">
    <input type="submit">
  </form>

  <pre id="output">Ouput here...</pre>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

JS:

```
let list = []
let form = document.getElementById('form')
let output = document.getElementById('output')

form.addEventListener('submit', (e) => {
  e.preventDefault()
  let item = document.getElementById('item')
  list.push(item.value)
  output.innerHTML = list
  item.value = ''
}) 
```

Enter fullscreen mode Exit fullscreen mode

首先我创建了一个名为`list`的空数组。我使用`getElementById()`抓取了`form`和`output`，然后在表单上设置了一个事件监听器。当检测到提交时，我获取输入 id (item)并将值推送到`list`数组中。列表更新后，我用数组填充了`pre`标签。最后，我清除了输入。

代码起作用了！但是就像克利夫兰·布朗队一样，仅仅因为他们有队服、队名和四分卫，并不意味着这个组织是精简的，并且火力全开。

我们代码的问题是，我们有三个变量在全局范围内浮动，如果我们添加新的变量，这可能会在将来产生影响。我们所有的功能，向列表中添加条目、清除输入和输出数组，都打包在我们的事件侦听器中，这使我们很难理解和阅读。如果我们想在未来添加更多的功能，我们将有大量杂乱的代码需要筛选。

我学会了总是写我希望拥有的代码，然后逆向工作。所以在这种情况下，我希望有一个这样的函数来开始:

```
const formListener = (form) => {}

formListener(form) 
```

Enter fullscreen mode Exit fullscreen mode

这个函数很好地包装了我们的代码，这样我们就不会像一个迷路的布朗接收器在错误的路线上运行一样，没有任何外部变量。

我在里面添加了我们的`list`数组，并在表单上创建了我们的事件监听器:

```
const formListener = (form) => {
  let list = []
  form.addEventListener('submit', (e) => {
    e.preventDefault()
  })
}

formListener(form) 
```

Enter fullscreen mode Exit fullscreen mode

我希望我的事件监听器只处理获取输入值和清除:

```
const formListener = (form) => {
  let list = []
  form.addEventListener('submit', (e) => {
    e.preventDefault()
    let item = document.getElementById('item')
    item.value = ''
  })
}

formListener(form) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我需要一个函数将输入值添加到数组中。类似于`addItem()` :

```
const addItem = (item, list) => list.push(item)

const formListener = (form) => {
  let list = []
  form.addEventListener('submit', (e) => {
    e.preventDefault()
    let item = document.getElementById('item')
    addItem(item.value, list)
    item.value = ''
  })
}

formListener(form) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我创建了另一个函数来显示页面上的数组，`show()`，然后用我们的表单:
调用我们的 formListener()函数

```
const addItem = (item, list) => list.push(item)
const show = (list) => document.getElementById('output').innerHTML = list

const formListener = (form) => {
  let list = []
  form.addEventListener('submit', (e) => {
    e.preventDefault()
    let item = document.getElementById('item')
    addItem(item.value, list)
    show(list)
    item.value = ''
  })
}

formListener(document.getElementById('form')) 
```

Enter fullscreen mode Exit fullscreen mode

虽然这可能是一个愚蠢的例子，但我希望它能阐明重构的思维过程，即使未重构的代码很简单。重构很像写作——你写了一个糟糕的初稿，然后回去重写，重写，重写，直到它变得干净和流畅。当然，除非你是布朗一家。

[https://codepen.io/danielwarren/embed/LeyJgp?height=600&default-tab=result&embed-version=2](https://codepen.io/danielwarren/embed/LeyJgp?height=600&default-tab=result&embed-version=2)