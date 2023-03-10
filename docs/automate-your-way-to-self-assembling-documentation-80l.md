# 自动组装文档

> 原文：<https://dev.to/dmfay/automate-your-way-to-self-assembling-documentation-80l>

文档使人们使用你的软件成为可能，而不需要像你写它一样投入大量的工作去理解它。这也是维护代码的最乏味的杂务之一，众所周知，这是程序员最不喜欢的家务活。我也不例外，但与此同时，我经营着一个相当受欢迎的图书馆， [Massive.js](https://github.com/dmfay/massive-js) ，如果它对地球上的其他人有用，它绝对需要文档。因此，本着拉里·沃尔的[第一美德](http://threevirtues.com)的精神，我已经竭尽全力尽可能少做这件事。

## 什么是文档？

这些年来，文档采用了多种形式，从实际的死树书到手册页，再到从特殊格式的注释生成的 API 文档站点，以及介于两者之间的任何东西。每一种都有各种各样的优点和缺点:在可搜索性方面，其他任何东西都比书强，但如果你需要对某样东西进行更结构化的介绍，或者正在进行一项工作，书绝对有它们的位置。格式是一个独立的问题。

一个更重要的问题是:是什么让文档*变得好*？这自然是主观的，但一些基本原则是有意义的:

*   良好的文档是最新的:新特性和变化在集成时就被记录下来，并且最新版本的文档总是最新的
*   好的文档是完整的:它涵盖了系统中每个值得注意的 API 函数、配置设置、选项以及最终用户可能会遇到的问题
*   好的文档是可读的，尤其是对于经验有限的人(他们比专家更需要它！)
*   好的文档需要尽可能少的时间和精力来维护，同时尽可能不牺牲以上三个方面

因为获得海量数据的唯一途径是从`npm`或 GitHub，所以可以相当安全地假设任何需要文档的人都会在线。这使事情变得更容易:我可以作为静态站点提供文档。我所说的“静态”并不是指它永远不变，而是指它只是普通的 HTML 和 CSS，也许是一点 JavaScript 来活跃一下气氛。没有数据库，没有后端 API，没有服务器端处理。

## 全自动化

让*做点什么*最简单的方法就是使用文档生成器。这些已经存在很久了；perldoc 和 JavaDoc 可能是最著名的，但是 JSDoc 也已经存在了将近 20 年。有了它，我可以用包含详细使用信息的注释块来装饰每个功能和模块，然后运行一个程序，将这些块组装成一个静态网站。

JSDoc 注释块，像 JavaDoc 一样，由一个`/**`头表示。这一个显示了一个函数，用`@param`和`@return`标签分别表示它的参数和返回值。其他标签覆盖模块和类的属性，或者为 JSDoc 编译器提供提示，以改变其组织页面的方式(在 JavaScript 这样的语言中，区分实体可能很棘手！).

```
/**
 * Perform a full-text search on queryable fields. If options.document is true,
 * looks in the document body fields instead of the table columns.
 *
 * @param {Object} plan - Search definition.
 * @param {Array} plan.fields - List of the fields to search.
 * @param {String} plan.term - Search term.
 * @param {Object} [options] - {@link https://dmfay.github.io/massive-js/options.html|Select options}.
 * @return {Promise} An array containing any query results.
 */
Queryable.prototype.search = function (plan, options = {}) { 
```

Enter fullscreen mode Exit fullscreen mode

我不需要复杂的`.jsdoc.json`配置来实现这个:

```
{
  "source": {
    "include": ["index.js", "lib", "README.md"]
  },
  "opts": {
    "recurse": true
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

剩下的就是在我的`package.json`中添加一个脚本来运行 JSDoc:

```
"docs": "rm -rf ./docs/api && jsdoc -d ./docs/api -c ./.jsdoc.json -r" 
```

Enter fullscreen mode Exit fullscreen mode

现在`npm run docs`生成了一个新的 API 文档站点——我所要做的就是更新我的注释块并记住运行它！

这张照片有两个问题:

首先，这份文件提出的问题和它回答的问题一样多。什么是文档正文字段？我只是假设人们知道那些是什么。对`options`物体的描述是——嗯，这有点超前了。`Queryable.search`不存在于虚空中:为了理解这个函数做什么，开发者需要理解`options`对象能做什么，文档和它们的主体字段是什么。这对于一个 JSDoc 注释来说太多了。尤其是当你考虑到`options`对象适用于 Massive 的大部分数据访问功能时，其中许多都与文档有关！很明显，我需要第二层文档，作为*概念*，而不是纯粹的技术参考。但是:我不能自动生成这样的东西。

第二，我必须记得运行它。这是一行 shell 脚本。我不应该记得运行它。让我们先解决这个问题:

## 生命周期事件

几个`npm`任务为您提供了在执行之前或之后从 package.json 执行脚本的钩子。有些，比如`npm test`，要求你将任务本身作为一个脚本来实现。一个带有钩子的任务是`npm version`。`preversion`脚本在碰到版本号之前执行；`version`脚本在碰撞之后执行，但是在它将更改的包定义提交到源代码控制之前执行；并且`postversion`脚本在提交之后执行。

当我发布一个新版本时，我只需要确保 API 文档是最新的。在`preversion`中运行 JSDoc 是完美的。如果我想将文档更新与版本升级分开，我可以将一个 shell 脚本放在钩子中运行:

```
#!/bin/bash

echo "regenerating API docs"

npm run docs

echo "committing updated API docs"

git add docs/api

git commit -m "regenerate api docs" 
```

Enter fullscreen mode Exit fullscreen mode

## 概念参考:Jekyll 和 GitHub 页面

JSDoc 是一个很棒的工具，但是它不能介绍和连接用户为了使用 Massive 而需要理解的概念。唯一的方法是我自己写，但是我不想写原始的 HTML，我可以用更友好的 Markdown 来代替。幸运的是，并不缺少可以将 Markdown 转换成 HTML 的静态站点生成器。我在我的[博客](https://di.nmfay.com/)上使用[弗莱德莫斯](https://github.com/sapegin/fledermaus)。或者我可以再次使用 [ReadTheDocs](https://readthedocs.org/) ，一个专注于文档的生成器服务。这是遗留文档已经托管的地方。但是在 Massive 上几乎只有我一个人，所以我想集中管理。GitHub Pages 使用 Jekyll 这使得这个决定很容易。

我认为使用 Jekyll 最困难的部分是决定主题。除此之外，`_config.yml`是非常基本的，一旦我发现我可以通过将主题的基础复制到我自己的`_layouts/default.html`来定制布局，并得到我的样式表的路径，剩下的就是编写内容了。

Jekyll 站点中的页面，就像 [dev.to](https://dev.to) 和(可能)其他平台上的文章一样，都是降价文件，在文件的顶部有一个可选的“封面内容”部分(博客文章需要封面内容)。

在本地查看文档需要几个步骤:

1.  通过包管理器安装 Ruby
2.  `gem install bundler`
3.  创建一个`Gemfile`，它会拉入 [`github-pages`](https://github.com/blog/1581-cutting-the-github-pages-gem) 红宝石
4.  `bundle install`
5.  然后，除非我给`Gemfile`添加更多的依赖项，否则我可以`bundle exec jekyll serve`并将浏览器指向运行 Jekyll 的本地地址

此时，我的工作树中有一个`docs/`目录:

```
docs
├── api              # JSDoc output
├── assets
│   └── css
│   └── style.scss   # Jekyll handles processing SCSS
├── _config.yml      # Main Jekyll config
├── Gemfile          # Jekyll dependency management
├── Gemfile.lock     # Auto-generated Jekyll dependency manifest
├── index.md         # Documentation landing page
├── _layouts
│   └── default.html # Customized HTML layout template
├── some-docs.md     # Some documentation!
└── _site            # Jekyll output (this is .gitignored) 
```

Enter fullscreen mode Exit fullscreen mode

GitHub 页面可以托管来自`master`分支的整个存储库、`master`中的 docs 目录或单独的`gh-pages`分支。虽然我有一个 docs 目录，但我不希望每次在`master`提交时都更新我的文档。Massive 的文档需要是人们从`npm install`获得的最新版本的库，而不是我做的每一个小改动。所以我创建了一个`gh-pages`分支，清空它，并将我的 docs 目录复制到根目录中(减去`_site`，因为 GitHub Pages 自己运行 Jekyll)。包含 JSDoc 输出，这样静态站点就完整了，包含了概念和技术参考。

经过推动和一点点的尝试和错误，我有了网站和工作！但是我真的真的不想在每次发布的时候都要手动完成所有这些工作。

## 自动化文档管理

我的生命周期事件脚本让我基本上忽略 JSDoc，只要我保持它是最新的。如果我可以编写出更新`gh-pages`分支的步骤，我就可以使用另一个生命周期事件来减少管理其余部分的工作。因为所有的事情都发生在另一个分支中，在与`postversion`的版本碰撞后开始就足够了。

首先要做的是:我要为哪个版本更新文档？这些信息存在于几个地方:我可以寻找最新的 git 标记，或者从 package.json 中取出它。我对`jq`(想想 JSON 的`sed`)非常熟悉，所以我选择了`git describe` :

```
type jq >/dev/null 2>&1 && { VERSION=$(jq .version package.json); } || exit 1 
```

Enter fullscreen mode Exit fullscreen mode

这一行首先确保`jq`存在于系统中。如果是，它将变量`VERSION`设置为 package.json 中的`version`字段；否则，它会以失败错误代码中止执行。

下一步是获取当前的分支名称和版本碰撞的提交 SHA:

```
BRANCH=$(git symbolic-ref --short HEAD)
COMMIT=$(git rev-parse --short "$BRANCH") 
```

Enter fullscreen mode Exit fullscreen mode

然后，是时候开始工作了。我想确保工作树中没有旧文件，但我有一个定制的。忽略我需要保留的东西。

```
git clean -dfqx
git ls-tree --name-only gh-pages | grep -v "\(.gitignore\)" | xargs -I {} rm -r {} 
```

Enter fullscreen mode Exit fullscreen mode

从工作树中删除所有未跟踪的文件。然后我`git ls-tree`分支的根目录，执行一个逆向 grep 来过滤掉我的。gitignore，并使用`xargs`将其中的所有其他文件传递给`rm -r`。最后，除了. gitignore，工作树应该是完全空的。现在从最初的分支:
获取最新的文档

```
git checkout "$BRANCH" -- docs

mv docs/* .

rm -r docs 
```

Enter fullscreen mode Exit fullscreen mode

相当简单:它只检查*docs 目录，将其内容移动到工作树根，并清理现在为空的目录。这是最后冲刺阶段。* 

```
git add .

git commit -m "regenerate documentation for $VERSION ($BRANCH $COMMIT)"

git checkout "$BRANCH" 
```

Enter fullscreen mode Exit fullscreen mode

添加文件，用新的版本号和源提交信息提交它们。然后完成所有这些工作，再次签出原来的分支。我*可以*推送`gh-pages`，但是我对自动上传有点偏执，所以我的脚本只是`echo`提醒手动上传。

所有这些都放在另一个 shell 脚本中，然后我只需确保该脚本在`postversion`上运行！

## 开始到结束

现在，当我`npm version`创建一个 Massive 的新版本时，我的脚本会触发生命周期事件。`preversion`脚本更新我的 API 文档并在其他事情发生之前提交它。标准的`version`功能在此时接管，在 package.json 中设置新版本，提交更改，并用新版本标记它。最后，我的`postversion`脚本汇编了最新的文档并提交给`gh-pages`分支。留给我手动做的唯一一件事就是将那个分支和`master`以及新标签一起推。只要我保持我的 JSDoc 注释和参考文档是最新的，其余的事情就会迎刃而解！