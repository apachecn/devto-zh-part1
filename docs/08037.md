# 测试 React 本机应用程序第一部分:Jest

> 原文：<https://dev.to/preslavrachev/testing-react-native-applications-part-i-jest>

*注:这篇文章最初发表在[我的博客](http://www.preslav.me/2017/05/06/testing-react-native-applications-part-i-jest/)上。*

当我不久前开始使用 React Native 时，我对大量关于如何测试 React 应用程序的无关信息感到沮丧。众所周知，React 发展很快，经常引入突破性的变化，所以这对于大多数开发人员来说并不奇怪。尽管如此，大量不赞成使用的内容还是给人留下了一种不好的感觉，尤其是在那些刚刚开始使用 React 或 RN 的开发人员中。

花了几天时间，我自己打破了围墙，我决定记录测试事务的当前状态，希望我的帖子在发布时不会过时。

注意:虽然我最初想写一大篇详细描述注册护士测试的文章，但我决定把它分成几个易于管理的部分。这篇文章将谈论 Jest，它是什么，以及它为什么有用。

## 是

当您通过提供的 CLI 工具之一创建一个新的 React 或 React 本机应用程序时，它会使用一个名为 [Jest](https://facebook.github.io/jest/) 的框架自动创建一个示例单元测试。我不得不坦率地说。您不一定要在 RN 测试中使用 Jest，但是它确实带来了一些有趣的特性。

与 Mocha 类似，Jest 是一个开放的测试运行器和断言框架，它使用熟悉的 BDD 语法来描述和断言测试:

```
describe('calculator', function() {
  describe('add()', function() {
    it('should add 2 numbers togoether', function() {
      expect(calculator.add(2, 2)).toEqual(4);
    });
  });
}); 
```

### 设置

设置 Jest 并编写您的第一个测试相当容易，因为它采用了“约定胜于配置”的方法。尽管微调 Jest 配置是完全可能的，但所有配置都有预定义的默认值。事实上，您需要做的就是将您的测试放在`__tests__`下，而`__mocks__`下的模拟模块和 Jest 会自动找到它们。

### 嘲讽

除此之外，Jest 本身也提供了一些有趣的特性。一个是它咄咄逼人的嘲讽方式。最多几个版本之前，Jest 的`automock`选项默认设置为`true`。这意味着除非被明确配置为不这样做，否则 Jest 将创建与您的代码相关的每个库和依赖项的模拟。开发团队[意识到](https://facebook.github.io/jest/blog/2016/09/01/jest-15.html)虽然这是一个过于激进的想法，虽然选项仍然存在，但它被默认设置为`false`:

> 我们在脸书引入了自动锁定，当单元测试在现有测试很少的大型现有代码库中被采用时，它对我们非常有效，但是随着时间的推移，感觉人们花在与模拟/未模拟模块斗争上的时间比他们正常编写测试所花的时间还要多。我们还注意到，库作者经常需要大量的基本模块，而这些模块总是需要手动卸载。

尽管如此，在关闭自动锁定的情况下，您可以很容易地模仿整个模块，或者只是一些调用代价很高或不必要的函数(HTTP 请求、磁盘读/写等)。

#### 笑话嘲讽小抄

```
// Mock-related functions available via the global Jest object
jest.resetAllMocks()
jest.clearAllTimers()
jest.disableAutomock()
jest.enableAutomock()
jest.fn(?implementation)
jest.isMockFunction(fn)
jest.genMockFromModule(moduleName)
jest.mock(moduleName, ?factory, ?options)
jest.resetModules()
jest.setMock(moduleName, moduleExports)
jest.unmock(moduleName)
jest.spyOn()

// Mock functions available to every mock
mockFn.mock.calls
mockFn.mock.instances
mockFn.mockClear()
mockFn.mockReset()
mockFn.mockRestore()
mockFn.mockImplementation(fn)
mockFn.mockImplementationOnce(fn)
mockFn.mockReturnThis()
mockFn.mockReturnValue(value)
mockFn.mockReturnValueOnce(value) 
```

### 抓拍

除了嘲讽，抓拍是 Jest 提供的另一大功能。如果您正在使用 React 或 React Native CLI，您一定已经在您的测试中看到过这一行代码:

```
expect(tree).toMatchSnapshot(); 
```

当断言快照时，Jest 将在`__snapshots__`目录中寻找一个现有的快照，如果没有找到，将在那里保存当前的快照。下一次运行测试时，当前测试将与现有测试进行比较，如果有变化，测试将会中断。

当测试多个组件的集成时，快照非常有用，结果通常是一个非常嵌套的结构。开发人员编写这种测试的方式要么是使用许多断言，手工编写预期输出的一部分，要么是使用一些非常肤浅的断言(如`toContain`)。因此，快照有助于节省大量手动编写这些冗长乏味的断言代码的时间。

这个来自官方 Jest 博客的例子使用 React 组件的渲染输出来说明快照:

```
import renderer from 'react-test-renderer';

test('Link renders correctly', () => {
  const tree = renderer.create(
    <Link page="http://www.facebook.com">Facebook</Link>
  ).toJSON();
  expect(tree).toMatchSnapshot();
}); 
```

以及保存的相应快照:

```
exports['Link renders correctly 1'] = ' <a
  className="normal"
  href="http://www.facebook.com"
  onMouseEnter={[Function bound _onMouseEnter]}
  onMouseLeave={[Function bound _onMouseLeave]}>
  Facebook
</a> '; 
```

虽然您肯定会发现快照对于验证应用程序和组件的渲染输出非常有用，但这并不是唯一的用例。我发现它在验证全局应用程序状态时非常有用，例如使用像 Redux 这样的库或其他类似的方法。

## 结论

总之，我发现 Jest 的“零配置，电池包括”的测试方法非常有趣。开始一个新项目，尤其是 React 或 React Native 项目，跟着它走是有意义的，因为它是捆绑在一起的。即使您已经有了一个大型的测试代码库，假设它们遵循熟悉的 BDD 惯例(并且很可能使用 Jasmine 断言)，您也可以很快在 Jest 上建立并运行它。

现在我已经弄清楚了 Jest 是怎么回事，在我的下一篇文章中，我将重点关注 React 本机应用程序的结构测试。

## 进一步阅读

*   [官方 Jest 网站](https://facebook.github.io/jest/)
*   [茉莉 vs 摩卡、柴、西农](http://thejsguy.com/2015/01/12/jasmine-vs-mocha-chai-and-sinon.html)
*   [用 Karma、Jest 或 Mocha 测试 React 应用程序](http://instea.sk/2016/08/testing-react-applications-with-karma-jest-or-mocha/)
*   [是 jest 的简称](https://dmitriiabramov.github.io/jest-cheatsheet/index.html)
*   [测试 React 原生应用——Jest](https://facebook.github.io/jest/docs/tutorial-react-native.html)