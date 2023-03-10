# 如何编写可访问的 JavaScript

> 原文：<https://dev.to/benrobertson/how-to-write-accessible-javascript-4ga9>

您正在完成一个 web 项目，就在您完成收尾工作时，您收到了一份由项目经理转发给您的可访问性错误列表。

色彩对比不足。

缺少 alt 标记。

这个交互组件需要可以通过键盘访问。

我们可能不愿意承认这一点，但我们都经历过:在一个项目结束时，试图对我们的网站的可访问性进行反向工程。这让开发者、设计师、项目经理和客户感到沮丧。

虽然可访问性可能令人沮丧，但是您可以通过从一开始就规划可访问性来为您自己、您的团队和您的客户的成功做准备。这里有 4 个技巧，在构建可访问的支持 JavaScript 的网站和应用程序时，可以节省你的时间和麻烦。

1.  对用户点击的任何东西使用`<button>`元素。
2.  计划常见的键盘交互。
3.  管理 ARIA 状态
4.  管理焦点

## 1。对用户点击的任何东西使用`<button>`元素。

一般来说，使用语义 HTML 元素将有助于 web 项目的可访问性。当进行交互时，`<button>`是用户点击的东西的*语义标签，这些东西不是链接或其他输入。这是一种语义方式，表示一个元素是可点击的，将成为你的新的最好的朋友。*

 *> HTML `<button>`元素代表一个可点击的按钮。
> 
> <cite>- [Mozilla 开发者网络](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button)T3】</cite>

当您将`<button>`元素用于可点击的界面元素并将点击事件监听器绑定到这些按钮时，您可以免费获得许多功能。

首先，**按钮自动对焦**；它们在一页的标签索引中。如果用户登录到您的站点，并且只使用键盘，他们可以按 tab 键在页面上的所有可聚焦元素之间循环，包括超链接和按钮。

第二，屏幕阅读器会通知用户有一个按钮处于焦点上。**屏幕阅读器用户默认知道按钮元素是交互式的**。这使得在你的`<button>`中包含清晰易懂的文本变得尤为重要，这样所有用户都能明白点击它会做什么。还有一些有用的`aria`属性你可以添加到你的按钮上，但是我们会[在后面的](https://benrobertson.io/accessibility/javascript-accessibility#3-manage-aria-states)中讲到。

第三，当您向`<button>`元素添加一个点击事件监听器时，**您可以免费获得键盘辅助功能。**这意味着当您使用`<button>`元素时可以编写更少的 JavaScript。相比之下，如果您为一个`div`添加一个点击事件监听器，那么您还必须为空格键和回车键添加 keyup 监听器，以便让键盘可以访问该元素。对于 button 元素，默认的键盘操作(空格键和 enter)和屏幕阅读器单击操作会触发 click 事件。您不必编写额外的键盘监听器。

总结一下:如果用户正在点击它，而它不是一个链接或某种输入，就使用一个`<button>`。

## 2。计划常见的键盘交互。

对于更复杂的交互组件，组件中可能有几个交互元素。注意添加到 DOM 中的事件侦听器的类型，并考虑这些操作是否能够由键盘触发。

例如，你的组件上有关闭或最小化按钮吗？ESC 键应该也能够触发关闭。是否有某种水平滚动类型的动作或者下一个/上一个按钮？考虑将事件绑定到箭头键。

常见的交互包括:

1.  退出当前组件
2.  提交
3.  移动位置/浏览

添加动作的常用键:

*   回车(键码 13)
*   空格键(键码 32
*   箭头键(37 - 40)
*   ESC(键码 27)
*   标签(键码 9)

如何将动作绑定到特定的键？您可以通过向`keyup`事件添加一个事件监听器来实现。当您将事件传递给回调函数时，您可以访问`keyCode`属性，并且可以根据键码触发操作。我很难记住`keyCodes`，所以在开发过程中，我经常会添加一个事件监听器，将所有键码记录到控制台，这样我就可以找到我需要使用的键码:

```
document.addEventListener('keyup', (event) => {
    console.log(event.keyCode);
}); 
```

Enter fullscreen mode Exit fullscreen mode

为了让事情变得简单一点，我将记录我最终需要引用的最常见的键码。下面是我最终在组件中使用的一个常见模式，以及我使用的最常见的键码:

```
document.addEventListener('keyup', (event) => {
    switch (event.keyCode) {
        // escape
        case 27:
            // exit
            break;
        // enter || spacebar
        case 13 || 32:
            // submit or something
            break;
        // left arrow
        case 37:
            // move back / previous
            break;
        // right arrow
        case 39:
            // move forward
            break;
        // up arrow
        case 38:
            // move up
            break;
        // down arrow
        case 40:
            // move down
            break;
       }
} 
```

Enter fullscreen mode Exit fullscreen mode

我不会在每种情况下都使用所有这些，但它们是我最常用的。

您需要记住的另一点是，您通常希望有条件地添加这些事件侦听器:只有当某个组件处于活动状态或被用户使用时。否则，如果您的所有事件侦听器都在全局范围内，您可能会在错误的时间触发操作。

为了有条件地添加这些，我通常有一个函数来处理所有的 keyup 逻辑(创造性地命名为`this.handleKeyup()`)。当我的组件被激活时，我添加了一个事件侦听器，并将该函数作为回调函数。当我的组件被禁用时，我触发一个与回调函数相同的`removeEventListener()`。这样，你可以根据用户正在做的事情用不同的键触发不同的动作。

你可以把它带到另一个层次，通过测试是否`event.shiftKey === true`来测试用户是否也按住了 shift 键。你可以这样做，如果你试图在一个模态中捕获焦点，并希望防止用户从一个模态中后退。

## 3。管理 ARIA 状态

Web Accessibility Initiative 的富互联网应用程序(T2，WAI-ARIA，或者只是 ARIA)的可访问性规范有很多，但是当你开始使用交互式 JavaScript 时，你应该真正关注于属性。

很多交互性都集中在显示或隐藏页面上的内容。根据 W3C 规范，属性`aria-expanded`表示该元素或者它所控制的另一个分组元素当前是展开的还是折叠的。

您将希望确保您的元素使用适当的`aria-expanded`属性呈现:如果元素没有展开，则为 false，如果元素展开，则为 true。此属性应该应用于控制可扩展元素的元素。如果分组元素是控制元素的子元素，你不需要做任何特殊的事情，但是如果你有一个`<button>`来控制一个兄弟`<ul>`，你需要用`aria-controls`属性指明按钮控制列表(W3C 的 [aria-controls 文档)。该属性接受由交互元素控制的 ID 或 ID 列表。在我们的例子中，我们的标记应该是这样的:](https://www.w3.org/TR/wai-aria/states_and_properties#aria-controls) 

```
<button class="list-expander" aria-expanded="false" aria-controls="expandable-list-1">Expand List</button>
<ul id="expandable-list-1">
    <li><a href="http://example.com">Sample Link</a></li>
    <li><a href="http://example.com">Sample Link 2</a></li>
    <li><a href="http://example.com">Sample Link 3</a></li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要切换扩展状态。我通常的做法是使用`setAttribute()`方法。

```
const listExpander = document.querySelector('.list-expander');
const list = document.querySelector('#expandable-list-1');
listExpander.addEventListener('click', (e) => {
    if(list.getAttribute('aria-expanded') === "true") {
        list.setAttribute('aria-expanded', 'false');
    } else {
        list.setAttribute('aria-expanded', 'true');
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

注意，当我检查查看`aria-expanded`属性的值时，我使用了`=== "true"`。这是因为`getAttribute`返回字符串`"true"`或`"false"`，而不是实际的真或假值。(那一开始把我绊倒了)。

您可以将这种思路用于其他真/假 ARIA 属性。最常见的是，我用这个和`aria-hidden`来显示和隐藏模态对话框。

## 4。管理焦点

我们将在本指南中讨论的最后一件事是管理焦点。焦点是指浏览器中能够通过键盘操作的单一元素。当用户单击元素、使用 TAB 键在可聚焦的元素之间循环或者使用屏幕阅读器时，元素通常会获得焦点。基本上，您需要确保用户可以在任何时候直观地看出哪个元素处于焦点。

我最终管理焦点的最常见的地方是模态组件。

这里有一个我们需要解决的示例问题。我们有一个关于页面，其中包含一个人的简历和一个按钮，上面写着“联系此人”。此按钮打开包含联系人表单的模式。但是如果表单没有按照页面的自然 tab 键顺序排列(这在模态中很常见)，当用户点击 tab 键时，他们的键盘焦点就在模态之后。对于键盘和辅助技术用户来说，被设计糟糕的模态卡住并感到沮丧是很常见的。

为了解决这个问题，我们想做几件事:

1.  当模式打开时，将焦点移动到模式内的第一个可聚焦元素。
2.  确保用户可以在模式打开时通过键盘轻松关闭它。
3.  当模式关闭时，将焦点返回到模式打开时活动的元素。
4.  如果我们想非常小心的话，我们可以在模态中前后移动标签，这样用户就不能退出，除非他们关闭模态。

### 获取第一个可聚焦元素。

我有几个助手方法来帮助我确定给定上下文中所有可聚焦的元素和第一个可聚焦的元素。下面是我如何找到页面上所有可聚焦的元素(h/t to [Chris Ferdinandi](https://gomakethings.com/how-to-get-the-first-and-last-focusable-elements-in-the-dom/) )。

```
/**
 * Get all focusable elements inside of the specifed context.
 *
 * @param {String} [context='document'] The DOM context you want to search in.
 * @return {Array} Array of focusable elements
 */
function getFocusable(context = 'document') {
    let focusable = Array.from(context.querySelectorAll('button, [href], select, textarea, input:not([type="hidden"]), [tabindex]:not([tabindex="-1"])'));
    return focusable;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数使用`querySelectorAll`和一个通常可聚焦的选择器列表:`<button>`、具有`href`属性的链接、输入和具有 tabindex 集合(不是-1)的东西。我还通过移除任何隐藏的输入来过滤`<input>`选择器，因为那些输入是不可聚焦的。我对属性`tabindex`设置为-1 的元素进行了同样的过滤，因为这些元素应该只能通过 JavaScript 方法获得焦点，而不是在普通的选项卡索引中。我使用`Array.from`从`querySelectorAll`返回的节点列表中创建一个数组。

我喜欢这个函数的一点是，它还可以传入一个上下文。默认情况下，上下文被设置为`document`，因此它将在文档中找到所有可聚焦的元素。但是在上面的模态示例中，您可以将模态元素本身作为上下文传入，并获得模态元素中所有可聚焦元素的列表😎。

寻找第一个可聚焦的元素现在很简单，只需要弹出数组中的第一个元素。我通常有另一个助手函数来获取第一个可聚焦的元素，并不直接调用第一个元素。事情是这样的:

```
/**
 * Get the first focusable element inside of the specified context.
 *
 * @param {String} [context='document'] The DOM context you want to search in.
 * @return {Object} A DOM element
 */
export function getFirstFocusable(context = 'document') {
    let focusable = getFocusable(context);
    return focusable[0];
} 
```

Enter fullscreen mode Exit fullscreen mode

您传入一个上下文，它调用我们最初的`getFocusable()`函数并返回数组中的第一项。现在我们可以在该元素上调用`focus()`以编程方式聚焦第一个可聚焦的元素。它看起来像这样:

```
getFirstFocusable(modal).focus(); 
```

Enter fullscreen mode Exit fullscreen mode

### 确保用户可以在模态打开时通过键盘轻松关闭模态

我们在前面讨论规划通用键盘交互时已经部分解决了这个问题。当您希望用户能够退出组件时，这是一个很好的例子。

您还可以在模式和站点内容之间添加一个覆盖层，该覆盖层可以通过关闭模式的点击事件来点击和聚焦。

### 当模态关闭时，将焦点返回到模态打开时活动的元素。

在我们的例子中，用户点击了一个按钮，然后他们的焦点跳到了模态上。当他们关闭模态时，我们想让他们的焦点回到触发模态的按钮上。使用`document.activeElement`属性，这实际上很简单。

当我们检测到一个模态应该打开并且在之前**我们将焦点转移到那个模态时，我们可以将当前的活动元素保存到一个变量中，像这样:** 

```
let previousActiveElement = document.activeElement; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以将焦点转移到我们的第一个可聚焦的元素，每当用户使用完模态并决定关闭它时，我们将焦点转移回我们保存的元素:

```
previousActiveElement.focus(); 
```

Enter fullscreen mode Exit fullscreen mode

现在用户又回到了他们开始的地方！

### 陷印 TAB 和 SHIFT + TAB 在模态内

正如我在上面提到的，如果我们想非常小心，我们可以在模态中向前和向后捕捉标签，这样用户就不能退出，除非他们关闭模态。

为此，我们需要在模态激活时监听`keyup`事件，下面是我用来捕获焦点的函数(这取决于上面的`getFocusable()`函数:

```
/**
 * Traps the tab key inside of the context, so the user can't accidentally get
 * stuck behind it.
 *
 * Note that this does not work for VoiceOver users who are navigating with
 * the VoiceOver commands, only for default tab actions. We would need to
 * implement something like the inert attribute for that (see https://github.com/WICG/inert)
 * @param {object} e the Event object
 */
export function trapTabKey(e, context) {
    if (e.key !== 'Tab') return;

    let focusableItems = getFocusable(context);
    let focusedItem = document.activeElement;

    let focusedItemIndex = focusableItems.indexOf(focusedItem);

    if (e.shiftKey) {
        if (focusedItemIndex == 0) {
            focusableItems[focusableItems.length - 1].focus();
            e.preventDefault();
        }
    } else {
        if (focusedItemIndex == focusableItems.length - 1) {
            focusableItems[0].focus();
            e.preventDefault();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们需要传入事件对象，这样我们就可以检测按下了什么键，以及用户“被困”在其中的上下文。

如果他们按的键是**而不是**TAB 键，我们可以安全地返回，什么也不做。

如果**是**TAB 键，我们得到模态中所有可聚焦的元素以及它们当前聚焦的元素。一旦我们有了这两样东西，我们就可以使用`indexOf`方法来告诉用户在这个上下文的 tab 键顺序中的位置。

如果他们按住 shift 键(`e.shiftKey === true`)，他们就在后退，所以我们希望在他们到达模态中的第一个可聚焦项时停止他们，并聚焦到最后一个可聚焦项:`focusableItems[focusableItems.length - 1].focus()`。

如果他们前进到模态(`focusedItemIndex == focusableItems.length - 1`)中的最后一个可聚焦项，我们需要聚焦第一个可聚焦项。

对于这两种情况，我们都需要调用`e.preventDefault()`来防止默认的 TAB 函数触发。但是对于所有其他情况，我们可以让它们正常跳转。

当用户关闭模式时，您需要确保移除您的`keyup`事件监听器，让他们的标签功能恢复正常。

## 结论

我们在这里已经介绍了很多，但是这应该是一个非常好的开始，让您开始开发可访问的交互式 JavaScript 站点和应用程序，并为您提供一个思考如何编写其他小部件和组件的框架。请记住:

1.  对可点击的元素使用`<button>`
2.  计划常见的键盘交互，如 ESC、箭头、Enter 和 TAB。
3.  思考并管理任何适当的 ARIA 状态。
4.  必要时管理焦点。

从一开始就记住这些技巧会节省你的时间和麻烦，你的用户会感谢你的！

PS:如果你想要更多的无障碍提示，我将推出一个免费的电子邮件课程:[常见的网站无障碍错误以及如何修复它们](https://benjamingrobertson.us15.list-manage.com/subscribe?u=aafc0f8e65dbc564446043b15&id=baaa94d97b)。通过在此注册[获得课程！](https://benjamingrobertson.us15.list-manage.com/subscribe?u=aafc0f8e65dbc564446043b15&id=baaa94d97b)*