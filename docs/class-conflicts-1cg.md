# 阶级冲突

> 原文：<https://dev.to/letsbsocial1/class-conflicts-1cg>

[![React and CSS Modules](img/6adadb73fbbe716cf95517d08ae747b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Kmqb4L9B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/78ilwecrmgzr8ler4ebq.png)

今天我学习了如何使用`css modules`来控制**的 **css 文件**对** `components`的样式做出反应。例如，`App.css`会包含样式 ***只包含`App.js``Person.css`的*** ，也就是说， ***只包含*** 包含`Person.js`的样式等等。

我正在为这个项目使用**【CRA】**(为了一个 ***变更*** )，并且不得不 ***将*** 弹出，以便对我的**【CRA】**`webpack.config.dev.js`和`webpack.config.prod.js`文件进行必要的变更。

唯一需要做的另一件事是在`module object`中的`css module`中的`webpack.config.dev.js`和`webpack.config.prod.js`中添加以下内容:

```
{
    test: /\.css$/,
    use: [
        require.resolve('style-loader'),
        {
            loader: require.resolve('css-loader'),
            options: {
                importLoaders: 1,
                modules: true,
                localIdentName: '[name]__[local]__[hash:base64:5]',
            },
        },
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

具体我加了`modules: true`，和
`localIdentName: '[name]__[local]__[hash:base64:5]'`，

`css-loader`解析转换我们的 css，提取，存储，支持`css-modules`。

添加`modules:true`和
`localIndentName: '[name]__[local]__[hash:base64:5]'`对你的风格至关重要，你的 css 类为每个组件获得唯一的名称，这样它们就不会在应用程序中全局覆盖彼此。

`[name]`:指你的 css 类名。

`[local]`:允许 ***css-loaders*** 将它(T1)分配给一个本地组件。换句话说，**范围*范围*范围**它。

`[hash:base64:5]`:给你的 ***类名*** 一个唯一的散列，以确保你不会在你的应用程序中全局覆盖你的样式。

这就是启用 css 模块所要做的全部工作。但是要记住，一旦弹射出去，就再也回不去了！

然后我进入我的**App**`component``App.js`**导入**我的 ***类*** 从`App.css`带**变量名** `classes`。

```
// App.js
import classes from './App.css'; 
```

Enter fullscreen mode Exit fullscreen mode

之后，我需要更新我的`className` **属性**，以便我的 ***类*** 能够再次工作。由 ***界定*** 我的`css classes`在`App.css`到`App.js`，他们不再在`JSX`担任弦乐。

这是我的代码最初的样子:

```
// App.js in return statement for the render() method

return (
    <div className={classes.App}>
    <h1 className={classes.title}>Hi, I'm a React App</h1>
    <p className={classes.join(' ')}>This is really working.</p>
    <button style={buttonStyle}
        onClick={this.togglePersonsHandler}>Toggle Persons</button>
        {persons}
    </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

看起来很简单，对吧？但是你猜怎么着。没用。我挠着头想知道，“为什么不呢？”，几分钟。然后我在 **CRA** Github 回购上检查了任何可能存在的 **CRA** `css modules` **相关问题**，因为 **CRA** 仍然不支持`css modules`开箱即用。已经有很多关于增加对它的支持的传言，据称随着 **CRA 2** 的发布 ***将会完全支持*** 。然而，不知道什么时候会发生。

接下来我擒下我的 ***其他的*** 组件，`Person.js`，看看那里会发生什么。我在文件的顶部做了如下操作:

```
// Person.js
import personClasses from './Person.css'; 
```

Enter fullscreen mode Exit fullscreen mode

然后在`Person` **的体内组成** :

```
const Person = (props) => {
    return (
        <div className={personClasses.Person}>
        <p onClick={props.click}>I am {props.name} and I am    {props.age} years old!</p>
        <input type='text' onChange={props.changed} value={props.name}/>
        </div>
    );
}
export default Person; 
```

Enter fullscreen mode Exit fullscreen mode

我没有遇到任何问题。？？

然后我去搜索一篇可能讨论过这种特殊解决方法的文章。我立刻找到了它，并且读了一遍。它似乎对每个人都有效，所以没有问题。那么是什么问题阻止了我的类被添加到我的 JSX 中呢？我突然恍然大悟。当然啦！我经历了阶级(命名)冲突。我将 ***命名为*** ，将**导入变量**为我的`App.css`、 ***类*** 。我曾将 ***命名为另一个*** **变量**与 css 模块 ***类*** 无关。由此产生了阶级冲突。我给了两个不同的变量相同的名字。

对于 ***从`App.css`导入*** my `classes`，我最终从 ***类*** 切换到***app classes***:

```
import appClasses from './App.css'; 
```

Enter fullscreen mode Exit fullscreen mode

对于**数组变量**，我保留了 ***名称*** **类**。

```
// App.js

const classes = []; 
```

Enter fullscreen mode Exit fullscreen mode

但是后来我不得不将一些 ***条件类*** 推入`classes array`，并且我不得不将 ***追加*****导入变量**到那些 ***类*** 。

```
const classes = [];
if(this.state.persons.length <= 2) {
    classes.push(appClasses.rebeccapurple);
}
if(this.state.persons.length <= 1) {
    classes.push(appClasses.bold);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在我的 ***主*** `App.js` `div` :

```
return (
    <div className={appClasses.App}>
    <h1 className={appClasses.title}>Hi, I'm a React App</h1>
    <p className={classes.join(' ')}>This is really working.</p>
    <button style={buttonStyle}
        onClick={this.togglePersonsHandler}>Toggle Persons</button>
        {persons}
    </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

现在一切都正常了。你明白为什么事情会变得很混乱了吧！所以当你给你的**反应** ***应用*** 添加越来越多的层时，一定要记住 ***你给什么*** 起的名字。

**相关资源:**

[我今晚做了第一次演讲(关于 React 工作流)](http://interglobalmedianetwork.com/post/i-gave-my-first-presentation-tonight/)

[如何在 Create React App 中使用 CSS 模块](https://medium.com/nulogy/how-to-use-css-modules-with-create-react-app-9e44bec2b5c2)

[添加对具有显式文件名的 CSS 模块的支持——【名称】. module.css #2285](https://github.com/facebookincubator/create-react-app/pull/2285)