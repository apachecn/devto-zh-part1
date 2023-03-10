# 玩 CSS

> 原文：<https://dev.to/horia141/playing-with-css-3bj7>

在很长一段时间里，我和 CSS 是朋友的朋友类型的关系。当然，当我们共同的朋友 frontend development 在的时候，我们会一起出去玩，但我们绝不会独自出去玩。然而，我总是对这种状况感到不舒服，因为它是网络的核心技术之一。我确信我对货物崇拜的态度并不恰当。这里面一定有一些逻辑。

确实有。回过头来看，我意识到我从来没有真正有原则地学习过 CSS。它总是支离破碎的，由当时的需要所驱动。所以在通读了一遍 CSS 教程后，我觉得我有了更好的理解。

那时我需要的是一种实际测试我的新知识的方法。幸运的是，这是以这个博客的简历部分的形式出现的。或者说，缺乏它。我一直想添加一个，以反映 StackOverflow 和 LinkedIn 上的信息，但我知道我必须超越这个 Jekyll 主题的界限，在这里需要比我所知道的更多的 CSS 来获得看起来更好的东西。所以有了这些，我开始添加东西。

第一步是编写包含所有信息但没有样式的 HTML。你可以在这里看到原始 HTML [。有珍贵的额外绒毛(div 上 div，额外的类等)。)，而是用语义 HTML5 来组织一切:列表、段落、章节等等。这里的造型本身就是](https://github.com/horia141/horia141.github.io/blob/master/cv.html)。

首先要注意的是样式表本身非常小。它有几行非常简单的注释代码。如果优先考虑的话，它本来可以做得更小。

此外，普通的 CSS 足以得到一个好看的页面。当然，没有那么多事情要做，但是知道不是所有的事情都需要 [Bootstrap](http://getbootstrap.com/css/) 或者其他 CSS 框架、网格，甚至是重置样式表是件好事。就排版而言，这种风格本身借鉴了 Jekyll 主题，并带有少量 StackOverflow 的标签样式。唯一的外部依赖是对 GitHub、StackOverflow 和 LinkedIn 图标的 [FontAwesome](http://fontawesome.io/) 。

此外，造型也很灵敏。事实证明，这比预期的要容易。事实上，在整个样式表中有一个单一的`@media`规则，它为超过`800`像素的视口设置了对`body`标签垂直大小的限制。其他一切都依赖于 HTML 默认布局的自然流程，这是非常移动友好的。版面设计的唯一工作是将段落、标题和章节隔开。

最后，不需要太多的 CSS3。唯一的用途是`@charset`和`@media`规则、`:last-child`状态选择器和`rem`单元。后者在定义边距和填充时非常有用，因为一切都是相对于根元素上的`font-size`集合，而不是最近的祖先元素。因此，几乎所有的尺寸都是 T5，除了边框和标签。

总的来说，这是一次非常愉快的经历，并且无疑增强了我处理 web 前端开发的信心。