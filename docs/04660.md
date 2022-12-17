# 使用 React 属性类型轻松验证属性

> 原文：<https://dev.to/a_reiterer/validating-props-easily-with-react-proptypes-plg>

React PropTypes 是一种通过验证通过 props 传递的值的数据类型来帮助您捕获 bug 的好方法。它们还提供了将 props 标记为强制或设置默认值的可能性。它们不费吹灰之力就能带来巨大的好处。

[Fré Sonneveld](https://unsplash.com/photos/2qLT_Rq-2tk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的标题照片

## 简介

如果你想把任何值传递给一个组件，你不会绕过 *props* 。它们是组件的接口，用于获取组件树中的数据。当您的应用程序变得更大更复杂时，您或您的同事可能想要重用一些现有的组件。可能你很久以前就写好了组件，所以你得弄清楚，怎么用，需要哪些道具。

为了找到答案，你可以看看这个组件的源代码，看看所有出现的 *this.props* 。您可以想象这可能需要一些时间，这取决于组件。但是现在你知道哪些道具是必须的了，你可以开始了——但是等等，你知道要传下来的*是什么*吗？仅仅是一个*字符串*还是需要一个*对象*？你看，没那么容易。但是有一个解决方案: **React PropTypes** 。

## React prop types 到底是什么东西？

PropTypes 不仅可以帮助您捕获 bug，还可以作为一个方便的文档，说明如何使用组件来传递属性。让我们来看一个小例子:

```
import React from 'react';
import PropTypes from 'prop-types';

const Person = (props) => <div> 
  <h1>{props.firstName} {props.lastName}</h1>
  {props.country ? <p>Country: {props.country}</p> : null}
</div>;

Person.propTypes = {
  firstName:PropTypes.string,
  lastName:PropTypes.string,
  country:PropTypes.string
};

export default Person; 
```

如您所见，我创建了一个小组件来显示一个人的姓名和国家。我们将名字和姓氏显示为标题，并添加一个包含国家的段落，但前提是设置了国家。一开始，我只是创建了*属性类型*来定义类型，而不关心强制属性。

那么它是做什么的呢？*道具类型*定义一个*道具*的类型。所以每次，一个值通过一个属性，它的类型被验证。如果您通过*属性*传递一个与*属性类型*中指定的数据类型不同的值，您的浏览器控制台中将会显示一条错误消息:

[![Warning: Failed prop type: Invalid prop `country` of type `number` supplied to `Person`, expected `string`.](img/3c53e545708200dd95cef87c3089c9a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--veZAwTT0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.andreasreiterer.at/wp-content/uploads/2017/09/propTypes_wrong_type_error.jpg) 

<figcaption>当你传递一个带有错误数据类型的值时得到的错误消息</figcaption>

如果你这样设置你的*属性类型*，你已经得到了第一个好处:

*   您很容易发现由于传递错误数据类型(例如:对象而不是字符串)的数据而导致的错误
*   使用你的组件的人可以在一个地方看到所有可用的*属性*，包括他们想要的数据类型。
*   一些代码编辑器支持 props 的代码补全，因此当您在工具提示中输入组件时，可以看到可用的 props。

现在我们知道了什么是 PropTypes，以及它们的用途，让我们看看它们还能做什么。

## 数据类型验证

如上例所示， *React 属性类型*用于数据类型验证。这就是它们的用途。我认为大多数时候你可能会检查基本的数据类型。

### 基本数据类型、数组和对象

以下是基本数据类型和的示例:

```
Person.propTypes = {
  email: PropTypes.string,
  age: PropTypes.number,
  worksRemote: PropTypes.bool,
  updateCallback: PropTypes.func
} 
```

可以使用的其他类型有:

```
PropTypes.array,
PropTypes.arrayOf(PropTypes.string),
PropTypes.object,
PropTypes.objectOf(PropTypes.number) 
```

### 复杂数据类型

甚至可以根据特定的形状来验证普通的 JavaScript 对象:

```
Person.propTypes = { 
  car: PropTypes.shape({
    registrationNumber: PropTypes.string,
    year: PropTypes.number
  })
} 
```

你刚才看到的是一个如何验证一个对象的例子，这个对象已经通过了*“car”道具。*如果您传递一个带有字符串字段“year”的对象，您会得到一条错误消息。太好了！

### 指定有效属性值的范围

有时，您可能希望传递一个合适值，也就是给定的一组值中的一个。这样做，可能看起来像这样:

```
Person.propTypes = {
  gender: PropTypes.oneOf([
    'female', 'male'
  ])
} 
```

如果你现在通过*【性别】属性传递*【未知】*，*你的浏览器会显示一个错误，因为它不符合指定的*属性类型:*

[![Warning: Failed prop type: Invalid prop `gender` of value `unknown` supplied to `Person`, expected one of ["female","male"].](img/8f0d543d5372528dc6cdc0a4e05f8bba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3BbvwVbh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.andreasreiterer.at/wp-content/uploads/2017/09/propTypes_oneOf_error.jpg) 

<figcaption>传递的属性不符合指定的值集</figcaption>

除了指定确切的值，您还可以为*属性*指定一组类型:

```
PropTypes.oneOfType([
  PropTypes.string,
  PropTypes.number
]) 
```

## 将道具标记为强制

这是另一个重要的话题。如果你想要求任何使用你的组件的人总是通过某个道具，你可以把它标记为强制的。看看你如何做到这一点，这很简单！😊

你还记得这篇文章开头的例子吗？我将再次显示它，但现在我们将名字和姓氏标记为**必需的**:

```
[...]

const Person = (props) => <div> 
  <h1>{props.firstName} {props.lastName}</h1>
  {props.country ? <p>Country: {props.country}</p> : null}
</div>;

Person.propTypes = { 
  firstName:PropTypes.string.isRequired, 
  lastName:PropTypes.string.isRequired, 
  country:PropTypes.string 
}; 

[...] 
```

那很容易，不是吗？您可以将“isRequired”链接到任何给定的 PropType 数据类型，如果没有提供*属性*，这将导致一个错误消息。

## 设置默认属性值

如果您希望某些属性在未提供任何内容的情况下具有默认值，您可以通过定义 *defaultProps* 来实现:

```
Person.defaultProps = {
  country: 'Austria' 
} 
```

以上确保了每当有人使用 *"Person"* 组件并且没有提供国家时，该人自动变成奥地利人！😁

## 包装完毕

*React 属性类型*是验证组件输入的好方法。而且，如果有人要使用您的组件，它会提供所有可用属性和预期数据类型的快速概述。

所以在你的下一个组件中，尝试使用 *PropTypes* 来避免一些讨厌的错误，并大大提高组件的可重用性。

你喜欢这篇文章并想阅读更多吗？注册我的[简讯](https://www.andreasreiterer.at/dev-newsletter/)。
你会把我以后所有的帖子直接发到你的收件箱里，你可以随时退订。

* * *

用 React PropTypes 轻松验证道具的帖子[最早出现在](http://www.andreasreiterer.at/web-development/react-proptypes/) [Andreas Reiterer](http://www.andreasreiterer.at) 上。