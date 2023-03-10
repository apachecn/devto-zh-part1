# (有点)修复 iOS Safari 中的自动对焦

> 原文：<https://dev.to/nevon/sort-of-fixing-autofocus-in-ios-safari-4675>

> *本文原载于 2016 年 Medium。在这一点上可能已经过时了。*

### (有点)修复 iOS Safari 中的自动对焦

> *我的一位同事正在过渡到我曾经参与的前端团队。为了让他在精神上为他的前端开发之旅做好准备，我给他发了一份时事通讯，我称之为每日前端黑客。我现在把它们发布到媒体上，让全世界欣赏*

假设您正在构建一个表单，您希望通过自动聚焦于第一个输入字段来帮助用户。

```
<form>
    <input type="email" name="email" placeholder="foo@example.com" autofocus />
    <input type="password" name="password" />
</form> 
```

你点燃它，尝试它，它工作得很好。装运它！

一段时间后，有人来找你，说它在 iOS Safari 中不起作用。你可以去[caniuse.com](http://caniuse.com/#search=autofocus)看看它确实不被浏览器支持。哦，没什么大不了的，我们可以用一点 Javascript 来解决这个问题。

```
document.addEventListener('DOMContentLoaded', () => {
    Array.prototype.slice.call(document.querySelectorAll('input'))
        .filter((el) => el.hasAttribute('autofocus'))[0]
        .focus()
}) 
```

令你惊讶的是，你发现这也不起作用！

事实证明，苹果*真的*不希望你关注用户没有点击的输入字段。不仅仅是不支持自动对焦属性，事实上你还让情况变得更糟！

<figure>[![](img/3e00a710773a3588cfa3db8d8dae9cc6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X_Nqi2j1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0N27dTsgnb3VTJzFsnBOIA.jpeg) 

<figcaption>我在为 Safari 开发</figcaption>

</figure>

时想做的事情

看，在用户与页面交互之前，即使手动调用元素上的焦点也不起作用。如果输入在 iframe 中，并且你试图在用户交互之前调用 focus，键盘打开，输入**而不是**获得焦点，并且在键盘上打字完全没有任何作用。作为一个额外的好处，如果视窗滚动，无用的，闪烁的光标将显示在输入之外的某个地方。

我还没有找到任何官方资源来解释这个决定，但我不得不假设这是因为聚焦一个字段会弹出键盘，如果你没有任何填写字段的意图，这可能会很烦人。

#### 作伪聚焦

我们不能完全模仿自动对焦的行为，但我们可以非常接近。

聚焦一个场做三件事:

1.  设置焦点样式
2.  滚动页面，使字段位于视口的中间
3.  打开键盘

3 是苹果唯一反对的东西，但其他两个可以相当容易地实现。我将向你展示一个非常具体的例子，但为了你自己的理智，我建议你想出一些方法来抽象这个问题，这样你就不需要担心你是否真的专注于这个领域，或者你只是假装的。

> 在 Github 上查看 [@klarna/ui，看看我们是怎么做的(](https://github.com/klarna/ui) [programmaticFocus.js](https://github.com/klarna/ui/blob/master/lib/features/programmaticFocus/index.js) )。

第一部分很简单，要设置焦点样式，只需添加一个具有相同样式的类:

```
input:focus,
input.has-focus {
    border: green;
    color: black:
} 
```

由于有了 [Element.scrollIntoView](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) ，将输入内容滚动到视图中变得异常简单。

如果我们把它们放在一起，我们会得到这样的结果:

```
const isIos = () => !!window.navigator.userAgent.match(/iPad|iPhone/i)

const hasInteracted = (() => {
    let interacted = false

    const onTouchStart = {
        interacted = true
        document.removeEventListener(onTouchStart)
    }
    document.addEventListener('touchstart', 'onTouchStart')

    return () => interacted
})()

const FOCUS_TYPES = {
    REAL: 'real',
    FAKE: 'fake'
}

const getFocusType = () => (hasInteracted() || !isIos())
    ? FOCUS_TYPES.REAL
    : FOCUS_TYPES.FAKE

const focus = (input) => {
    switch getFocusType() {
        case FOCUS_TYPES.REAL:
            return input.focus()
        case FOCUS_TYPES.FAKE:
            input.classList.add('has-focus')
            const onBlur = (input) => {
                input.classList.remove('has-focus')
                document.removeEventListener(onBlur)
            }
            input.addEventListener('blur', onBlur)
            input.scrollIntoView()
    }
}

document.addEventListener('DOMContentLoaded', () => {
    const autofocusedInput = Array.prototype.slice.call(
            document.querySelectorAll('input')
        ).filter((el) => el.hasAttribute('autofocus'))[0]

    focus(autofocusedInput)
}) 
```

我们最终得到的是一个看起来有焦点并且位于视窗中心的区域。键盘不会弹出来，但这是我们能做到的最接近的了。

希望这对你有用。这些帖子的目的不是向您展示一些突破性的新前端技术，而只是分享一些我和我的同事多年来不得不实施的应对各种浏览器怪癖的技巧。

*这大部分是凭记忆写的，有一些输入来自* [*Xavier 通过*](https://medium.com/u/dc0f4cb7debe) *，所以可能会有一些不准确。如果我错过了什么，请留下评论。*