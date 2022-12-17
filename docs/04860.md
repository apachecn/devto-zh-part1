# 自定义输入[type="file"]组件的结构

> 原文：<https://dev.to/greduan/the-anatomy-of-a-custom-inputtypefile-component>

我必须开发一个自定义的上传模式。所以我咬紧牙关，谷歌了一下“表单上传文件”,就在那里，有一个`<input type="file" />`,我觉得很棒。我用了它，这是我得到的回应:

[![Basic \<input type="file" />](img/91cb6962ad3b5427fa687d1d1cb09277.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PhGKNB83--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pappjgryf9rkehtbbjl9.png)

与提供的设计相差甚远。

那么，从哪里开始呢？事实上，我看了[一篇博客文章](https://tympanus.net/codrops/2015/09/15/styling-customizing-file-inputs-smart-way/)，这让我看到了[的一个演示](https://tympanus.net/Tutorials/CustomFileInputs/)。有了这两个来源，我就能把这些点联系起来，并找出解决方法。

## 基本问题

基本问题很简单，但如果不知道如何解决，就不容易解决。问题是输入有预设的文本、布局等等。我不能按照设计来格式化那些东西，我没有任何选择器来工作。即使我知道，我也要用黑魔法让它看起来像我想要的那样。

但是谢天谢地，如果你知道怎么做，解决起来并不是太难。

## 基本解决方案(CSS)

简单地说，隐藏`<input type="file" />`，用`<label>`标签的魔力从头开始呈现你自己的版本。

### 隐藏非理想默认

所以让我们从隐藏它开始，这可以简单地通过下面的样式来完成:

```
input[type="file"] {
  opacity: 0;
  position: absolute;
  pointer-events: none;
  // alternative to pointer-events, compatible with all browsers, just make it impossible to find
  width: 1px;
  height: 1px;
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这两行，你就使它不可见了(不像你用`display: none;`那样不存在)，并赋予它`position: absolute;`这样它就不会干扰文档流和`pointer-events: none;`这样点击其他可见元素就不会触发这个元素(因为它在技术上仍然存在，只是不可见)。

### 能够再次点击它

那么，如果我们的元素是不可见的，我们如何点击它呢？这就是`label`的魔力所在。这么有用的东西。

我们的 HTML 现在看起来应该是这样的:

```
<input type="file" id="myuniqueid" />
<label for="myuniqueid">!!Upload file!!</label> 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们点击`!!Upload file!!`并从浏览器触发上传文件对话框。这就是标签的魔力。

很好，那么我们需要做的就是将这个标签设计成按钮或者我们想要的任何东西。很简单。

```
input[type="file"] + label {
  // your styles here
} 
```

Enter fullscreen mode Exit fullscreen mode

## (JS)基本解决方案

所以现在我们有了我们想要的按钮。现在我们希望它像我们希望的那样工作。

在接下来的所有步骤中，我们将使用以下内容来处理文件更改(假设使用 JSX 语法):

```
<input type="file" id="myuniqueid" onChange={handleChange} /> 
```

Enter fullscreen mode Exit fullscreen mode

其中`handleChange`是我们的函数，这是我们将要探索的。`onChange`在用户上传一个或多个文件或取消交互后触发。

### 如果用户取消上传

我们需要能够处理如果用户取消他们的上传互动，这可以简单地完成:

```
function handleChange(event) {
  // User cancelled
  if (!event.target.files[0]) {
    return
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 获取文件的名称

要获得文件的名称，需要做`event.target.files[0].name`。

### 预览文件

也许我们想在文件上传元素中预览文件。在这种情况下，有两种方法，一种是上传到亚马逊 S3，然后显示上传的图像，另一种(我们现在要做的)是在上传之前进行本地预览。

那么我们开始吧，我们的本地预览器:

```
function generatePreviewImgUrl(file, callback) {
  const reader = new FileReader()
  const url = reader.readAsDataURL(file)
  reader.onloadend = e => callback(reader.result)
} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们的`handleChange`函数看起来是这样的:

```
function handleChange(event) {
  const file = event.target.files[0]

  // User cancelled
  if (!file) {
    return
  }

  generatePreviewImgUrl(file, previewImgUrl => {
    // (assuming we use React)
    this.setState({ previewImgUrl })
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

在 React 组件的后面:

```
<img src={this.state.previewImgUrl} /> 
```

Enter fullscreen mode Exit fullscreen mode

## 在关闭

希望这个指南有用。我试图将核心概念放在一个易于参考的指南中，以便您可以在任何需要的环境中重用它。:)

这些概念并不难，你只需要知道一个关键点:

*   为按钮使用隐藏输入+样式标签