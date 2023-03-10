# 保持前端的控制

> 原文：<https://dev.to/wes/keeping-control-of-the-front-end-ag7>

在 Booking.com，数百名开发人员和设计人员每天为我们的代码库做出贡献，这导致了代码可发现性、维护和重用的潜在复杂性。在本帖中，我们将重点关注这些挑战的客户端方面，并介绍一些我们用来应对这些挑战的技术。

### 前缀和命名空间

由于我们代码库的规模和每天引入变更的人数，维护所有页面组件的唯一标识符可能会很困难。

JavaScript 和[级联样式表](https://developer.mozilla.org/en-US/docs/Web/CSS)都利用标识符正常工作，分别通过[变量名](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var)和[选择器](https://developer.mozilla.org/en/docs/Web/Guide/CSS/Getting_started/Selectors)，两者都在全局执行范围内。

让我们从 JavaScript 开始。我们使用了[名称空间](https://www.2ality.com/2011/04/modules-and-namespaces-in-javascript.html)，这是一个全局变量的对象属性。(我们还实现了模块，这将在本文后面讨论。)

```
// The only global variable
var B = {};

// The search namespace
B.search = {};

// Check-in date of the search
B.search.checkIn = new Date(2015, 3, 1);

// Travel purpose of the search
B.search.travelPurpose = 'business'; 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，B.search 是我们用来可视化标识代码的名称空间。请注意 travelPurpose 在搜索中的范围，澄清其上下文和含义。

CSS 不一样。因为 CSS 没有提供可伸缩的选择器分组方式，所以我们使用前缀。我们还确保所有的选择器都尽可能地特定，以防止冲突。例如，在我们的文件中，已经有大约 4000 个类选择器在其声明中包含了术语 item。

想象下面这个简单的例子:酒店页面上的设施列表。

```
<ul class="facilities">
  <li class="item">Wi-Fi</li>
  <li class="item">Breakfast</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

这可能会干扰另一个团队在网站标题上添加通用菜单的工作。

```
<ul class="menu">
  <li class="item">Home</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

在这两种情况下。项将具有可能被覆盖的特定 CSS 规则，从而生成意外的结果。有时这些交互发生在超出开发人员测试范围的特定页面上。

为了防止这些冲突，我们经常使用前缀:

```
<ul class="hp-facilities">
  <li class="hp-facilites__facility">Wi-Fi</li>
  <li class="hp-facilites__facility">Breakfast</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

由于我们通过 A/B 测试在实验上投入了这么多，当相关的实验到期时，大量的代码变得无关紧要。

因为我们希望避免代码库中的[代码腐烂](https://en.wikipedia.org/wiki/Software_rot)，我们希望只保留我们真正需要的部分，那些不相关的代码片段必须定期删除。能够在代码中快速搜索特定的标记，比如 CSS 类名，是这种清理的关键要求。

### 控制执行

我们的 JavaScript 代码以受控的方式运行是非常重要的。我们的代码需要*精确*，只在某个特性、页面或事件需要时执行。它还需要*健壮*，防止不相关的功能之间的干扰。

假设我们有三个脚本文件连接在一起，结果文件被添加到我们网站的每个页面。

```
// from tooltip.js
$('.tooltip').addTooltip();

// from available_rooms.js
var prices = $('#prices .price');
prices[0].scrollTop = 0;

// from sticky_user_bar.js
$(window).scroll(function () {
  $('.user_bar').css('top', document.body.scrollTop);
}); 
```

Enter fullscreen mode Exit fullscreen mode

任何失败的部分都将阻止下一部分的执行。例如，如果页面上没有匹配#prices 的元素。price 将会有一个错误，停止所有其他的执行，破坏用户栏行为。

此外，可能有不应该运行的代码，这是不可取的。在示例中，如果没有元素匹配。页面上的 user_bar，滚动事件仍然被监控，浪费 CPU 周期。

为了减轻这一点，我们选择开发一个内部流控制器，它提供一个基于[和](https://github.com/amdjs/amdjs-api/blob/master/AMD.md)的执行控制 API，构建在[和](https://github.com/jrburke/almond)之上。它使用 setTimeout 来提供单独的上下文，然后隔离故障并防止主线程阻塞。

额外的好处是，因为代码被包装了，我们可以很容易地[标记这些调用以进行分析](https://developer.mozilla.org/en-US/docs/Web/API/Console/profile)，并找到可能浪费资源的特性。

这也有助于隔离我们的 A/B 测试的实验部分，使它更容易发现和清除失败的实验，如前一节所述。

### 限制你的执行力

虽然 CSS 代码通过使用名称空间来避免冲突，但是 JavaScript 不应该将行为泄露给 DOM 元素或同一页面中其他不相关的组件。

这个问题的一部分可以通过遵循完善的编码原则来避免，比如避免全局变量(通过使用[严格模式](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Strict_mode)来强制执行)，模块化代码的特定部分，等等。

我们也鼓励我们的团队开发上下文相关的软件来避免副作用。

```
$('form').on('submit', function () {
  var destination = $('.destination');
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里，每个表单元素都有一个 submit 事件处理程序。此外，它在整个文档 DOM 树中搜索。目标选择器，一旦有人无意中添加了与同一选择器匹配的元素，它就可能停止工作。

一种更好的方法强调更详细地指定目标，旨在只影响需要影响的部分。

```
$('.js-searchbox-form').on('submit', function (event) {
  var form = $(event.currentTarget);
  var destination = form.find('.js-destination-input');
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，类名是清晰而具体的，代码将只在它自己的表单中寻找元素，防止可能的泄漏。

### 模块化

让多个团队同时独立工作允许不同的开发风格跨越代码库。一个开发者可能喜欢在[life](https://benalman.com/news/2010/11/immediately-invoked-function-expression/)上包装她自己的代码，另一个可能更喜欢[原型模式](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#prototypepatternjavascript)。

虽然如果代码完成一个简单的任务，这不是问题，但是更复杂的解决方案可能会变得太大而难以理解、操作或维护。

```
function showTheGallery (hotelId) {
/* Here goes 31415 lines of code involving all event handlers, the overlay behind the gallery, capturing the keyboard events to navigate and close the gallery, logic to preload the images, logic to center the main image relative to another element, scrolling of the thumbnails, obtaining the images from a specific variable in the code, showing the gallery, etc. */
}

showTheGallery(42); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，部分代码可能会变得过于复杂和孤立，从而难以理解和调试。它还阻止了任何类型的可重用性。

然而，我们可以将代码分解成服务于特定目的的更小的块，正如在 [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) 原则中描述的那样“每一部分知识必须在系统中有一个单一的、明确的、权威的表示”。

```
define('photo-gallery',
  [
    'component-maker',
    'dom-position',
    'dom-scroll',
    'env-variables',
    'image-preload',
    'keyboard-events',
    'overlay'
  ],
  function (...) {
    // Tying them all together nicely, exporting an extensible component
  }
); 
```

Enter fullscreen mode Exit fullscreen mode

在这里，每个依赖项都是独立的、足够具体的，并且完全可以被其他人重用，得到的对象允许快速扩展和行为改变，因此可以修改原始代码。

### 组件

遵循将代码的行为限制在您希望它运行的地方的原则，以及我们希望构建模块化和可重用的代码库的事实，我们开发了一个简单的解决方案，称为 **B.components** 。

其背后的原理是向一个或多个 DOM 节点添加行为。它只在元素存在时执行代码，并允许一个组件扩展另一个组件的功能，从而促进可重用性。

```
<button type="button" data-component="alert">Alert</button> 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们将行为添加到代码中的特定按钮。JavaScript 不需要知道文档中的具体目标元素，因为请求行为的是按钮，而不是相反。

该代码接收一个指向 DOM 节点的指针，并可以执行必要的操作，比如监听这个引用上的 click 事件并触发一个警告窗口。

这种方法的好处是基于 DOM 的灵活性。我们可能会改变 HTML 的每个方面，甚至添加更多的元素，同时保持相同的行为。

```
<a data-component="alert">Alert Anchor</a>

<button type="button" data-component="alert">Alert Span</button> 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 AMD 作为基础，将组件定义存储为模块，与之前提到的用于包含执行的 setTimeout 技术相同，并为在 DOM 或任何指定节点中找到的每个组件定义创建一个新的实例。

这种解决方案使我们不必知道在页面上呈现动态 HTML 时到底要做什么。

```
var templateCode = 'Complex HTML structure';
$('body').append(templateCode);

// We added tooltips, so initialize them
$('.tooltip').tooltip();

// We also added a lightbox, but that uses another API
LightboxFactory.addLightbox('#lightbox-a', { lightbox: 'options' });

// Did we forget something? Did some API change? 
```

Enter fullscreen mode Exit fullscreen mode

这一切都被一行程序所取代:

```
$(templateCode).appendTo('body').loadComponents(); 
```

Enter fullscreen mode Exit fullscreen mode

方法$.fn.loadComponents 将负责查找必要的组件，每个组件都有机会初始化自己，所有这些都在同一个可预测的 API 下进行。

### 大局

因为我们是一家拥有数百名开发人员的大公司，所以我们会小心谨慎，这样我们的贡献会使我们的代码比我们发现的更好，从长远来看，使它更易于维护。

组织和命名我们的 CSS 和 JavaScript 块有助于使我们的代码容易被发现和健壮。控制我们的执行代码范围，只在真正必要的时候运行它，这使得它变得可预测和快速。考虑模块和 UI 组件可以确保我们能够快速理解和重用我们的解决方案。所有这些都是在保持良好的社区意识和相互尊重的情况下完成的。

这些只是我们在 Booking.com 采用和创造的帮助我们应对增长挑战的一些技术。

重要的一课是永远要放眼全局，永远不要假设你处在一个完全受控的环境中。

代码必须具有弹性。