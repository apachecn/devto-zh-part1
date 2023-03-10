# 编写基线可访问标记和样式

> 原文：<https://dev.to/adammckenna/writing-baseline-accessible-markup-and-styles>

文章原贴[此处](https://amckenna.co/articles/writing-baseline-accessible-markup-and-styles)。

* * *

啊，*可达性*...你会发现大多数你提到的开发者会开始疏远你，就好像你对他们施了魔法一样。

但是，什么是可访问性，为什么这么多开发人员对此不感兴趣？

**可访问性(a11y)指的是一个应用程序对*所有*受众**的可用性。无障碍应用程序可供任何人使用，无论是否有身体、视觉或听觉障碍。任何用户的体验都不会受到影响。

听起来工作量很大。这是可能的。这是这么多开发者不感兴趣的一个原因。

我的大部分经历都是在开发机构。在这些组织中，目标是利润最大化:在尽可能短的时间内生产出好的产品。在这种环境下，可访问性变成了拥有的*美好。至少，这是项目经理或产品负责人的观点。问一个开发者，他们会说:*

*“可及性是无聊的。谁在乎？”*

这是他们不感兴趣的另一个原因。

我不同意。我觉得不无聊。创建可以被众多受众使用的健壮的界面是有益且有趣的。

但是，这是无聊还是有趣无关紧要。让我们来谈谈在乎谁。

由[残疾人权利委员会](https://www.gov.uk/government/organisations/disability-rights-commission)在 2008 年进行的一项研究发现，在英国有超过**690 万处于工作年龄的残疾人**，占工作人口的 19%。

这几乎是每五个人中就有一个。

这张照片中捕捉到的残疾类型有很大不同。但是，这确实意味着每五个网站访问者中就有一个可能有残疾，你的网站应该迎合这种残疾。例如，如果他们是盲人，而你的网站不支持屏幕阅读器，他们能做什么？它们已从您的产品中剔除。

易接近的产品是更可行的产品。越容易访问，网站就能容纳越多的用户。

当然，更不用说道德问题了。如果你意识到一个不可访问的网站的含义，并且你继续避免实现可访问的功能，这是否可以被认为是你断然拒绝了有可访问性要求的用户？

所以，问题的答案是:*谁在乎呢？*答案(应该是):参与项目的每一个人。无论您是在为 web、移动设备、平板电脑还是烤面包机进行开发。如果您的应用程序有任何形式的用户交互，您必须考虑可访问性。

谢天谢地，有很多“低垂的果实”可以摘。只需相对较少的努力，您就可以对应用程序的可访问性产生巨大的影响。我们将探索如何。

我们将研究代码中的可访问性。因此，包括颜色、UX 设计图案等因素不在本文讨论范围之内。也就是说，这些因素与本文中涉及的任何内容一样与可访问性相关。

这篇文章也不应该被看作是一个“该做什么”和“不该做什么”的全面列表，而是我认为重要且容易实现的考虑因素的选择。

事不宜迟，让我们开始吧。

## 测试工具

作为开发人员，我们需要考虑的主要障碍之一是视觉障碍。我们需要确保几乎看不见或者根本看不见的人仍然可以浏览您的应用程序，找到他们需要的所有信息，并像其他用户一样轻松地与之交互。

大多数视障用户使用一种叫做*屏幕阅读器*的技术浏览网页。屏幕阅读器扫描 HTML 文档，解释标记，并将信息读给用户。使用键盘操作屏幕阅读器，以便用户可以与链接和其他交互式元素进行交互。

有太多的屏幕阅读器可供使用，但按平台分类，最受欢迎的屏幕阅读器有:

*   Windows - [NVDA](https://www.nvaccess.org/)
*   Mac -用于 Mac 的 VoiceOver(内建)
*   iOS-iOS 上的 VoiceOver(内置)
*   安卓- [谷歌对讲](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback&hl=en_GB)

让屏幕阅读器有用的关键是丰富的描述性标记和视图。这是通过使用语义元素(例如`<nav>`、`<header>`、`<main>`等)来实现的。)如预期的那样，正确的标题、描述性的图像、隐藏的元素、可用的元素、WAI-ARIA 组曲以及其他各种考虑因素。Android 和 iOS 也有自己的工具来使元素可访问。

我们将依次探讨这些问题。

## 标签

您应该始终标记交互元素，包括链接、按钮(如果元素的子元素没有提供足够的描述)和输入。

您还应该为有图标但没有可见文本的链接和标签提供标签。

让我们看一个真实的例子。使用`a`标签，屏幕阅读器将读取元素开始和结束标签中的内容。然而，这通常是非描述性的。例如:

```
<a href="about-us.html">Read More</a> 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，屏幕阅读器将向用户逐字读出*“read more”*。你多久在网上看到一次？

这并不能很好地说明这个链接指向哪里。如果用户浏览页面，用户只会听到*“读更多，读更多，读更多…，”*而不知道如果他们点击链接会发现什么。

我们如何解决这个问题？

### [网络上的](#on-the-web)

谢天谢地，在网上你可以使用 WAI-ARIA 为链接提供描述性标签。

WAI-ARIA 是“网页可访问性倡议”的首字母缩写，即可访问的富互联网应用程序。它是一个为标记提供丰富语义的工具。ARIA 提供了一个属性库，可以用来增强 HTML 元素。

一个这样的例子是`aria-label`。您可以使用`aria-label`属性来指定一个特定的值，该值将被屏幕阅读器读取，而不是元素的值。

```
<a href="about-us.html" aria-label="Read More About The Team">Read More</a> 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以使用`aria-labelledby`给这个元素另一个元素的值。

```
<h1 id="title">Read More About The Team"</h1>

<a href="about-us.html" aria-labelledby="title">Read More</a> 
```

Enter fullscreen mode Exit fullscreen mode

### 安卓

在 Android 上，方法是相似的。您可以像使用`aria-label`一样在元素上使用`android:contentDescription`。

```
<Button
    android:id="@+id/some_id"
    android:src="@drawable/search"
    android:contentDescription="@string/search"/> 
```

Enter fullscreen mode Exit fullscreen mode

### 雨燕

在为 iOS 编写 Swift 时，可以使用 UIAccessibility API 中的`accessibilityLabel`。

```
self.navigationController.navigationBar.backItem.setAccessibilityLabel("back") 
```

Enter fullscreen mode Exit fullscreen mode

## 表单和输入占位符

如果有表单元素或输入，不要依赖`placeholder`属性作为标签。HTML5 规范对此提出了警告，因为它不能作为屏幕阅读器用户的标签。

对于普通用户来说，一旦输入被填充，它也会消失。这使得表单一旦完成就很难修改。

### [网络上的](#on-the-web)

**不要**

```
<input type="text" name="message" placeholder="Message…"> 
```

Enter fullscreen mode Exit fullscreen mode

相反，您应该为每个输入提供一个关联的文本标签。您还应该首先提供标签，并将标签的`for`属性设置为它所引用的表单元素的名称:

**做**

```
<label for="message">Message</label>
<input type="text" name="message" placeholder="Message…"> 
```

Enter fullscreen mode Exit fullscreen mode

### 安卓

与 HTML5 类似，Android XML 使用`android:hint`而不是`placeholder`属性。它以同样的方式工作，并且不应该出于同样的原因替换标签。

**不要**

```
<EditText 
    android:id="@+id/edit_text" 
    android:hint="@string/aac_edit_text_about_label" /> 
```

Enter fullscreen mode Exit fullscreen mode

相反，为输入添加一个 TextView 标签，并为属性添加 label for 及其所属输入的 id 值:

**做**

```
<TextView
    android:labelFor="@+id/edit_text"
    android:text="@string/aac_edit_text_about_label" />

<EditText android:id="@+id/edit_text" /> 
```

Enter fullscreen mode Exit fullscreen mode

### 雨燕

iOS 版 Swift 略有不同。您也不应该依赖 UITextFields 的占位符属性:

**不要**

```
let messageField = UITextField = UITextField (...);
messageField.placeholder = "Message…" 
```

Enter fullscreen mode Exit fullscreen mode

然而，与 HTML5 和 Android 不同，没有办法将标签连接到输入。相反，您应该直接在文本输入中使用 accessibilityLabel 属性:

**做**

```
let messageField = UITextField = UITextField (...);
messageField.accessibilityLabel = "Message…" 
```

Enter fullscreen mode Exit fullscreen mode

占位符属性本身在所有三个平台上都很好，应该在必要的地方使用。

问题是开发者用它来代替标签。这不是它的目的。它应该总是和标签一起使用，而不是作为替代品。

## 写好标签和描述

提供标签和描述是很重要的，这样用户才能理解元素的用途，以及与元素交互的结果。

确保这些标签和描述写得很好也很重要。一个写得不好的标签和根本没有标签造成的损害一样大。

Google Material UI 可用性指南定义了一套写好标签的规则。这些是大多数应用的理想选择。

让我们探索这些规则:

### 言简意赅

保持标签简短和“切中要点”，确保标签简短但具有描述性。

**不要**

> 注销按钮。注销账户[adam@gospelware.co.uk](mailto:adam@gospelware.co.uk)并返回主页

**做**

> 注销[adam@gospelware.co.uk](mailto:adam@gospelware.co.uk)

### 避免在文本中描述控件类型

标签不应该描述控件的类型，不管是按钮、链接还是字段等等。

**不要**

> 消息字段。
> 
> 设置按钮。

屏幕阅读器将宣布控件类型。因此，指定类型将导致向用户公布该类型两次。

在我们的例子中，用户会听到“消息域”。字段"和"设置按钮。按钮。”

**做**

> 消息。
> 
> 设置。

相反，可以省略控件类型。在我们的第二个例子中，用户将听到“设置按钮”和“消息字段”。

### 避免在文本中描述控件状态

出于同样的原因，不要描述元素的状态。

**不要**

> `Accept Terms and Conditions is selected`

**做**

> `Accept Terms and Conditions`

### 表示一个元素做什么

描述一个元素在交互时做什么。不要描述元素本身或元素的属性。

**不要**

> `Burger menu icon`
> 
> `Pencil icon`

**做**

> `Show/hide navigation menu`
> 
> `Edit`

### 不提确切的手势或互动

不要讨论手势或互动，而是描述任务。

**不要**

> `Tap to speak`
> 
> `Click to start`

**做**

> `Voice search`
> 
> `Start game`

## 对话框

对话或情态动词是传递信息的有用元素。它们通常用于提示用户输入信息，或者提供需要用户立即关注的信息。

在撰写本文时，还没有描述对话框的原生 HTML5 标签，尽管常见的第三方 JavaScript 库和框架，包括 [jQuery](https://jqueryui.com/dialog/) 和 [Angular Material](https://material.angularjs.org/latest/demo/dialog) ，都提供了预构建的可访问对话框。

如果您没有使用带有开箱即用对话框的库或框架，您将需要使用带有几行 JavaScript 的常规旧`<div>`标签来提示对话框(这超出了本文的范围)。

典型的对话框可能如下所示:

```
<div class="dialog">
    Whoa, mate. You've made a mistake.
</div> 
```

Enter fullscreen mode Exit fullscreen mode

如果用户遇到错误，确保提供了一个标签，并且`<div>`使用了`role=”alert”`属性和值。

```
<div role="alert">
    <span class="close-icon" tabindex="0">x</span>
    The form has an error: you forgot to provide a message.
</div> 
```

Enter fullscreen mode Exit fullscreen mode

当元素出现在 UI 中时，使用`role=”alert”`的元素将显示给屏幕阅读器用户。必要时，元素通常会通过 JavaScript 或 CSS 显示。

Android 和 iOS 本身都提供了可访问的对话框。

## 图片- 'alt '属性

屏幕阅读器要考虑的另一件事是图像。应用程序中使用的图像分为两类:*装饰性的*或*信息性的*。

另一方面，装饰性的图像除了美观之外没有其他用途。屏幕阅读器应该忽略这些图像。

信息图像向用户呈现信息。它们需要以文本形式传递给屏幕阅读器，否则图像信息将会丢失。

我们如何解决这个问题？这就是`alt`属性的用武之地。

如果图像提供了相关信息，例如一个图表或信息图，您需要使用带有`alt`属性的`img`标签。`alt`属性将描述图像的内容。例如:

```
<img src="political-graph.jpg" alt="Nationwide results at a glance: ..."> 
```

Enter fullscreen mode Exit fullscreen mode

当然，您应该将任何显著复杂的数据输入到表格或类似的布局中，并依赖图像作为基本数据。

装饰图像包括图标、徽标和背景。尽可能使用`background-image` CSS 属性提供装饰图像。

如果不能使用`background-image` CSS 属性来提供图像，那么应该使用带有空`alt`属性的`img`标签来提供图像。当`alt`属性为空时，屏幕阅读器会忽略该图像。

```
<img src="decorative-icon.jpg" alt=""> 
```

Enter fullscreen mode Exit fullscreen mode

## 标题

网站上使用标题来表示页面和章节的标题。它们使用`<h1>`到`<h6>`标签交付。下面是一个取自随机网站的网站标题层次结构示例:

[![Gospelware.co.uk website headers](img/a236ed6f89e13953bfb59d3e5a505d37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JCy-deiA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0wxfvhybee46txxdpj2r.png)

来源:[gospelware.co.uk](http://gospelware.co.uk)通过[seowebpageanalyzer.com](http://www.seowebpageanalyzer.com/)测试

标题只使用标题标签。不要用它们来改变审美。

Header 标记只能在降序层次结构中使用，不应被跳过。

例如，如果你使用了`<h1>`标签，并且需要在它下面使用另一个标题，那么下一个标题必须是一个`<h2>`标签，或者另一个`<h1>`标签。这适用于每个标题级别。

文档应该总是以一个`<h1>`标签开始，没有其他的标题标签。

## 隐藏描述

用户界面可以为网页上的信息提供大量的上下文。当页面以文本形式通过屏幕阅读器传递时，这些信息可能会丢失。

您可以通过使用对屏幕阅读器显示但对所有其他用户隐藏的隐藏元素来解决这个问题。这可以通过一小块聪明的 CSS 来实现:

```
.hide {
   position: absolute !important;
   top: -9999px !important;
   left: -9999px !important;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 空州

空状态是指通知用户没有数据的一小段文本。下面是 Outlook 的一个例子:

[![](img/f87883b19481fe22f4c992a6014787a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--49p0mJcf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e59fzu2inf09fdztuj5e.png)

在可能存在数据但没有数据的情况下，应该总是提供空状态。例如，待办事项列表或电子邮件收件箱。

这意味着屏幕阅读器用户马上就知道没有数据。否则，他们可能会等待一段时间，假设数据正在加载，或者发生了阻止数据显示的问题。

## 样式

最后，让我们简单讨论一下 CSS。

任何可点击的元素都应该有一个悬停状态，向用户直观地表明该元素是可点击的。这可以通过使用 CSS 伪`hover`选择器来实现:

```
.button:hover {
    background-color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

`hover`伪选择器仅适用于鼠标和基于触摸的设备。它不适用于通过键盘选择的元素。

结果，如果仅使用`hover`，不使用或不能使用鼠标或触摸设备的用户将不会接收到当前所选元素被选择的任何视觉指示。

为了解决这个问题，您应该将`focus`伪选择器与`hover`选择器的任何实例一起使用:

```
.button:hover,
.button:focus {
    background-color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后提一下，还有一个新的 CSS 伪选择器已经被提出，但是还没有完全实现，叫做:focus-within。

它允许您在选择了父元素的任何子元素时设置父元素的样式。

```
div:focus-within {
    background: yellow;
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这还处于试验阶段，但我确信这将很快得到广泛支持。它提醒人们，网络正变得越来越容易访问。

## 结论

也许可访问性很无聊是真的，也许可访问性很无聊，当然可访问性确实需要更多的工作。

但是，这很重要。

我真的相信任何人，不管有什么残疾或疾病，都应该能够像任何所谓的“完全健康”的人一样享受网络和移动应用。我相信你也是。

如果你同意这种观点，那么也许是时候重新评估你对可访问性的看法了。

感谢您的阅读。

### 说句题外话:通达难。

可及性*是*困难的，即使有这些速赢，但是为什么呢？

我认为有两个主要因素使可访问性变得困难。

首先是对导致应用程序不可访问的问题以及解决这些问题的步骤缺乏理解或认识。网上有太多的资源可以识别共有问题。当然，最好的方法是对你的网站进行真正的用户测试。

第二是因为难以感同身受。这是一个广泛的话题。感同身受的最佳方式是真实的用例。从使用你的应用程序的真正残疾人那里获得反馈。当然，这个选项在大多数情况下很少可用。

谢天谢地， [Eric Bailey](https://ericwbailey.design/) ，一位波士顿的设计师，创建了一个[非常有用的网站](https://empathyprompts.net)来模拟可访问性问题，并帮助开发者获得一点同情心。我强烈建议去看看。

### 来源:

*   [https://material . io/guidelines/usable/accessibility . html # accessibility-writing](https://material.io/guidelines/usability/accessibility.html#accessibility-writing)
*   [https://developer . Android . com/training/accessibility/accessible-app . html](https://developer.android.com/training/accessibility/accessible-app.html)
*   [https://www.deque.com/blog/accessible-text-input-android/](https://www.deque.com/blog/accessible-text-input-android/)
*   [https://www . paciellogroup . com/blog/2011/02/html 5-accessibility-chops-the-placeholder-attribute/](https://www.paciellogroup.com/blog/2011/02/html5-accessibility-chops-the-placeholder-attribute/)
*   [https://developer . apple . com/documentation/ui kit/accessibility/ui accessibility](https://developer.apple.com/documentation/uikit/accessibility/uiaccessibility)
*   [https://developer . Mozilla . org/en-US/docs/Web/Accessibility/ARIA/ARIA _ Techniques/Using _ the _ alert _ role](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_alert_role)
*   [http://webaim.org/techniques/semanticstructure/](http://webaim.org/techniques/semanticstructure/)
*   [https://CSS-tricks . com/places-its-independent-to-use-display-none-but-don/](https://css-tricks.com/places-its-tempting-to-use-display-none-but-dont/)
*   [http://www.seowebpageanalyzer.com/](http://www.seowebpageanalyzer.com/)
*   [https://developer . apple . com/documentation/objective c/ns object/1615181-accessibilitylabel](https://developer.apple.com/documentation/objectivec/nsobject/1615181-accessibilitylabel)
*   [https://stack overflow . com/questions/33077902/is-a-way-to-customize-accessibility-label-on-the-navigation-bar-back-butto](https://stackoverflow.com/questions/33077902/is-there-a-way-to-customise-accessibility-label-on-the-navigation-bar-back-butto)
*   [https://developer . Mozilla . org/en-US/docs/Web/CSS/:focus-within](https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-within)
*   [https://www . techopedia . com/definition/10165/accessibility-a11y](https://www.techopedia.com/definition/10165/accessibility-a11y)