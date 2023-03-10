# React 路由器 DOM v4 教程(带示例)

> 原文：<https://dev.to/techiediaries/react-router-dom-v4-tutorial-with-examples-dok>

[![React Router DOM Tutorial (with Examples)](img/a29049616ed177047368a21aa57688bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u0f1Wl-g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.techiediaries.cimg/content/react-router-dom-v4.png)

## 简介

在本教程中，我们将使用一个 react 应用程序示例让您从 **react-router-dom** 开始，向您展示如何使用不同的概念，如 **Link** 和 **NavLink** 以 React 方式创建链接(而不是锚点)，使用 **Switch** 和 **exact** 启用独占路由和**浏览器路由历史**。

也许了解 **React 路由器 v4** 如何工作的最合适的方式是使用新的路由器概念编写一个简单的多页 React 应用程序。我们的示例应用程序将有到主页，关于，联系，登录，注册和配置文件组件/页面的路线。但是首先让我们来看一下 React 路由器 v4 的概念以及它们与 React 路由器 v3 的区别。

## 反应路由器 v4 vs 反应路由器 v3

在 v4 之前有 **React 路由器 v3** ， **React 路由器 v4** 是完全重写的，那么这两个 React 路由器有什么区别呢？以下是最大差异的汇总列表:

*   有了 **React router v4** ，路由不再是集中的，而是成为应用布局和 UI 其余部分的一部分。
*   浏览器特定的路由组件位于 **react-router-dom** 中，而不是 **react-router** 中，因此需要将导入更改为来自 **react-router-dom** 包。
*   引入新组件，如用于特定用例的**浏览器**和**散列路由器**(见下文)。
*   v4 React 路由器中不再使用 **{props.children}** 嵌套组件。
*   React Router v3 路由规则是排他的，这意味着一次只能匹配一条路由。对于 v4，路由规则是包含性的，这意味着可以匹配多条路由，然后进行渲染。

**React-router-dom** 是 React Router v4 专为 web 应用设计的版本，React Router v4 分为三个包:

*   **react-router**:DOM 和原生版本之间的通用核心组件。
*   **react-router-dom** :为浏览器或 web apps 设计的 dom 版本。
*   **react-router-native** :为 react-native 移动应用设计的原生版本。

## 反应路由器 vs 反应路由器 dom vs 反应路由器 native

react-router 托管 react 应用程序路由的核心组件，react-router-dom 提供路由 web 应用程序的浏览器特定组件，react-router-native 提供 react-native 或使用 React Native 创建的移动应用程序的特定组件。所以你应该要么安装 **react-router-dom** 要么安装 **react-router-native** ，因为两者都导出它们相应的环境组件以及 **react-router** 导出的内容。

## 安装

因为我们正在构建一个 web 应用程序，而不是一个本地移动应用程序，我们需要安装 **react-router-dom** 包，所以在 react 项目中，使用您的终端(Linux 或 MAC)或命令提示符(Windows)运行以下命令:

```
npm install --save react-router-dom 
```

Enter fullscreen mode Exit fullscreen mode

## 了解和使用路由器

*   BrowserRouter:这是路由器接口的一个子类或具体实现，它利用 HTML5 历史 API 将你的用户界面与当前浏览器的 url 同步，或者实际上是 url 的路径，即 window.location
*   HashRouter:就像前面的路由器一样，但是只使用 URL 的散列部分，即 window.location.hash。
*   内存路由器
*   NativeRouter:用于在 react-native 移动应用程序内部进行路由。
*   StaticRouter:用于静态路由，就像 React Router v3。

### 浏览器路由器 vs 散列路由器

路由器组件有很多种，其中 **< BrowserRouter >** 和 **< HashRouter >** 用于客户端的 React 应用。如果你使用的是可以处理动态 URL 的动态服务器，那么你需要使用**浏览器**组件，但是如果你使用的是只提供静态文件的服务器，那么**浏览器**组件就是在这种情况下使用的。

## 了解和使用路线

**< Route >** 组件是 React Router v4 最有用的组件之一，其背后的思想很简单，只要你想渲染某个东西，只要有与该位置的路径匹配，你就可以使用 Route 组件。

**路由**组件具有许多属性，例如:

*   path 属性:字符串类型，保存要匹配的路径的名称。
*   component 属性:如果匹配，它保存要呈现的组件的名称。
*   精确属性:该属性告诉 Route 精确匹配路径(请参见包含和排除路由)
*   strict 属性:这个属性告诉 Route 只匹配一个带有斜杠的路径。

有两个其他属性可以代替 component 属性，告诉 Route 组件在匹配时需要呈现什么:

*   render 属性:返回 React 元素函数。对于内联呈现或包装呈现的组件更有用。
*   children:也是一个呈现 React 元素的函数。除了这个即使没有匹配的路径也会一直渲染。

大多数情况下，您会使用组件属性，但也有许多情况下，您必须使用渲染或子属性。这三个方法将通过这三个道具:

*   比赛
*   位置
*   历史

例如:

使用组件:

```
<Route exact path="/" component={HomePage} /> 
```

Enter fullscreen mode Exit fullscreen mode

当浏览器的位置路径与`/`完全匹配时，将呈现`HomePage`组件。

使用渲染:

对于内嵌渲染:

```
<Route path="/home" render={() => <div>Home</div>}/> 
```

Enter fullscreen mode Exit fullscreen mode

用于包装:

```
const FadingRoute = ({ component: Component, ...rest }) => (
  <Route {...rest} render={props => (
    <FadeIn>
      <Component {...props}/>
    </FadeIn>
  )}/>
)

<FadingRoute path="/cool" component={Something}/> 
```

Enter fullscreen mode Exit fullscreen mode

利用孩子:

```
<ul>
  <ListItemLink to="/somewhere"/>
  <ListItemLink to="/somewhere-else"/>
</ul>

const ListItemLink = ({ to, ...rest }) => (
  <Route path={to} children={({ match }) => (
    <li className={match ? 'active' : ''}>
      <Link to={to} {...rest}/>
    </li>
  )}/>
) 
```

Enter fullscreen mode Exit fullscreen mode

有关 React 路由器 v4 如何匹配路径的更多信息，请参见 [path-to-regexp](https://github.com/pillarjs/path-to-regexp) 用于匹配路径的模块。

## URL/路径/路由参数

通常，路径名中有一些可变部分用于在应用程序的不同路径之间传递信息，那么我们如何捕获这些变量并将它们传递给组件呢？我们可以将变量的名称加上冒号 **:** 附加到路线路径的末尾，例如:

```
<Route path="/:param1" component={Home}/>
const Home = ({ match }) => (
  <div>
    <h1> Parameter 1 : {match.params.param1}</h1>
  </div>
) 
```

Enter fullscreen mode Exit fullscreen mode

当存在路径匹配时，将创建具有以下属性的对象并将其传递给组件:

*   URL:URL 的匹配部分。
*   路径:简单来说就是路径。
*   如果 path 完全等于当前位置的路径名，isExact: equals **True** 。
*   params:包含 URL 参数的对象。

## 理解和使用链接

链接是 React 路由器 v4 组件，旨在替代锚链接，以创建导航元素，使用户能够在 React 应用程序的不同页面之间导航。与重新加载整个页面的锚不同，链接只重新加载 UI 中与浏览器位置路径匹配的部分。

一个**链接**组件接受一个**到**属性，该属性告诉 React 路由器要导航到的目的地。比如:

```
import { Link } from 'react-router-dom'
const Nav = () => (
    <Link to='/'>Home</Link>
) 
```

Enter fullscreen mode Exit fullscreen mode

单击后将带我们到路径为的位置:/

**到**属性可以接受一个字符串或一个位置(路径名、散列、搜索和状态)对象，例如:

```
<Link to={ {
  pathname: '/me',
  search: '?sort=asc',
  hash: '#hash',
  state: { fromHome: true }
} } /> 
```

Enter fullscreen mode Exit fullscreen mode

**链接**也可以带另一个属性:**替换**如果**为真**，当点击时，历史中的链接条目将被替换。

## <林克> vs < NavLink >

**NavLink** 是 **Link** 的子类，它将样式信息添加到呈现的元素中，例如:

```
import { NavLink } from 'react-router-dom'

<NavLink
  to="/me"
  activeStyle=
   activeClassName="selected">My Profile</NavLink> 
```

Enter fullscreen mode Exit fullscreen mode

## 用 React 路由器 DOM 编写我们的第一个例子

现在让我们编写一个 React 应用程序示例，向您展示如何使用 **BrowserRouter** 来实现路由。

首先我们导入必要的路由组件，如**路由**和**浏览器路由器**T4】

```
import { BrowserRouter } from 'react-router-dom'
import { Route } from 'react-router-dom' 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们创建基本布局组件，除了常见的 HTML 标签，我们还使用 React 路由器 v4 组件 **Link** 和 **Route** :

```
const BaseLayout = () => (
  <div className="base">
    <header>
      <p>React Router v4 Browser Example</p>
        <nav>
          <ul>
            <li><Link to='/'>Home</Link></li>
            <li><Link to='/about'>About</Link></li>
            <li><Link to='/me'>Profile</Link></li>
            <li><Link to='/login'>Login</Link></li>
            <li><Link to='/register'>Register</Link></li>
            <li><Link to='/contact'>Contact</Link></li>

          </ul>
        </nav>
    </header>
    <div className="container">
      <Route path="/" exact component={HomePage} />
      <Route path="/about" component={AboutPage} />
      <Route path="/contact" component={ContactPage} />
      <Route path="/login" component={LoginPage} />
      <Route path="/register" component="{RegisterPage}" />
      <Route path="/me" component={ProfilePage} />
    </div>
    <footer>
        React Router v4 Browser Example (c) 2017
    </footer>
  </div>
) 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们创建我们的页面:

```
const HomePage = () => <div>This is a Home Page</div>
const LoginPage = () => <div>This is a Login Page</div>
const RegisterPage = () => <div>This is a Register Page</div>
const ProfilePage = () => <div>This is the Profile Page</div>
const AboutPage = () => <div>This is an About Page</div>
const ContactPage = () => <div>This is a Contact Page</div> 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们创建应用程序组件， **BrowserRouter** 组件保存我们的基本布局组件，然后呈现应用程序。

```
const App = () => (
  <BrowserRouter>
    <BaseLayout />
  </BrowserRouter>
)
render(<App />, document.getElementById('root')) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，使用 React 路由器 v4 组件创建带路由的应用程序非常容易。

## 了解包容性路由

在我们的示例应用程序中，我们在组件主页
的路径中使用了属性 **exact**

```
<Route path="/" exact component={HomePage} /> 
```

Enter fullscreen mode Exit fullscreen mode

这是因为 React 路由器 v4 使用包含路由而不是 React 路由器 v3 使用的排他路由，因此如果没有确切的属性，home 组件将与所有其他组件一起呈现，例如，当用户访问`/login`路径时，`/`和`/login`路径都将匹配，并且它们对应的组件`LoginPage`和`HomePage`将被呈现。但这不是我们要寻找的行为，这就是为什么我们需要添加确切的属性来告诉路由组件精确匹配`/`路径。

现在，让我们看看如何使用包含路由在我们的优势，让我们假设我们有一个子菜单组件，只需要当我们在配置文件页面可用，我们可以很容易地改变我们的基本布局，以添加这个要求:

```
const BaseLayout = () => (
  <div className="base">
    <header>
      <p>React Router v4 Browser Example</p>
        <nav>
          <ul>
            <li><Link to='/'>Home</Link></li>
            <li><Link to='/about'>About</Link></li>
            <li>
                <Link to='/me'>Profile</Link>
                <Route path="/me" component={ProfileMenu} />
            </li>
            ...
) 
```

Enter fullscreen mode Exit fullscreen mode

因此，如您所见，当我们访问“/me”路径时，所有带有路径“/me”的路由都将被呈现，而不仅仅是第一个匹配，也就是**包含路由**。

## 了解独占路由

独占路由与包含路由相反，它是 React Router v3 中的默认路由，其中仅呈现第一个匹配，那么如果您想要独占路由回来呢？这也可以通过 v4 路由器使用**交换机**组件来完成。在一个**切换**组件中只有第一个子 **<路径>** 或 **<重定向>** ，即匹配位置，才会被渲染。例如:

```
import { Switch, Route } from 'react-router'    

<Switch>
  <Route exact path="/" component={HomePage}/>
  <Route path="/about" component={AboutPage}/>
  <Route path="/me" component={ProfilePage}/>
  <Route component={NotFound}/>
</Switch> 
```

Enter fullscreen mode Exit fullscreen mode

## 浏览器历史

**React 路由器 v4** 提供了一个**历史**对象，该对象公开了一个具有不同实现的简单 API(dom 的 HTML5 历史 API，DOM 的传统哈希历史，react-native 的内存历史)来管理/操作浏览器历史。

您还可以使用来自**历史**对象的方法在 React 应用程序中导航，例如:

```
history.push("/my-path")
history.replace("/my-path") 
```

Enter fullscreen mode Exit fullscreen mode

其中相当于:

```
<Link to="/my-path"/>
<Redirect to="/my-path"/> 
```

Enter fullscreen mode Exit fullscreen mode

## 如何用<重定向>组件进行重定向

每当你想重定向到另一个位置时，你可以放置**组件，该组件在渲染时将重定向到**到**属性中指定的位置，该属性可以是一个字符串或一个位置对象，例如:** 

```
<Redirect to={ {
  pathname: '/register',
  search: '?utm=techiediaries',
  state: { referrer: techiediaries.com }
} }/> 
```

Enter fullscreen mode Exit fullscreen mode

或者干脆:

```
<Redirect to="/register"/> 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

**React Router v4** 使得创建具有复杂 ui 的 React 应用程序变得非常容易，这些 ui 在不同部分之间具有路由，您可以简单地声明一个**路由器**组件，如**浏览器路由器**或**散列路由器**，并在其中放置一组子**路由**组件，这些组件具有指示要匹配的**路径**和每当有匹配时要包含地呈现的**组件**的属性(即如果你需要进行独占渲染(就像 React Router v3:即只有第一个匹配的 **Route** 将被渲染)，那么你可以简单地使用一个 **Switch** 组件，其中只有一个孩子(第一个找到的)将被渲染。您还可以使用参数在路径之间传递不同的信息，这些参数可以从 **match** 对象中捕获和检索，一旦建立了匹配，该对象就会被创建，然后被传递给当前的渲染组件。最后， **React Router v4** 中为 web 应用程序路由设计的所有构建模块或组件都可用，并且可以从 **react-router-dom** 中导入。**