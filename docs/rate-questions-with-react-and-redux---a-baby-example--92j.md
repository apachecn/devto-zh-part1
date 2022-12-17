# 用 React 和 Redux 评价问题——以ðÿ'为例

> 原文：<https://dev.to/createdd/rate-questions-with-react-and-redux---a-baby-example--92j>

# 用 React 和 Redux 评价问题——以ðÿ'为例

[![](img/e214873c66f24ed68ab309e80f5e0e7d.png)](https://unsplash.com/photos/s0XDLfhyN34)
[https://un flash . com/photos/s0 xdlfhyn 34](https://unsplash.com/photos/s0XDLfhyN34)

我将构建一个简单的评级问题的小应用程序。这是为 React 和 Redux 设计的练习项目，因为我仍然不理解它。

[Github 回购可在此处获得…](https://github.com/DDCreationStudios/)

## ðÿ"„目录

*   [撰写本文的动机](#motivation-for-this-article)
*   [模块化底座](#modularizing-the-base)
*   [添加冗余](#adding-redux)
    *   [动作类型](#action-types)
    *   [减速器](#reducers)
    *   [动作和动作创建者](#actions-and-action-creators)
    *   [创建 Redux 商店](#create-the-redux-store)
    *   [将容器连接到商店](#connect-the-container-to-the-store)
*   [在 Redux App 中添加另一个组件](#add-another-component-in-the-redux-app)
*   [弈鸭](#implement-ducks)
*   [Chrome Redux 开发工具](#chrome-redux-devtools)

* * *

> ## "Everything is practice." -Bailey

## 本文动机

又一个了解 Redux 和 React 的小应用。感觉是第 100 个试图抓 Redux 的 app。但是一个月没有 Redux，你基本上又是从零开始。我说:“是的，我听说过”——就是这样。行动，行动创造者，减少者，派遣，blabla。太多的事情让我无法理解:D——

## 模块化底座

构建组件，以便完美地适应 Redux 应用程序。

[代码库在 Github 上…](https://github.com/DDCreationStudios/questionScores/tree/ad0543f1d6607048482ecec409041a3b3329e80d)

*   秒表组件有自己的本地状态，不依赖于其他组件
*   统计信息和计数器组件依赖于其他组件
*   AddQuestionForm 依赖于其他组件，也包含逻辑信息
*   标题和问题组件

模块化可以帮助

*   分离责任，这意味着更容易的测试和调试
*   更好地扩展应用程序，更容易使用 Redux
*   更好地组织团队

[运行库上的年龄调制码…](https://github.com/DDCreationStudios/questionScores/tree/fe8fd2a45b6c3c4129d7abb970541d3f2541147b)

## 添加 Redux

#### 动作类型

决定哪些组件应该参与 Redux 存储。
- >在该应用中，只有问题必须对所有组件可用。

针对这种特定状态，查找应用程序中发生的事件。->在本应用中，它是

*   改变比分
*   添加问题
*   移除问题

#### 减速器

减速器是纯函数，根据动作类型改变状态。

reducer 函数提供了关于如何改变状态的不同开关语句。(确保永远不要改变状态本身！应该是纯函数！#不变性)

例如:

```
export default function Player(state = initialState, action) {
  switch (action.type) {
    case QuestionActionTypes.ADD_PLAYER:
      return [
        ...state,
        {
          name: action.name,
          score: 0,
        },
      ];
    case QuestionActionTypes.REMOVE_QUESTION:
      return [...state.slice(0, action.index), ...state.sclice(action.index + 1)];
    case QuestionActionTypes.UPDATE_QUESTION_SCORE:
      return state.map((question, index) => {
        if (index === action.index) {
          return {
            ...question,
            score: question.score + question.score,
          };
        }
        return question;
      });
    default:
      return state;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 动作和动作创建者

向 redux 提交操作

*   动作创建者生成动作(动作=将导致状态改变的事件)
*   动作被分派给 Redux 存储
*   缩减器将动作传递给组件，并返回新的状态

例如添加一个问题:

```
export const addQuestion = name => ({
  type: QuestionActionTypes.ADD_QUESTION,
  name,
}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建 Redux 商店

在 index.js 中创建一个存储，将其传递给主 reducer，并将其包装在记分板组件周围，以便为整个应用程序提供存储。

#### 将容器连接到商店

*   使用`mapStateToProps`将状态分配给一个道具值- >将问题的状态分配为道具
*   对于使用以下方式创建的自动调度操作:

```
const {dispatch, questions} = this.props;
const addQuestion = bindActionCreators(QuestionActionCreators.addQuestion, dispatch);
const removeQuestion = bindActionCreators(QuestionActionCreators.removeQuestion, dispatch);
const updateQuestionScore = bindActionCreators(QuestionActionCreators.updateQuestionScore, dispatch); 
```

Enter fullscreen mode Exit fullscreen mode

*   相应地更新组件(计数器、问题和记分板组件)上的事件处理程序
*   标题和秒表组件不需要更改，因为它们不参与 Redux 循环

## 在 Redux App 中添加另一个组件

现在我们想显示每个问题的细节

*   添加新的操作类型(选择一个问题)
*   用新的开关盒和附加状态扩展减速器
*   为选择问题添加新的操作创建者
*   在记分板组件中创建新的 bindActionCreator
*   用所选问题索引更新 mapStateToProps
*   创建一个 QuestionDetail 组件来显示详细信息
*   更新问题组件上的事件处理程序

[![](img/7f6d0b9e41a32c8fda75b9bf042e51e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X5N7PSnY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/../assets/REDSCORE/Screenshot2.png)

[参见 Github 上实现细节组件的提交…](https://github.com/DDCreationStudios/questionScores/commit/e23757d34620b8a5f6c5aac9d0fe63dba621c5ca)

## 弈鸭

对于较小的应用程序来说， [ducks 概念](https://github.com/erikras/ducks-modular-redux)可以帮助更快地开发 Redux 应用程序。基本上，我们可以将它们保存在一个文件中，以便有一个更好的概览，而不是将所有东西都模块化(动作、减少器、动作创建器)。

这个文件看起来像:

```
// Actions
const ADD_QUESTION = 'question/ADD_QUESTION';
const REMOVE_QUESTION = 'question/REMOVE_QUESTION';
const UPDATE_QUESTION_SCORE = 'question/UPDATE_QUESTION_SCORE';
const SELECT_QUESTION = 'question/SELECT_QUESTION';

// Reducers
const initialState = {
  questions: [
    {
      name: 'Do you like AI?',
      score: 31,
      created: '00:00',
      updated: '00:00',
    },
    {
      name: 'Do you like Engineering?',
      score: 20,
      created: '00:00',
      updated: '00:00',
    },
    {
      name: 'How many Redux Apps?',
      score: 50,
      created: '00:00',
      updated: '00:00',
    },
  ],
  selectedQuestionIndex: -1,
};

export default function Question(state = initialState, action) {
  const date = `${new Date().getHours()}:00`;
  switch (action.type) {
    case ADD_QUESTION:
      const addQuestionList = [
        ...state.questions,
        {
          name: action.name,
          score: 0,
          created: date,
        },
      ];
      return {
        ...state,
        questions: addQuestionList,
      };
    case REMOVE_QUESTION:
      const removeQuestionList = [
        ...state.questions.slice(0, action.index),
        ...state.questions.slice(action.index + 1),
      ];
      return {
        ...state,
        questions: removeQuestionList,
      };
    case UPDATE_QUESTION_SCORE:
      const updateQuestionList = state.questions.map((question, index) => {
        if (index === action.index) {
          return {
            ...question,
            score: question.score + action.score,
            updated: date,
          };
        }
        return question;
      });
      return {
        ...state,
        questions: updateQuestionList,
      };
    case SELECT_QUESTION:
      return {
        ...state,
        selectedQuestionIndex: action.index,
      };
    default:
      return state;
  }
}

// ActionCreators
export const addQuestion = name => ({
  type: ADD_QUESTION,
  name,
});
export const removeQuestion = index => ({
  type: REMOVE_QUESTION,
  index,
});
export const updateQuestionScore = (index, score) => ({
  type: UPDATE_QUESTION_SCORE,
  index,
  score,
});
export const selectQuestion = index => ({
  type: SELECT_QUESTION,
  index,
}); 
```

Enter fullscreen mode Exit fullscreen mode

[参见 Github 上 ducks 实现的提交…](https://github.com/DDCreationStudios/questionScores/commit/4e7b107f6d7a2232e6a7543271d137886d6d2d70)

## Chrome Redux DevTools

*   下载 [Redux DevTools 扩展](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd)
*   将必要的代码行添加到您的存储中

```
const store = createStore(
    QuestionReducer,
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__(),
); 
```

Enter fullscreen mode Exit fullscreen mode

DevTools 有助于开发和调试 Redux 应用程序。查看这篇[文章](https://medium.com/@zalmoxis/improve-your-development-workflow-with-redux-devtools-extension-f0379227ff83)了解更多信息。

[![](img/7111185a880ae6ea962a10bdc0aad89b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RdclCf8A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/../assets/REDSCORE/Screenshot1.png)

* * *

[运钞机上的年龄结果……](https://github.com/DDCreationStudios/questionScores)

* * *

如果你从这篇文章中有所收获，请告诉我你的意见或想法。请确保关注更多内容:)