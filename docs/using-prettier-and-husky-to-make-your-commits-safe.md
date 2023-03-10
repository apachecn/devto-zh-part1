# 使用漂亮和沙哑，使您的承诺安全。

> 原文：<https://dev.to/bartw/using-prettier-and-husky-to-make-your-commits-safe>

无论您是在团队中工作还是独自工作，具有一致格式的代码对于可读性和可维护性都是非常重要的。

#### Tl；博士；医生

使用更漂亮的来自动格式化您的 JavaScript，使用 husky 和 lint-staged 来添加一个预提交脚本，在您提交时运行更漂亮的脚本和您的测试。

查看完整代码:

[bartw/pretty_husky](https://github.com/bartw/pretty_husky)

#### ESLint vs JavaScript 标准风格 vs 漂亮

对于 JavaScript，有像 [ESLint](http://eslint.org/) 这样的解决方案可以检查你的代码是否一致。但是 [ESLint](http://eslint.org/) 需要大量的配置，虽然它可以自己修复一些东西，但当你出错时，它通常需要手动干预。

然后是 [JavaScript 标准样式](https://standardjs.com/)。这个模块和 ESLint 完全相反。它不需要任何配置，也可以自己修复一些代码。但是没有配置，很固执己见。

而且现在还有[更漂亮的](https://github.com/prettier/prettier)。pretty 和其他 lint 工具的区别在于，pretty 不会检查你的代码。它只是将您的代码作为输入，并将格式化的代码作为输出。就像 JavaScript 标准样式一样，appellite 是固执己见的，但是您可以选择自定义格式。因为 Prettier 不检查你的代码而是解析它，一切都会自动修复。听起来两全其美。

#### 你好漂亮

看到漂亮女人的伟大之处的最好方法就是去尝试。

打开你的终端，开始输入:

```
mkdir pretty_husky
cd pretty_husky
npm init -y
npm install --save-dev prettier
mkdir src
touch ./src/foo.test.js
code . 
```

现在，您可以在 foo.test.js 中编写一些格式不佳的代码:

```
test('this needs to be prettier.', () => {
expect(4).toBe(4)
}) 
```

并添加一个脚本 package.json 运行起来更漂亮:

```
"scripts": {
    "pretty": "prettier--write --tab-width 4 \"src/**/*.js\""
} 
```

现在，您可以更好地从终端运行:

```
npm run pretty 
```

您可以看到 foo.test.js 的内容已经更改为:

```
test("this needs to be prettier.", () => {
    expect(4).toBe(4);
}); 
```

#### 那只哈士奇呢？

漂亮使你的代码看起来漂亮，但是如果你在签入之前忘记运行它怎么办？

在持续集成期间，您可以作为一个步骤运行得更好，但是如果有更改，您必须再次签入代码。如果您确保没有“难看的文件”可以被推送或提交，事情会简单得多。

这就是哈士奇的用武之地。 [Husky](https://github.com/typicode/husky) 让创建 git 挂钩变得简单。Git 挂钩是 git 在事件之前或之后执行的脚本。如果哈士奇为我们运行得更漂亮，那么“丑陋的文件永远不会被提交。但没那么容易。Husky 可以运行得更漂亮，但更改后的文件不会添加到我们的提交中。

这个问题可以通过 [lint-staged](https://github.com/okonet/lint-staged) 来解决。Lint-staged 可以运行 Lint 工具，比如 Prettier，并立即将它们添加到 staged 文件中。

#### 勾搭我！

如果您还没有创建 git 存储库，现在是时候了，如果没有存储库，您的钩子将无法工作:

```
git init
touch .gitignore 
```

将“node_modules”添加到。gitignore 文件以避免签入过多:

```
node_modules 
```

回到您的终端，安装 husky 用于钩子，lint-staged 用于将格式化文件添加到 staged 文件，Jest 用于运行一些测试:

```
npm install --save-dev husky lint-staged jest 
```

创建一些脚本来连接 package.json 文件中的所有内容:

```
"scripts": {
    "test": "jest",
    "pretty": "prettier --write --tab-width 4 \"src/**/*.js\"",
    "precommit": "npm run pretty && npm test"
},
"lint-staged": {
    "*.js": [
        "npm run pretty",
        "git add"
    ]
} 
```

在 foo.test.js 中做一些难看的失败的测试:

```
test('ugly', () => {
expect(4).toBe(4)
})

test('failing', () => {
    expect(4).toBe(5);
}); 
```

现在试着承诺:

```
git add .
git commit -m"will this work?" 
```

脚本运行，测试失败，foo.test.js 得到更新:

```
test("ugly", () => {
    expect(4).toBe(4);
});

test("failing", () => {
    expect(4).toBe(5);
}); 
```

哈士奇阻止了提交:

```
> husky - pre-commit hook failed (add --no-verify to bypass)
> husky - to debug, use 'npm run precommit' 
```

如果您修复了测试并再次尝试，提交应该可以工作。

#### 结论

通过格式化 JavaScript 文件并在每次提交前运行一些脚本，可以改善你的工作流程。

查看 GitHub 上的完整代码。