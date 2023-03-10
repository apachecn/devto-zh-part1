# 惯用的 Redux:为封装和性能使用重选选择器

> 原文：<https://dev.to/markerikson/idiomatic-redux-using-reselect-selectors-for-encapsulation-and-performance-bm8>

*为什么以及如何将 Reselect 与 React 和 Redux 一起使用的概述*

> **注**:这篇文章最初发表在我在 blog.isquaredsoftware.com[的博客](http://blog.isquaredsoftware.com/2017/12/idiomatic-redux-using-reselect-selectors/)上，是我在[“惯用 Redux”博客系列](http://blog.isquaredsoftware.com/series/idiomatic-redux/)中关于良好 Redux 使用实践的一部分。

## [T1】简介](#intro)

在一个好的 Redux 架构中，鼓励你[保持你的存储状态最小化，并根据需要从状态中获取数据](https://redux.js.org/docs/recipes/ComputingDerivedData.html)。作为该过程的一部分，我们建议您在应用程序中使用“选择器函数”,并使用 Reselect 库来帮助创建这些选择器。下面更深入地看看为什么这是一个好主意，以及如何正确地使用重选。

## 选择器基础知识

“选择器函数”就是任何接受 Redux 存储状态(或状态的一部分)作为参数，并返回基于该状态的数据的函数。选择器不一定要用特殊的库来编写，不管你是把它们写成箭头函数还是`function`关键字都没关系。比如这些都是选择器:

```
const selectEntities = state => state.entities;

function selectItemIds(state) {
    return state.items.map(item => item.id);
}

const selectSomeSpecificField = state => state.some.deeply.nested.field;

function selectItemsWhoseNamesStartWith(items, namePrefix) {
     const filteredItems = items.filter(item => item.name.startsWith(namePrefix));
     return filteredItems;
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以随意调用你的选择器函数，但是通常以`select`或`get`作为前缀，或者以`Selector`作为名字的结尾，比如`selectFoo`、`getFoo`或`fooSelector`(参见[这篇 Twitter 上关于命名选择器](https://twitter.com/_jayphelps/status/739905438116806656)的投票讨论)。

使用选择器函数的第一个原因是为了封装和可重用性。假设你的一个`mapState`函数是这样的:

```
const mapState = (state) => {
    const data = state.some.deeply.nested.field;

    return {data};
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个完全合法的声明。但是，假设您有几个需要访问该字段的组件。如果你需要改变这个州的位置，会发生什么呢？你现在必须改变引用该值的每个 `mapState`函数的*。因此，就像[我们建议使用动作创建者来封装创建动作](http://blog.isquaredsoftware.com/2016/10/idiomatic-redux-why-use-action-creators/)的细节一样，我们建议使用选择器来封装给定状态位于何处的知识。**理想情况下，只有你的 reducer 函数和选择器应该知道确切的状态结构，所以如果你改变了一些状态所在的位置，你只需要更新这两个逻辑**。*

对选择器的一个常见描述是，它们就像“对您的状态的查询”。您不关心查询是如何得到您需要的数据的，只关心您请求数据并返回结果。

## 重新选择用途和记忆

使用选择器的下一个原因是为了提高性能。性能优化通常包括更快地完成工作，或者找到减少工作的方法。对于 React-Redux 应用程序，选择器可以以几种不同的方式帮助我们做更少的工作。

假设我们有一个组件，它需要一个非常昂贵的数据过滤/排序/转换步骤。首先，它的`mapState`函数是这样的:

```
const mapState = (state) => {
    const {someData} = state;

    const filteredData = expensiveFiltering(someData);
    const sortedData = expensiveSorting(filteredData);
    const transformedData = expensiveTransformation(sortedData);

    return {data : transformedData};
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，这个昂贵的逻辑将为每个导致状态更新的分派动作重新运行*，即使被改变的存储状态是在这个组件不关心的状态树的一部分中。*

我们真正想要的是，如果`state.someData`真的改变了，就重新运行这些昂贵的步骤。这就是“记忆化”概念的由来。

记忆是缓存的一种形式。它包括跟踪一个函数的输入，并存储输入和结果供以后参考。如果使用与以前相同的输入来调用一个函数，该函数可以跳过实际的工作，并返回上次接收这些输入值时生成的相同结果。

**[重选库](https://github.com/reactjs/reselect)提供了创建记忆选择器函数的方法。** Reselect 的`createSelector`函数接受一个或多个“输入选择器”函数，以及一个“输出选择器”函数，并返回一个新的选择器函数供您使用。

`createSelector`可以接受多个输入选择器，它们可以作为单独的参数或数组提供。来自所有输入选择器的结果作为单独的参数提供给输出选择器:

```
const selectA = state => state.a;
const selectB = state => state.b;
const selectC = state => state.c;

const selectABC = createSelector(
    [selectA, selectB, selectC],
    (a, b, c) => {
        // do something with a, b, and c, and return a result
        return a + b + c;
    }
);

// Call the selector function and get a result
const abc = selectABC(state);

// could also be written as separate arguments, and works exactly the same
const selectABC2 = createSelector(
    selectA, selectB, selectC,
    (a, b, c) => {
        // do something with a, b, and c, and return a result
        return a + b + c;
    }
); 
```

Enter fullscreen mode Exit fullscreen mode

当您调用选择器时，Reselect 将使用您给出的所有参数运行您的输入选择器，并查看返回值。如果有任何结果与之前不同，它将重新运行输出选择器，并将这些结果作为参数传入。如果所有的结果都与上次相同，它将跳过重新运行输出选择器，只返回之前缓存的最终结果。

在典型的重选用法中，您将顶级“输入选择器”编写为普通函数，并使用`createSelector`创建查找嵌套值的记忆化选择器:

```
const state = {
    a : {
        first : 5
    },
    b : 10
};

const selectA = state => state.a;
const selectB = state => state.b;

const selectA1 = createSelector(
    [selectA],
    a => a.first
);

const selectResult = createSelector(
    [selectA1, selectB],
    (a1, b) => {
        console.log("Output selector running");
        return a1 + b;
    }
);

const result = selectResult(state);
// Log: "Output selector running"
console.log(result);
// 15

const secondResult = selectResult(state);
// No log output
console.log(secondResult);
// 15 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们第二次调用`selectResult`时，“输出选择器”没有执行。因为`selectA1`和`selectB`的结果与第一次调用相同，`selectResult`能够返回第一次调用的存储结果。

值得注意的是，默认情况下，重新选择只记忆最近的参数集。这意味着，如果您使用不同的输入重复调用选择器，它仍然会返回一个结果，但是它将不得不重新运行输出选择器来产生结果:

```
const a = someSelector(state, 1); // first call, not memoized
const b = someSelector(state, 1); // same inputs, memoized
const c = someSelector(state, 2); // different inputs, not memoized
const d = someSelector(state, 1); // different inputs from last time, not memoized 
```

Enter fullscreen mode Exit fullscreen mode

此外，您可以将多个参数传递给选择器。重新选择将用这些确切的输入调用所有的输入选择器:

```
const selectItems = state => state.items;  
const selectItemId = (state, itemId) => itemId;  

const selectItemById = createSelector(  
    [selectItems, selectItemId],  
    (items, itemId) => items[itemId]  
);  

const item = selectItemById(state, 42);

/*
Internally, Reselect does something like this:

const firstArg = selectItems(state, 42);  
const secondArg = selectItemId(state, 42);  

const result = outputSelector(firstArg, secondArg);  
return result;  
*/ 
```

Enter fullscreen mode Exit fullscreen mode

因此，您提供的所有“输入选择器”都应该接受相同类型的参数，这一点很重要。否则，选择器会损坏。

```
const selectItems = state => state.items;  

// expects a number as the second argument
const selectItemId = (state, itemId) => itemId;  

// expects an object as the second argument
const selectOtherField (state, someObject) => someObject.someField;  

const selectItemById = createSelector(  
    [selectItems, selectItemId, selectOtherField],  
    (items, itemId, someField) => items[itemId]  
); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`selectItemId`期望它的第二个参数是某个简单的值，而`selectOtherField`期望第二个参数是一个对象。如果你调用`selectItemById(state, 42)`，`selectOtherField`会中断，因为它试图访问`42.someField`。

**你可以(也许*应该*)在你的应用程序中任何你访问状态树**的地方使用选择器函数*。这包括`mapState`函数、thunks、sagas、observables、中间件，甚至 reducers。*

选择器函数通常与归约器放在一起，因为它们都知道状态形状。然而，将选择器函数放在哪里以及如何组织它们取决于您。

## 通过重选优化性能

让我们回到前面的“昂贵的`mapState`”例子。我们真的想只在`state.someData`改变时执行那个昂贵的逻辑。将逻辑放入记忆选择器中就可以做到这一点。

```
const selectSomeData = state => state.someData;

const selectFilteredSortedTransformedData = createSelector(
    selectSomeData,
    (someData) => {
         const filteredData = expensiveFiltering(someData);
         const sortedData = expensiveSorting(filteredData);
         const transformedData = expensiveTransformation(sortedData);

         return transformedData;
    }
)

const mapState = (state) => {
    const transformedData = selectFilteredSortedTransformedData (state);

    return {data : transformedData};
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个很大的性能提升，原因有二。

首先，现在昂贵的转换只发生在`state.someData`不同的情况下。这意味着如果我们调度一个更新`state.somethingElse`的动作，我们不会在这个`mapState`函数中做任何实际的工作。

第二，React-Redux `connect`函数使用“浅相等”比较，根据从`mapState`返回的对象的内容，确定您的真实组件是否应该重新呈现。如果返回的任何字段与上次的`===`不同，那么`connect`将重新呈现您的组件。这意味着除非需要，否则应该避免在`mapState`函数中创建新的引用。像`concat()`、`map()`和`filter()`这样的数组函数总是返回新的数组引用，对象扩展操作符也是如此。通过使用记忆化的选择器，如果数据没有改变，我们可以返回相同的引用，从而跳过重新呈现真实组件。

## 使用 React-Redux 进行高级优化

当您将记忆化选择器与可以多次呈现的组件一起使用时，会出现一个特定的性能问题。

假设我们有这个组件定义:

```
const mapState = (state, ownProps) => {
    const item = selectItemForThisComponent(state, ownProps.itemId);

    return {item};
}

const SomeComponent = (props) => <div>Name: {props.item.name}</div>; 
export default connect(mapState)(SomeComponent);

// later
<SomeComponent itemId={1} /> <SomeComponent itemId={2} /> 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`SomeComponent`将`ownProps.itemId`作为参数传递给选择器。当我们渲染多个`<SomeComponent>`实例时，每个实例都共享同一个`selectItemForThisComponent`函数实例。这意味着当一个动作被分派时，`<SomeComponent>`的每个单独的实例将单独调用这个函数，比如:

```
// first instance
selectItemForThisComponent(state, 1);
// second instance
selectItemForThisComponent(state, 2); 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，只对最近的输入进行重选(即缓存大小为 1)。这意味着`selectItemForThisComponent`将*永远不会*正确记忆，因为它永远不会被同样的输入连续调用。

这段代码仍然可以运行和工作，但是没有完全优化。为了获得绝对最佳的性能，我们需要为`<SomeComponent>`的每个实例创建一个单独的`selectItemForThisComponent`副本。

**React-Redux`connect`函数支持针对`mapState`和`mapDispatch`函数的特殊“工厂函数”语法，该语法可用于为每个组件实例创建选择器函数的唯一实例。**

如果对`mapState`或`mapDispatch`函数的第一次调用返回一个函数而不是一个对象，`connect`将使用那个返回的函数作为*真正的*T3】或`mapDispatch`函数。这使您能够在闭包内创建特定于组件实例的选择器:

```
const makeUniqueSelectorInstance = () => createSelector(
    [selectItems, selectItemId],
    (items, itemId) => items[itemId]
);    

const makeMapState = (state) => {
    const selectItemForThisComponent = makeUniqueSelectorInstance();

    return function realMapState(state, ownProps) {
        const item = selectItemForThisComponent(state, ownProps.itemId);

        return {item};
    }
};

export default connect(makeMapState)(SomeComponent); 
```

Enter fullscreen mode Exit fullscreen mode

组件 1 和组件 2 都将获得它们自己唯一的`selectItemForThisComponent`副本，每个副本都将被一致的可重复输入调用，从而允许正确的记忆。

## 最后的想法

像[其他常见的 Redux 使用模式](http://blog.isquaredsoftware.com/2017/05/idiomatic-redux-tao-of-redux-part-2/)、**一样，你不需要在 Redux 应用**中使用选择器功能。如果您想直接在您的`mapState`函数或 thunks 中编写深度嵌套的状态查找，您可以这样做。类似地，您不需要*使用*来创建选择器——如果您愿意，您可以只编写普通的函数。

话虽如此，**我们鼓励您使用选择器函数，并为记忆化的选择器**使用重新选择库。创建选择器还有许多其他选项，包括使用函数式编程实用程序库，如 lodash/fp 和 Ramda，以及其他用于重选的替代方法。还有建立在重新选择基础上的[实用程序库来处理特定的用例](https://github.com/markerikson/redux-ecosystem-links/blob/master/utilities.md#selectors)。

## 进一步信息

*   **选择器基础和用法**

    *   [react casts # 8:Redux 中的选择器](https://www.youtube.com/watch?v=frT3to2ACCw)
    *   [通过重新选择优化 React Redux 应用程序开发](https://codebrahma.com/reselect-tutorial-optimizing-react-redux-application-development-with-reselect/)
    *   [在 React-Redux 应用程序中使用重选](https://dashbouquet.com/blog/frontend-development/usage-of-reselect-in-a-react-redux-application)
    *   [反应、重选、还原](https://medium.com/@parkerdan/react-reselect-and-redux-b34017f8194c)
    *   [反应/还原链接:还原减速器和选择器](https://github.com/markerikson/react-redux-links/blob/master/redux-reducers-selectors.md#selectors-and-normalization)
    *   [Redux 生态系统链接:实用程序-选择器](https://github.com/markerikson/redux-ecosystem-links/blob/master/utilities.md#selectors)
*   **组织选择器**

    *   丹·阿布拉莫夫的“惯用还原”视频系列:将选择器与还原器放在一起
    *   Randy Coulman 关于选择器和封装的文章:
        *   [封装冗余状态树](http://randycoulman.com//blog/2016/09/13/encapsulating-the-redux-state-tree/)
        *   [减少减速器/选择器不对称](http://randycoulman.com/blog/2016/09/20/redux-reducer-selector-asymmetry/)
        *   [模块化减速器和选择器](http://randycoulman.com/blog/2016/09/27/modular-reducers-and-selectors/)
        *   [全球化冗余选择器](http://randycoulman.com/blog/2016/11/29/globalizing-redux-selectors/)
    *   [React/Redux 链接:Redux 架构——封装和可重用性](https://github.com/markerikson/react-redux-links/blob/master/redux-architecture.md#encapsulation-and-reusability)
*   **反应/还原性能**

    *   [实用冗余，第 6 部分:关联列表、表单和性能](http://blog.isquaredsoftware.com/2017/01/practical-redux-part-6-connected-lists-forms-and-performance/)
    *   [高性能冗余](http://somebody32.github.io/high-performance-redux/)
    *   [反应/还原链接:还原性能](https://github.com/markerikson/react-redux-links/blob/master/react-performance.md)