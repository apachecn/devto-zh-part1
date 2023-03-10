# 这篇博文是用 Draft.js 写的

> 原文：<https://dev.to/ben/this-blog-post-was-written-using-draftjs>

拖延在编程中可能是一件美妙的事情:等待足够长的时间，开源世界将会呈现在你面前。很长一段时间以来，我一直想在这个网站的后端重新创建一个浏览器内文档编辑器，从 [TinyMCE](https://www.tinymce.com/) 到一个不那么固执己见的“产品”和更具可扩展性的东西，这样我就可以添加语法高亮，在 wysiwyg、markup 和 markdown 之间切换，以及任何未来想到的有趣功能，并且以一种理智的方式。理想情况下，它将与 [ReactJS](https://facebook.github.io/react/) 配合得很好，因为这是我想用于这个网站的动态部分的，并且绝对是我实现复杂 UI 的首选环境。

然后 [Draft.js](https://facebook.github.io/draft-js/) ，一个用于构建文本编辑器的 React 框架公布了。它不仅提供了我需要的所有东西，所以实现我想要的功能会有些困难，但这是正确的选择。由于该项目已经在 GitHub 上拥有[近 4000 颗星星，并被脸书用于主要项目的生产中，如](https://github.com/facebook/draft-js) [Notes](https://www.facebook.com/notes/) 和 [Messenger](https://www.messenger.com/) ，我可以预计这是一个会变得更好并保持稳定的框架。React 生态系统是一个非常好的组成部分，我很高兴能够使用它，它不仅运行良好，而且能够完美地利用应用程序的生命周期。

## React 是富文本编辑的正确选择

文本编辑是一个基本的状态同步问题。当我对文档的一部分进行修改时，我希望这些修改能够在 transpiled 标记中得到反映，而不会丢失任何状态或潜在的异步结果，并且这需要高效地发生。基于一个中心状态使用智能差异以声明方式呈现整个文档是这方面的完美模式。用户在应用程序中呆的时间越长，与应用程序的交互越多，状态不同步的风险就越大。React 的构建就是为了减少这个问题，并且在思想上与 UI 稳定性的最终目标一致。

## React 社区给了我信心

React 团队宣布的准备投身于任何大型项目的人数之多，使得在这样的环境中工作成为一种乐趣。当我得到 Draft.js 的消息时，也就是它发布的同一天，我已经看到了与我对这个框架的疑问相关的 Github 问题。我很快发现[的一个项目](https://github.com/evanc/backdraft-js)正在开发从 Draft.js 块到标记的转换，这启发了我的解决方案。尽管 Draft.js 是新的，但是社区的支持和一家 3000 亿美元的公司的支持让我感觉很舒服。

## 我的基本实现

我匆忙地把它写进了我的代码，因为时机非常好，而且我很高兴使用它，但是现在它还相当基础，缺少了许多我将在接下来的几周左右实现的特性。但是我相信我将能够逐渐增加我需要的特性，同时更好地理解这个灵活的 API。我立即使用了`convertFromRaw`和`convertToRaw`方法在 HTML 之间来回转换，尽管这是一项正在进行的工作。为了帮助管理复杂的富文本样式，我使用了`RichUtils`。我还没有深入研究过这个功能，但是把它放进去是很容易的。虽然 Draft.js 的一些概念很复杂，但是通过查看示例开始是非常简单的。

这是我的`render()`方法，代码大部分直接来自 Draft.js [rich editor 示例代码](https://github.com/facebook/draft-js/blob/master/examples/rich/rich.html)。

```
render() {
  const {editorState} = this.state;
  let className = 'RichEditor-editor';
  var contentState = editorState.getCurrentContent();
  let markedUpBlocks = backdraft(convertToRaw(contentState), MARKUP);
  if (!contentState.hasText()) {
    if (contentState.getBlockMap().first().getType() !== 'unstyled') {
    className += ' RichEditor-hidePlaceholder';
    }
  }

  return (
    <div className="RichEditor-root">
      <BlockStyleControls
        editorState={editorState}
        onToggle={this.toggleBlockType}
      />
      <InlineStyleControls
        editorState={editorState}
        onToggle={this.toggleInlineStyle}
       />
      <div className={className} onClick={this.focus}>
        <Editor
          blockStyleFn={getBlockStyle}
          customStyleMap={styleMap}
          editorState={editorState}
          handleKeyCommand={this.handleKeyCommand}
          onChange={this.onChange}
          ref="editor"
          spellCheck={true}
         />
        </div>
      <div>
        <input type="hidden" name="article[body_html]" id="article_body_html" value={markedUpBlocks.join("")} />
      </div>
    </div>
  );
}
```

## 为什么我需要这么复杂的编辑器？

这是我非常感兴趣的一个项目，我遇到了许多与 Draft.js 试图解决的问题相关的用例。这些问题包括动态生成内容，如用户键入时突出显示的标签，以直观的方式组织丰富的数据和文本，以及其他类似的情况。动态富文本编辑比我们意识到的更有用，我认为开发人员很多时候满足于脆弱的实现。这个项目涉及全面的丰富的经典文档文本编辑，在我看来，这是“基础案例”,是我心目中的界面的一个很好的起点，但这种交互性可以发挥很大作用的领域有很多。这个 API 对于 web 开发来说将会是一个重要的 API，我很高兴学习它。

## 最后

实现这一点很简单，需要一个温和渐进的学习曲线。在学习这个框架的过程中，即使没有越过冰山一角，也能有一些有用的东西可以使用，这是非常好的。起初，脸书没有提供一些基本的标记和降价翻译似乎很愚蠢，但原因是每个用例的不同需求使得提供一个一刀切的解决方案不现实。这对我来说很有意义，我相信为这些功能而构建的基本 API 会运行得非常好。我将开源任何来自我努力的好东西，并且我鼓励其他人也这样做。我预计在不久的将来，将会有许多即插即用的选项来满足不同的数据转换需求。将这留给兴奋的 React 开发人员社区有很大的潜力。