# 按需动态加载 React 组件

> 原文：<https://dev.to/dance2die/loading-react-components-dynamically-on-demand-1a56>

我在寻找 JavaScript 中的一个[工厂模式](https://en.wikipedia.org/wiki/Factory_method_pattern)的实现，以便在显示 React 组件时去掉一个`switch`语句。

然后就碰到了一个动态 [import()](https://hospodarets.com/native-ecmascript-modules-dynamic-import) 。

我将向您展示使用`import()`动态加载 React 组件的 3 种方式。

1.  [动态加载 React 组件](#case-1-loading-react-components-dynamically)–[演示](https://epic-chandrasekhar-a99744.netlify.com/)–[源代码](https://github.com/dance2die/Demo.React.ReactDynamicComponent)
2.  [处理不同的数据类型](#case-2-handling-different-data-types)–[演示](https://pedantic-goldstine-aff0e1.netlify.com/)–[源代码](https://github.com/dance2die/Demo.React.ReactLoadOnDemand)
3.  [按需加载组件](#case-3-loading-components-on-demand)–[演示](https://hardcore-fermi-db1121.netlify.com/)–[源代码](https://github.com/dance2die/Demo.React.ReactLoadOnDemand)

让我们开始吧🏊~

### 案例 1–动态加载 React 组件

#### **问题**

假设您想对事件数组中的每个事件进行不同的显示。