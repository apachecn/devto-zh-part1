# 用 CSS 裁剪和调整图像大小

> 原文：<https://dev.to/teroauralinna/crop-and-resize-images-with-css-444h>

有时你没有在服务器端裁剪图像的选项，所以你需要在浏览器中进行裁剪。这里有几个例子，如何创建只使用 CSS 裁剪图像缩略图。所有的例子都响应迅速，适用于大多数图像的长宽比。裁剪的图像被缩放并居中，以最大化图像的可视区域。下面标记了关于浏览器支持和其他限制的说明。

没有一个例子是优秀的，所以使用哪一个主要是个人喜好的问题。尽管在浏览器支持上有微小的变化。

除了 SVG 之外的所有解决方案都需要使用透明的占位符图像。可以通过改变占位符图像大小来改变缩略图的纵横比。这些例子是用来在网格中显示图像的。

使用 CSS 裁剪时应该使用最小的图像尺寸，因为这会影响图像的加载时间。

## 在[代码栏](https://codepen.io/teroauralinna/pen/goXJQL)查看完整的裁剪示例和代码。

[https://codepen.io/teroauralinna/embed/goXJQL?height=600&default-tab=result&embed-version=2](https://codepen.io/teroauralinna/embed/goXJQL?height=600&default-tab=result&embed-version=2)

## 图像作为背景

### 优点:

*   良好的浏览器支持。
*   背景图像属性允许很好地改变图像位置。

### 缺点:

*   辅助功能只需要屏幕阅读器图像(用于替代文本)。
*   此外，搜索引擎优化是解决这个相同的图像(虽然不得不管理我不知道搜索引擎将如何处理这个隐藏的图像或他们索引背景图片)。
*   访问者不能很容易地从浏览器的对话框中查看或下载原始图像。

### HTML

```
<div class="image-grid">
  <a href="#" class="image-bg" style="background-image:url(https://url.to.image/image.jpg)">
    <img class="image-bg-placeholder" src="https://url.to.image/placeholder.png" alt="" />
    <img class="image-bg-img sr-only" src="https://url.to.image/image.jpg" alt="Cropped image as a background example" />
  </a>
</div> 
```

### CSS

```
.image-grid {
  display: -webkit-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  -webkit-flex-wrap: wrap;
      -ms-flex-wrap: wrap;
          flex-wrap: wrap;
  margin: 0 auto 35px;
  max-width: 920px;
}

.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  overflow: hidden;
  clip: rect(0,0,0,0);
  white-space: nowrap;
  clip-path: inset(50%);
  border: 0;
}

.image-bg {
  background-position: center center;
  background-size: cover;
  -webkit-flex: 0 0 auto;
      -ms-flex: 0 0 auto;
          flex: 0 0 auto;
  margin: 5px;
  width: calc(25% - 10px);
}
.image-bg-placeholder {
  height: 100%;
  visibility: hidden;
  width: 100%;
} 
```

## 对象拟合属性

[`object-fit`](https://developer.mozilla.org/en-US/docs/Web/CSS/object-fit) 是一个类似于`background-size`的属性，它允许选择元素应该如何调整到它的容器。

### 优点:

*   `Object-position`属性允许像改变背景图像一样改变图像位置。

### 缺点:

*   Internet Explorer 既不支持`object-fit`也不支持`object-position`(图像会显示失真)。可提供多种填充:[对象适配图像](https://www.npmjs.com/package/object-fit-images)，[对象适配多种填充](https://www.npmjs.com/package/objectFitPolyfill)。
*   此外，iOS Safari 对 [`object-fit`和`object-position`](https://caniuse.com/#search=object-fit) 的支持有限。

### HTML

```
<div class="image-grid">
  <a href="#" class="image-fit">
    <img class="image-fit-placeholder" src="https://url.to.image/placeholder.png" alt="" />
    <img class="image-fit-img" src="https://url.to.image/image.jpg" alt="Cropped image using object-fit example" />
  </a>
</div> 
```

### CSS

```
.image-grid {
  display: -webkit-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  -webkit-flex-wrap: wrap;
      -ms-flex-wrap: wrap;
          flex-wrap: wrap;
  margin: 0 auto 35px;
  max-width: 920px;
}

.image-fit {
  -webkit-flex: 0 0 auto;
      -ms-flex: 0 0 auto;
          flex: 0 0 auto;
  margin: 5px;
  position: relative;
  width: calc(25% - 10px);
}
.image-fit-placeholder {
  height: 100%;
  visibility: hidden;
  width: 100%;
}
.image-fit-img {
  bottom: 0;
  height: 100%;
  left: 0;
  object-fit: cover;
  object-position: center;
  position: absolute;
  right: 0;
  top: 0;
  width: 100%;
} 
```

## 绝对定位图像

### 优点:

*   良好的浏览器支持。

### 缺点:

*   在不知道图像纵横比的情况下，不可能完美地裁剪和缩放垂直和水平图像。在我的例子中，我已经设置了`max-height`和`max-width`，这样它们就可以使用我所使用的长宽比。但是您需要选择水平或垂直图像是否正确显示。或者，如果您在服务器端或用 Javascript 检查纵横比，您可以在 image 中添加一个 CSS 类。然后，您可以为垂直和水平图像实现不同的样式。

### HTML

```
<div class="image-grid">
  <a href="#" class="image">
    <img class="image-placeholder" src="https://url.to.image/placeholder.png" alt="" />
    <img class="image-img" src="https://url.to.image/image.jpg" alt="Cropped image example" />
  </a>
</div> 
```

### CSS

```
.image-grid {
  display: -webkit-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  -webkit-flex-wrap: wrap;
      -ms-flex-wrap: wrap;
          flex-wrap: wrap;
  margin: 0 auto 35px;
  max-width: 920px;
}

.image {
  -webkit-flex: 0 0 auto;
      -ms-flex: 0 0 auto;
          flex: 0 0 auto;
  margin: 5px;
  overflow: hidden;
  position: relative;
  width: calc(25% - 10px);
}
.image-placeholder {
  height: 100%;
  visibility: hidden;
  width: 100%;
}
.image-img {
  left: 50%;
  max-height: 150%;
  max-width: 177%;
  position: absolute;
  top: 50%;
  -webkit-transform: translate(-50%, -50%);
      -ms-transform: translate(-50%, -50%);
          transform: translate(-50%, -50%);
} 
```

## SVG 内的图像

使用 SVG 对我来说是一个新的想法。感谢[Cloud Four:Cropping Image Thumbnails with SVG](https://cloudfour.com/thinks/cropping-image-thumbnails-with-svg/)的博文，我在这篇博文中注意到了这项技术。

我对这个例子做了一点改进，使它具有响应性，并添加了 SVG 标题以方便访问。标题作为替代文本。

### 优点:

*   不需要占位符，所以原始图像是唯一需要的图像。

### 缺点:

*   在某些浏览器和分辨率下，图像渲染质量很差。
*   是最难在所有浏览器中完美显示的一个。

### HTML

```
<div class="svg-image-grid">
  <a href="#" class="image-svg">
    
      Cropped image using SVG example
      <image xlink:href="https://url.to.image/image.jpg" width="100%" height="100%" preserveAspectRatio="xMidYMid slice" />
    
  </a>
</div> 
```

### CSS

```
.svg-image-grid {
  clear: both;
  margin: 0 auto 35px;
  max-width: 920px;
  overflow: hidden;
  position: relative;
}

.image-svg {
  float: left;
  margin: 5px;
  padding-bottom: calc(25% - 10px);
  position: relative;
  width: calc(25% - 10px);
}
.image-svg svg {
  bottom: 0;
  display: block;
  height: 100%;
  left: 0;
  position: absolute;
  right: 0;
  top: 0;
  width: 100%;
} 
```

我很想知道你是否成功地使用过任何其他技术，或者有不能使用这里提到的某些例子的用例。

* * *

这篇博文最初发表于[aural linna . blog](https://auralinna.blog/post/2018/crop-and-resize-images-with-css)