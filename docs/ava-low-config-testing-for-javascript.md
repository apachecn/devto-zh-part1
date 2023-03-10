# AVA，JavaScript 的低配置测试

> 原文：<https://dev.to/hugo__df/ava-low-config-testing-for-javascript>

AVA 是一个未来主义的 JavaScript 测试者。

它的一些最佳特性是:

*   它开箱即用，不需要为测试文件指定 blob 或添加 Babel 挂钩
*   它可以并行运行测试，这样可以避免使用全局状态，运行速度更快
*   默认情况下，测试是异步的

我很喜欢 AVA，因为我可以直接把它扔进去，没有太多的麻烦就能得到好处。我从来没有为了让测试运行而与 AVA 对抗过，这意味着更多地关注代码和测试，而不是设置。

# 无斑点配置

在命令行运行 AVA 非常简单(如果您已经在全球范围内安装了它):

```
$ ava 
```

要在命令行中以观察模式运行 AVA:

```
$ ava -w 
```

要在详细模式下运行 AVA:

```
$ ava --verbose 
```

如果你的测试文件与`test.js test-*.js test/**/*.js **/__tests__/**/*.js **/*.test.js`匹配，AVA 将会提取并运行它们。您可以随时覆盖这一点，但我不认为您会想要配置 AVA 不同。

我们可以将这些命令包装在 npm 脚本中，并使用项目版本的 AVA。实际上，AVA 有一个命令可以把这些东西添加到你的`package.json`:

```
$ ava --init 
```

它将在`devDependencies`和测试脚本中添加 AVA。

# 重要的能见度

下面是 Mocha 中的一个示例测试文件:

```
const { expect } = require('chai');
describe('Some function', () => {
  it('does something with input', () => {
    const actualOutput = 42;
    const expectedOutput = 42;
    expect(actualOutput).to.equal(expectedOutput);
  });
}); 
```

`describe`？`it`？不太明显，它们是由测试运行程序定义的函数。我们还在断言中使用了 chai 如果所有这些都是一个包，并且很明显我们在使用它，那不是很好吗？

Mocha 最佳实践还说我们不应该使用箭头函数(`() => {}`)，因为我们可能想要访问 describe 或 it 内部定义的`this`，而不是全局模块`this`。

这是在 AVA 进行的同样的测试:

```
import test from 'ava';
test('Some function', t => {
    const actualOutput = 42;
    const expectedOutput = 42;
    t.equal(actualOutput, expectedOutput);
}); 
```

AVA 不太啰嗦，它做断言库的工作，同时也是测试运行程序。我们在任何地方都依赖于 this，因为测试对象`t`是作为参数传递的。

在 AVA 中没有嵌套，它使用测试文件及其路径来定义测试套件。

# 挂钩当前和未来的 JavaScript

AVA 默认使用完整的 ES2016 语法运行，并通过巴别塔变换运行其代码。这意味着，不需要添加一个巴别塔挂钩(就像你在摩卡中那样),你可以写:

```
import test from 'ava';
import sinon from 'sinon';
test('It actually runs', t => {
  t.pass();
}); 
```

当您运行测试时:

```
$ ava
  1 passed 
```

“import”语句没有语法错误。如果您正在导入需要进行 Babel 转换的组件(例如使用 JSX 的 React 组件)，您只需将以下 Babel 挂钩添加到 package.json 中的 AVA 配置中:

```
{
  "ava": {
    "require": [
      "babel-register"
    ],
    "babel": "inherit"
  }
} 
```

这意味着使用相同的语法编写和测试现代 JavaScript 很容易，使用 AVA 也很容易。

# 异步默认

不再许诺你的断言，不再传递已经完成的回调。如果你使用承诺，AVA 很喜欢:

```
import test from 'ava';
test('Promise resolves to correct value', t => 
  return new Promise(resolve => resolve(1))
    .then(value => {
      t.is(value, 1)
    });
}); 
```

如果你想使用异步，那就更好了:

```
import test from 'ava';
test('Promise resolves to correct value', async t => {
  const promiseResolution = new Promise(resolve => resolve(1));
  t.is(await promiseResolution, 1);
}); 
```

你有好的旧的回调模式，它工作得很好，你显然必须调用和。像其他测试跑步者一样在某处结束:

```
import test from 'ava';
test.cb('Callback gets correct value', t => {
  t.plan(1);
  setTimeout(() => {
    t.pass();
    t.end();
  }, 100);
}); 
```

在异步情况下，您可以使用 t.plan 来指定应该运行的断言数量，如果没有运行确切数量的断言，那么它将会失败。这与其他一些测试运行程序不同，如果运行的断言少于指定的数量，这些测试运行程序就会失败。

# 结论

AVA 是 2017 年及以后 JavaScript 的低配置、低维护测试。对于失败的断言，您会得到非常好的区别，这是一个用于断言和运行测试的单库解决方案。测试运行速度快，并促进最佳实践(纯测试),并准备好测试异步功能。

如果你觉得这篇文章有用，请随意分享。你可以在 Twitter 上找到我 [@hugo__df](https://twitter.com/hugo__df) 。

JavaScript 的低配置测试于 2017 年 3 月 13 日在 https://medium.com/@hugo__df[发布。](https://medium.com/@hugo__df)

最近的 JavaScript/开发人员生产力帖子:

[Visual Studio 代码:我认为我不需要的编辑器](https://hackernoon.com/virtualstudio-code-the-editor-i-didnt-think-i-needed-16970c8356d5)

[有效函数 JavaScript:一级和更高阶函数](https://hackernoon.com/effective-functional-javascript-first-class-and-higher-order-functions-713fde8df50a)