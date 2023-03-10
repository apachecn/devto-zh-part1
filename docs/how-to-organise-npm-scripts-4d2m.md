# 如何在 package.json 中组织 NPM 脚本？

> 原文：<https://dev.to/ycmjason/how-to-organise-npm-scripts-4d2m>

我喜欢使用 npm 脚本作为我的构建工具。通过这样做，我们可以确保脚本不那么复杂并且易于阅读(在我看来，与 gulp/grunt 相比)。

但是随着项目越来越大，添加到`package.json`中的脚本越来越多，事情变得越来越不可维护。

您可能尝试过在脚本之间添加空行，但是 figure `npm install`只会删除您的空行，留给您的只有失望。

这里有一个小技巧，可以让我们将脚本分类。

```
{
  "scripts": {
    "test": "nyc mocha --recursive",
    "preversion": "git checkout master && git merge --squash dev && npm test",
    "version": "git add -A",
    "postversion": "git push && git push --tags && git checkout -",
    "\n# TESTING SCRIPTS:": "",
    "createTestPages": "node ./scripts/createTestPages.js",
    "test:watch": "mocha --recursive --watch",
    "coverage": "nyc report --reporter=lcov > coverage.lcov && codecov",
    "lint:js": "eslint .",
    "\n# HUSKY GIT HOOKS:": "",
    "precommit": "lint-staged"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的技巧是定义一些类别键。通过将`\n`添加到键的前面，我们将得到一个非常整洁的`npm run`结果。

```
> npm run
Lifecycle scripts included in grab-lyrics:
  test
    nyc mocha --recursive
  preversion
    git checkout master && git merge --squash dev && npm test
  version
    git add -A
  postversion
    git push && git push --tags && git checkout -

available via `npm run-script`:

# TESTING SCRIPTS:

  createTestPages
    node ./scripts/createTestPages.js
  test:watch
    mocha --recursive --watch
  coverage
    nyc report --reporter=lcov > coverage.lcov && codecov
  lint:js
    eslint .

# HUSKY GIT HOOKS:

  precommit
    lint-staged 
```

Enter fullscreen mode Exit fullscreen mode

要记住的一件事是，始终把 NPM 生命周期挂钩放在脚本的顶部，因为 NPM 会抓住它们，把它们放在顶部。

你觉得这一招怎么样？