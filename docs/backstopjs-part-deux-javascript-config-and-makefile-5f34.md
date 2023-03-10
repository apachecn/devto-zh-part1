# BackstopJS Part Deux: Javascript 配置和 Makefile

> 原文：<https://dev.to/angelariggs/backstopjs-part-deux-javascript-config-and-makefile-5f34>

*本文最初发表在我的博客上，[http://Angela Riggs . github . io](http://angelariggs.github.io/blog/backstopjs-part-deux-javascript-config-and-makefile)。*

* * *

我已经[写了以前](http://angelariggs.github.io/blog/visual-regression-testing)关于我的背刺的设置(我仍然很兴奋地说这是创作者推荐的[教程](https://www.npmjs.com/package/backstopjs#tutorials-extensions-and-more)的 V2 包！).自从那篇文章之后，我已经从 JSON 切换到 Javascript 配置，并添加了一个 Makefile 作为使用 BackstopJS 运行可视化回归测试的主要方法。

## 迭代过程

改变 BackstopJS 设置的主要动机是为了简化我们开发工作流的配置。我们通常为每个网站提供三个独立的环境——开发、试运行和生产。JSON 配置要求每个环境有三个独立的配置文件，这也意味着任何更改通常都要进行三次。有了 Javascript config 和 minimist 包，我可以将参数变量添加到一个配置文件中，这样就可以更容易地全面更新 URL 或场景。

另外，Javascript config 意味着我可以添加代码注释！这很重要，因为在大多数情况下，我不再运行可视化回归测试了。我的团队目前的做法是，负责每周部署的开发人员运行可视化回归测试。能够包含代码注释，以及为简单命令添加 Makefile 的进一步改变，使得将 BackstopJS 的责任移交给我团队中的开发人员变得非常简单。

## 后支撑设置

当前的设置步骤没有太大的变化——主要的区别是用`npm install minimist`在本地添加最小化的包(全局安装似乎不起作用；老实说，我还没有花时间去解决这个问题，因为在本地安装就可以了)。我创建了一个独立的可视化回归报告，因此任何人都可以将模板复制到新项目中。BackstopJS 测试目录现在看起来像这样:

```
tests
     |_ backstop
          |_ backstop_data
               |_ casper_scripts
               |_ env_reference
               |_ env_test
               |_ env_html_report
          |_ nodemodules
          |_ .env
          |_ backstop.js
          |_ package-lock.json
          |_ paths.js
          |_ README.md 
```

`backstop_data folder`是一个在您运行引用和测试时自动创建的目录，其中的`*_reference`、`*_test`和`*_html_report`文件夹也是如此。应该将`backstop_data`文件夹和`node_modules`一起添加到你的`.gitignore`文件中——将截图添加到版本控制中没有任何实际价值，这样做会增加很多不必要的臃肿。

文件是 BackstopJS 的配置，我将进一步分享我的配置。它包括基本环境 URL 的变量、保存屏幕截图的目录路径、如何设置屏幕截图的 URL 的选项，以及像视窗大小和 casper 标志这样的常规配置。`paths.js`文件，我也将在这里分享，是一组用于截图的 URL 路径。因此，如果我将我的基本开发 URL 设置为`http://dev-site.com`，那么路径列表将类似于`/about, /contact-us, /case-studies`。注意，路径列表包括前导正斜杠——您可以选择这样做，或者在基本 URL 上有一个结束正斜杠。对我来说，没有结尾斜杠的基本 URL 似乎更自然，但这也意味着每次创建 URL 路径列表时，需要记住前面的斜杠，这就有点麻烦了。

## Pathfile

`paths.js`文件没有多少内容；它看起来像这样:

```
var pathConfig = {};

pathConfig.array = [
  '/', // homepage
  '/company',
  '/blog',
  '/our-work',
  '/careers/open-positions',
  '/contact'
]

module.exports = pathConfig; 
```

## Javascript 配置

有趣的部分来了！这是我目前的背景材料配置:

```
/* Quick guide to BackstopJS commands

  backstop reference --configPath=backstop.js --pathFile=paths --env=local --refHost=http://site.dev
  backstop test --configPath=backstop.js --pathFile=paths --env=local --testHost=http://site.dev

*/

var args = require('minimist')(process.argv.slice(2)); // grabs the process arguments
var dotenv = require('dotenv').config(); // handles basic auth
var defaultPaths = ['/']; // default path just checks the homepage as a quick smoke test
var scenarios = []; // The array that'll have the URL paths to check

// env argument will capture the environment URL
// if you use one of the options below to pass in, e.g. --env=dev
var environments = {
  'dev': 'http://dev-site.com',
  'staging': 'http://staging-site.com',
  'prod': 'http://www.site.com'
};
var default_environment = 'prod';

// Environments that are being compared
if (!args.env) {
  args.env = default_environment;
}
// if you pass in a bogus environment, it’ll still use the default environment
else if (!environments.hasOwnProperty(args.env)) {
  args.env = default_environment;
}

// Site for reference screenshots
if (!args.refHost) {
  args.refHost = environments[args.env];
}

// Site for test screenshots
if (!args.testHost) {
  args.testHost = environments[args.env];
}

// Directories to save screenshots
var saveDirectories = {
  "bitmaps_reference": "./backstop_data/"+args.env+"_reference",
  "bitmaps_test": "./backstop_data/"+args.env+"_test",
  "html_report": "./backstop_data/"+args.env+"_html_report",
  "ci_report": "./backstop_data/"+args.env+"_ci_report"
};

// Work out which paths to use: an array from a file, a supplied array, or defaults
// We'll be using the array from paths.js
if (args.pathFile) {
  var pathConfig = require('./'+args.pathFile+'.js'); // use paths.js file
  var paths = pathConfig.array;
} else if (args.paths) {
  pathString = args.paths; // pass in a comma-separated list of paths in terminal
  var paths = pathString.split(',');
} else {
  var paths = defaultPaths; // keep with the default of just the homepage
}

// Scenarios are a default part of config for BackstopJS
// Explanations for the sections below are at https://www.npmjs.com/package/backstopjs
for (var k = 0; k < paths.length; k++) {
  scenarios.push (
    {
      "label": paths[k],
      "referenceUrl": args.refHost+paths[k],
      "url": args.testHost+paths[k],
      "hideSelectors": [],
      "removeSelectors": [],
      "selectors": ["document"], // "document" will snapshot the entire page
      "delay": 1000,
      "misMatchThreshold" : 0.1
    }
  );
}

// BackstopJS configuration
module.exports =
{
  "id": "project_"+args.env+"_config",
  "viewports": [
    {
      "name": "desktop",
      "width": 1600,
      "height": 2000
    },
    {
      "name": "mobile",
      "width": 375,
      "height": 2000
    }
  ],
  "scenarios":
    scenarios,
  "paths":
    saveDirectories,
  "casperFlags": ["--ignore-ssl-errors=true", "--ssl-protocol=any"],
  "engine": "phantomjs", // alternate can be slimerjs
  "report": ["browser"],
  "debug": false
}; 
```

Javascript 配置的目标是尽可能简单地运行可视化回归测试——以最小的努力获得最大的回报。例如，在 90%的时间里，我们将检查环境本身；例如，在试运行时运行引用，部署变更，然后在试运行时运行测试。因此，如果所有的参数都需要引用 staging 的参数，我们让`env`参数决定截图目录的命名，以及传入的 URL。如果您没有为`env`传入任何参数，配置允许生产 URL 和命名为默认。

## 处理基本授权

除了更新我的配置方法之外，我还更新了我的站点基本认证方法。我使用一个名为 [dotenv](https://www.npmjs.com/package/dotenv) 的 npm 包。我在我的`backstop`文件夹的根目录下创建了一个本地的`.env`文件来保存网站基本认证的用户名和密码。它看起来像这样:

```
# Format for basic auth getting passed in is:
# `username:password`
BASIC_AUTH=uname:pwrd 
```

回到我的`backstop.js`文件，我将修改我的环境部分中的值，如下所示:

```
var environments = {
  'dev': ''http://'+process.env.BASIC_AUTH+'\@dev-site.com'', // note we need to escape the @ symbol
  'staging': 'http://staging-site.com',
  'prod': 'http://www.site.com'
}; 
```

这种设置意味着我们永远不必将我们的基本授权信息提交给回购。项目`.gitignore`包含此文件；因此，任何在项目中使用 BackstopJS 的人只需在本地创建一个`.env`文件，并填写`BASIC_AUTH`值。

## Makefile

Makefile 真正简化了可视化回归过程。BackstopJS 命令可能相当乏味，将它们浓缩成一个简短的 Make 命令对我团队中的开发人员来说是一个巨大的推动。

```
BACKSTOP_BASE := ./tests/backstop
# ------------------------------------------------------------------------------

# NOTES:
# This Makefile assumes you've gone through the README steps in tests/backstop
# This Makefile is also only for running references and tests against the same environment
# Use the setup in tests/backstop to compare different environments

prod-reference:
  @cd $(BACKSTOP_BASE) && backstop reference --configPath=backstop.js --pathFile=paths --env=prod

prod-test:
  @cd $(BACKSTOP_BASE) && backstop test --configPath=backstop.js --pathFile=paths --env=prod

prod-report:
  @cd $(BACKSTOP_BASE) && backstop openReport --configPath=backstop.js --env=prod

staging-reference:
  @cd $(BACKSTOP_BASE) && backstop reference --configPath=backstop.js --pathFile=paths --env=staging

staging-test:
  @cd $(BACKSTOP_BASE) && backstop test --configPath=backstop.js --pathFile=paths --env=staging

staging-report:
  @cd $(BACKSTOP_BASE) && backstop openReport --configPath=backstop.js --env=staging

dev-reference:
  @cd $(BACKSTOP_BASE) && backstop reference --configPath=backstop.js --pathFile=paths --env=dev

dev-test:
  @cd $(BACKSTOP_BASE) && backstop test --configPath=backstop.js --pathFile=paths --env=dev

dev-report:
  @cd $(BACKSTOP_BASE) && backstop openReport --configPath=backstop.js --env=dev 
```

在 Makefile 之前，一个命令应该是这样的:`backstop reference --configPath=backstop.js --pathFile=paths --env=dev`。现在，他们只要跑`make prod-reference`！

注意，Makefile 只允许在相同的环境下运行引用和测试。这是因为比较是在目录命名之外进行的，例如，`staging_reference`屏幕截图与`staging_test`屏幕截图进行比较。因此，如果你需要传入两个不同的 URL，你需要手动运行整个命令，使用一个特定的`env`参数和`refHost` / `testHost`参数。大概是:`backstop reference --configPath=backstop.js --pathFile=paths --env=screenshots --refHost=http://site-dev.com`，其次是`backstop test --configPath=backstop.js --pathFile=paths --env=screenshots --testHost=http://site-staging.com`。`env`参数是屏幕截图目录的命名方式，因此您只需为引用和测试命令传递相同的参数。

## 好处

对我来说，最大的好处是简化流程的价值。我们每周都会部署，这意味着我们每周都会针对多个站点和每个站点的多个环境运行可视化回归测试。使用 minimist 切换到 Javascript 配置，特别是为命令添加 Makefile，极大地简化了我们的部署过程。现在每个项目都有一个单独的配置文件，和一个用于截图的 URL 路径的文件，而不是三个。

在配置中创建参数变量减少了打字错误或错误的机会。令人沮丧的是，很容易忘记参数的`--`(例如`--env`)，或者打错`refHost`或`testHost` URL。有了变量和默认值，开发人员就少了一件担心的事情。

到目前为止，Makefile 是最大的成功。它极大地降低了 BackstopJS 命令的复杂性，并使开发人员在每周部署环境链时针对开发、准备和生产运行可视化回归测试变得非常容易。

重温我的 BackstopJS 设置并找出如何使它更简单的过程是很有趣的。如果你正在使用 BackstopJS，并且一直在寻找一种方法来简化你的工作流程，我希望这篇文章能有所帮助！一如既往，欢迎在下面的评论区留下任何问题或评论。