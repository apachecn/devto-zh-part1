# 对第一个 CSS 样式表的思考

> 原文：<https://dev.to/tomhodgins/reflections-on-the-first-css-stylesheet-33c>

这可能是第一个向公众发布的 CSS 样式表，它来自 kon W Lie 在 1994 年 10 月对 CSS 的最初提议。

当我在 2017 年回顾它时，我注意到这里有一些有趣的事情:

*   单行注释(此处所有注释都是以`#`开头的单行注释)
*   自定义设置级联(不基于选择器特异性进行计算)
*   对于 CSS 来说，`pt`、`mm`和`cm`单元和`px`一样古老/基础
*   设置`font.size`或`space.left`，然后使用`font.size * 2`和`space.left + 0.5cm`作为值，这就像今天 CSS 变量的前身
*   `.first`好像要么是`::first-line`或者`::first-letter`这样的伪元素，要么是`:first`、`:first-child`或者`:first-of-type`这样的伪类
*   `x > y ? a : b`类似于 at-rule 逻辑，如`@media`查询
*   看起来像是环境价值，CSS 中全新的东西
*   分离屏幕和印刷媒体风格

因此，即使 CSS 多年来不断发展，它仍然在努力匹配一些从一开始就打算的表达能力。

```
#
# This is an initial style sheet. It should provide fallback values
# with low influence. Also, at the end, the user has added some
# favorite settings
#

0%          # 0% impact, i.e. fallbacks

# first, set some common defaults

font.family = times
font.size = 12pt        # alternative units are px, mm, cm
font.slant = normal
font.weight = normal

h1.font.size = font.size * 3
h2.font.size = font.size * 2.5
h3.font.size = font.size * 2

strong.font.weight = bold
em.font.slant = italics
tt.font.spacing = proportional

align.style = left      # paragraph alignment
h1.align.style = center

space.left = 0pt        # set the white space surrounding paragraphs
space.right = 0pt
space.above = 4pt
space.below = 4pt
space.first = space.left + 0.5cm    # i.e. some extra indentation

head.space.above = 15pt         # straying from the 4pt default above

list.space.left = 1cm           # extra indentation for all lists
list.space.first = 1cm

# window-wide settings

AGE > 3d ? window.background = pale_yellow : window.background = white

window.foreground = black

window.width = 400px
window.height = REAL_HEIGHT - 50px

window.margin.left = 2cm
window.margin.right = 2cm
window.margin.above = 2cm
window.margin.below = 2cm

#
# paper works different
#

print.font.size = 10pt

#
# The user has redefined some of the default values below
#

100%    # first, redefine influence to dictatorship

font.family = helvetica

#
# after this, the rest of the influence is handed over to the style
# sheets of the incoming documents
# 
```

Enter fullscreen mode Exit fullscreen mode