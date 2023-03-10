# 设置一个最小但有用的 JavaScript 开发环境

> 原文：<https://dev.to/corgibytes/setting-up-a-minimal-yet-useful-javascript-dev-environment>

在一个框架、库和工具无处不在的时代，可能很难决定什么时候使用什么工具。

根据我的经验，一旦你决定写一个模块或 CLI 工具，你要做的第一件事就是建立一个环境。有人爱，有人恨。但是不管你站在哪一边，你都很可能会花太多时间去做这件事，去润色设置的每一个方面。

当然，您可以使用 webpack、eslint、jasmine 甚至 TypeScript 来获得很好的编译错误消息。事实是，大多数时候，作为开发人员，我们可以使用几乎不需要配置的工具。这些“开箱即用”的工具通常是完全可以接受的，将帮助我们直接解决问题，同时提供几乎即时的反馈循环。

当谈到最小设置时，脑海中浮现的是测试、林挺、观察更改并确保在提交更改之前不会破坏任何东西。

这里有一个帮助你在五分钟或更短时间内从一无所有到富有成效的步骤(取决于 NPM 的心情)。

## Init Node.js 项目和 GIT 资源库

```
# Create a directory and cd into it (#protip – $_ holds argument of your last command)  $  mkdir  awesome-module  &&  cd  $_  # Initialize Node.js project with default settings  $  npm  init  --yes  # Create initial folders and files  $  mkdir  lib  test  $  touch  index.js  lib/meaningOfLife.js  test/index.test.js  test/meaningOfLife.test.js  # Initialize GIT repository and create initial commit  $  git  init  $  git  add  -A;  git  commit  -am  "Awesome Module, day one" 
```

Enter fullscreen mode Exit fullscreen mode

## 安装工具

这里，我们将使用四个简单的模块，每个模块都有一个目的。[艾娃](https://github.com/avajs/ava)用于测试，[标准](https://github.com/feross/standard)用于林挺， [Chokidar-cli](https://github.com/kimmobrunfeldt/chokidar-cli) 用于文件查看， [Precommit-hook](https://github.com/nlf/precommit-hook) 用于自动运行 npm 脚本。

为什么是那些工具？因为它们不需要任何配置，并从你的大脑中取走认知负荷。少了一件需要思考和担心的事情。

```
$ npm i --save-dev ava standard chokidar-cli precommit-hook 
```

Enter fullscreen mode Exit fullscreen mode

记得创建一个`.gitignore`文件，并在其中添加`node_modules`！我们不希望它出现在我们的存储库中。

## 设置工具

打开`package.json`并将这些脚本添加到您的文件中。

```
"scripts": {
  "test": "ava",
  "lint": "standard",
  "dev": "chokidar '**/*.js' -c 'standard && ava'"
},
"pre-commit": ["test", "lint"], 
```

Enter fullscreen mode Exit fullscreen mode

你完成了！一旦你运行`npm run dev`,你将得到所有由 Standard.js 链接的 JS 文件和由艾娃运行的测试。没有更多的事情，你可以马上开始工作。

创建 GIT 提交也是如此。除非您的所有测试都是绿色的，并且 linter 很满意，否则您无法做到这一点。

两件毫无价值的事情:

1.  您不必全局安装`standard`或`ava`，因为它们是在`node`上下文中运行的。
2.  因为我们在`dev`脚本中使用了`&&`而不是`;`，除非你通过了 linter 规则，否则测试不会被触发。这使得反馈循环更快。

## 使用设置

因为环境假设您将以 TDD 方式工作(您可能应该这样做！)，一旦您运行了您的`dev`脚本，您就可以创建测试，它们将被添加到测试套件中，而不需要重新启动一个观察器或者重新构建任何东西。

让我们创建第一个测试。

```
// test/meaningOfLife.test.js
const test = require('ava')
const meaningOfLife = require('../lib/meaningOfLife')

test('Real meaning of life', (t) => {
  t.is(meaningOfLife(), 42)
}) 
```

Enter fullscreen mode Exit fullscreen mode

一旦您保存了文件，您将立即得到通知，您的一个测试失败了。让我们修理它。

```
// lib/meaningOfLife.js
module.exports = () => 42 
```

Enter fullscreen mode Exit fullscreen mode

我们又回到绿色了！就这么简单。让我们创建另一个将数字参数相乘的模块，对这个模块进行单元测试，看看它是否能很好地与我们的“生命的意义”一起工作(注意，这已经是一个集成测试，而不是单元！).

```
// lib/multiply.js
module.exports = (number) => {
  if (typeof number !== 'number') throw new TypeError('Only numbers can be multiplied!')
  return number * 2
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// test/multiply.test.js
const test = require('ava')
const multiply = require('../lib/multiply')

test('Can multiply numbers', (t) => {
  t.is(multiply(10), 20)
})

test('Throws when you try to multiply non-number value', (t) => {
  t.throws(() => multiply('ohai!'), 'Only numbers can be multiplied!')
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，一起测试一下:

```
// test/index.test.js
const test = require('ava')
const awesomeModule = require('../index')

test('Works nicely together', (t) => {
  t.is(awesomeModule(), 84)
}) 
```

Enter fullscreen mode Exit fullscreen mode

```
// index.js
const meaningOfLife = require('./lib/meaningOfLife')
const multiply = require('./lib/multiply')

module.exports = () => multiply(meaningOfLife()) 
```

Enter fullscreen mode Exit fullscreen mode

有用！仅在几分钟内，我们就让一切正常运行。

作为开发人员，我们经常被闪亮的新工具所吸引。我们似乎忘记了这些东西应该使我们的工作更容易、更快、更少出错。最简单的解决方案已经足够了，比我们想象的要多。我们应该把时间花在编写软件本身上，而不是花在安装上。遵循这些步骤将会让你做到这一点。

一旦你的项目开始增长，你可能会发现自己需要更复杂的东西。然而，在大多数情况下，这是不会发生的。这些工具将在很长很长一段时间内很好地满足您的需求。