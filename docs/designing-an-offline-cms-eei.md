# 设计离线内容管理系统

> 原文：<https://dev.to/michaelmior/designing-an-offline-cms-eei>

首先是一个明显的问题。离线 CMS 到底是什么？拥有一个 CMS 的意义不就在于你不需要在离线状态下改变任何东西吗？是的，那是真的。但是最近我遇到了一个问题。

当我开始攻读硕士学位时，系里想让我创建一个网页，但我真正能做的就是提供无聊的旧静态内容。嗯，这并不完全正确，因为有人提议用我自己的 LAMP 堆栈建立一个反向代理，但对于我简陋的小主页来说，这似乎有些过头了。我也知道，我肯定不希望每次我想改变共同的内容时不得不一个接一个地编辑页面。我也不想每次事情发生变化时都必须手动上传。

我四处搜索了一下，决定从 [Cheetah](http://www.cheetahtemplate.org/) 模板引擎开始。它基本上拥有你需要的所有模板优点和一个漂亮、干净的语法。所以我创建了一些 Cheetah 模板，并编写了一个小脚本来生成我所有的文件，一切都很好。但我不想就此止步。所以我决定加入一些[的好脾气](http://wiseheartdesign.com/articles/2010/01/18/the-demise-of-css-why-sass-and-languages-like-it-will-triumph/)。(如果你不熟悉 SASS，看看这个链接，它会改变你的生活。)最后，为了更好地衡量，我决定加入 cssoptimizer 和 [jsmin](http://www.crockford.com/javascript/jsmin.html) 来压缩所有内容。

最后，为了满足我疼痛的手指，我决定编写一个 Makefile 来执行所有的任务:生成静态 HTML，优化 JS 和 CSS 代码，并将任何更改上传到服务器。

```
SERVER = yourserverhere

JS = $(wildcard src/js/*.js)
JS_OPT = $(patsubst src/js/%.js,web/js/%.js, $(JS))

SASS = $(wildcard src/sass/*.sass)
CSS = $(patsubst src/sass/%.sass,web/css/%.css, $(SASS))

TMPL = $(wildcard src/tmpl/*.tmpl)
TMPL_INC = $(wildcard src/tmpl/inc/*.tmpl)
HTM = $(patsubst src/tmpl/%.tmpl,web/%.htm, $(TMPL))

# Build all JavaScript, CSS, and XHTML files
all: $(JS_OPT) $(CSS) $(HTM)

# Remove all built files from the output directory
clean:
 rm -f web/*.htm
 rm -f web/js/*.js rm -f web/css/*.css

# Upload the final site to the server
put: all
 rsync -avr --delete web/ $(SERVER):~/public_html

# Optimize JavaScript files
$(JS_OPT): web/js/%.js: src/js/%.js
 cat $< | ./bin/jsmin > $@

# Optimize CSS files
$(CSS): web/css/%.css: src/sass/%.sass
 sass $< | ./bin/cssoptimizer -i $@

# Ensure XHTML is rebuilt when included templates change
$(HTM): $(TMPL_INC)

$(HTM): web/%.htm: src/tmpl/%.tmpl
 (cd src/tmpl; cheetah fill --nobackup --oext=$(suffix $@) --odir=../../$(dir $@) $(notdir $<)) 
```

Enter fullscreen mode Exit fullscreen mode

为了让它为你工作，你应该能够将`SERVER`改为你的 web 服务器的地址。我使用的目录结构如下所示:

*   箱子
*   科学研究委员会
    *   tmpl
    *   股份有限公司
    *   厚颜无耻
    *   射流研究…
*   网
    *   img

最后，这里是我使用的所有工具的下载位置的完整列表:

*   [Sass——语法上很棒的样式表](http://sass-lang.com/)
*   [JSMIN，JavaScript Minifier](http://www.crockford.com/javascript/jsmin.html)
*   [CSS 优化器](http://mabblog.com/cssoptimizer/download.html)

我在研究这篇文章时发现，Lakshmi Vyas 已经为 Python 提出了一个名为 T2 海德的解决方案。还有使用红宝石的[杰基尔](http://jekyllrb.com/)。这只是为您提供了更多的方法来构建适合您需求的定制工具。

如果你有任何生成静态网页内容的技巧，请在评论中发表！