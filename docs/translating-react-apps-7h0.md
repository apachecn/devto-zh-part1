# 翻译 React 应用程序

> 原文：<https://dev.to/gadventurestech/translating-react-apps-7h0>

*原贴于[tech.gadventures.com](https://tech.gadventures.com/translating-react-apps-99dede52d924)T3】*

想象你半夜在罗马火车站，周围没有人。你需要买一张去威尼斯的票。售票软件是意大利语的。你不会说意大利语。经过一番挣扎，你设法弄到了一张票。你上了火车。更像意大利人。3 小时的旅程。你看欢迎标志。“欢迎来到佛罗伦萨”。叹气。佛罗伦萨不是威尼斯。

<figure>[![](img/a2994f0b8164b5f7e673602ee19b2e7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2jqYxt1y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/850/1%2ApqLmZeeFn8P24_4eOOSdsA.jpeg) 

<figcaption>罗马终点站</figcaption>

</figure>

在理想世界中，我们都会说多种语言，就像我们的一些欧洲旅行者一样。但并不是每个人都懂英语，所以最好能支持尽可能多的语言。

你们中的一些人可能知道，G 有一个旅行应用程序，叫做 Good to Go。在旅行之前，您可能需要完成某些要求。这些包括向我们发送您的签证复印件、您的夹克尺寸、您的食物偏好等——我们称之为“旅行要求”。在旅行者开始他们的冒险之前，Good to Go 可以帮助他们确保他们在旅行之前拥有所有需要的东西。

成千上万的旅行者在旅行开始前使用它来完成他们的旅行要求。我们的每一次离别都是美好的。是大明星。事实上，G-Stock 23 标志着 Good to Go 的一周年纪念日。我们会继续努力，每天都做得更好。

上个月，我们带来了德国对 Good to Go 的支持，这是用 React 编写的。多亏了雅虎的 React-Intl 库，这变得非常容易。这篇文章详细介绍了我将国际化加入 Good to Go 的经历。

首先，在翻译时，单词的顺序很重要，日期和名字的格式也很重要。这些东西组合在一起，被称为“区域数据”。

安装 react-intl 非常简单，但是在初始纱线添加 react-intl 之后，您需要完成一些额外的步骤。让我们现在过一遍。

#### 通天塔

我将举例说明如何用 Babel 和 Webpack 做到这一点。首先安装 babel-plugin-react-intl 插件(yarn add babel-plugin-react-intl)。它将提取标记为翻译的字符串。要激活插件，你必须将其名称添加到你的。babelrc 文件。

```
"plugins": [
    ...,
    ["react-intl", {
        "messagesDir": "./src/translations",
        "enforceDescriptions": false
    }],
    ...
] 
```

在上面的例子中，react-intl 将标记的消息提取到 src/translations 中。消息存储在 JSON 文件中，位于以组件命名的目录中。

当需要将消息发送给翻译服务时，您可能希望将所有消息保存在一个文件中。出于这个原因，库的创建者已经编写了一个脚本，你可以用它将所有的字符串合并成一个文件。要查看更详细的例子，[见本帖。](https://medium.freecodecamp.org/internationalization-in-react-7264738274a0)

#### 本地数据

每种语言的地区数据都是独立提供和导入的，所以您只需发送您需要的数据。在下面的例子中，我们导入英语和德语的地区数据。

```
import { addLocaleData } from 'react-intl'
import enLocaleData from 'react-intl/locale-data/en'
import deLocaleData from 'react-intl/locale-data/de' 
```

所以过程是:

1.  安装库
2.  安装巴别塔插件
3.  导入区域设置数据

最后，用 IntlProvider 包装根组件，这样所有翻译的消息和语言环境数据都可以加载到应用程序使用的 React Intl 组件中。

```
<IntlProvider locale="de" messages={messages}>
     <Root />
</IntlProvider> 
```

另一种方法是在应用生命周期的“启动”阶段将这些值直接注入 intl reducer，比如 index.js 或 configureStore 方法。

```
{
    ...,
    intl: {
        defaultLocale: 'en',
        locale: 'en',
        messages: allMessages.en
    },
    ...
} 
```

#### 用 React-Intl 格式化

react-intl 的核心是它的标签系统。您可以通过将字符串包装在 Rect 组件中或使用 API 来标记(或标记)要翻译的字符串。

1.  反应组分

```
import { FormattedMessage } from 'react-intl'

<FormattedMessage
    id="header.selectPax"
    defaultMessage="Select the traveller for whom you'd like to enter requirement information." 
/> 
```

使用该组件非常简单，而且功能强大。例如，它们可以取值，如下例所示:

```
<FormattedMessage
    id="form.name"
    defaultMessage="{name}"
    values={{ name }} 
/> 
```

提供给 values 的对象可以是另一个 FormattedMessage，这意味着您可以像用链一样将它们串起来，以创建一个带有 HTML 标签的消息！

```
<FormattedMessage
    id="body.text"
    defaultMessage="{bodyText}"
    values={{
        bodyText:
            <div>
                <h2>
                    <FormattedMessage 
                        id="bodyText.1"
                        defaultMessage="Ready for a G Adventure?" />
                </h2>
            </div>
    }} 
/> 
```

1.  应用程序接口

```
import { defineMessages } from 'react-intl'

const formButtons = defineMessages({
    login: {
        id: 'formButtons.login',
        defaultMessage: 'Login'
    }
}) 
```

使用 API，您可以提前在一个简单的 Javascript 对象中定义您的消息。

API 在 React 组件不适用的地方为您提供了翻译后的字符串。例如，在一个<button>的内部。</button> 

```
import { injectIntl } from 'react-intl'

const myForm = ({ intl }) => {
    <button
        type="submit"
        onClick={handleSubmit}>
        {intl.formatMessage(formButtons.login)}
    </button>
}

export default injectIntl(Waiver) 
```

您已经看到了使用 react-intl 库翻译字符串的两种方法。我建议在任何地方使用它。当你的用户看到你的母语内容时，你会很高兴。

请关注本系列关于翻译的第 2 部分，在那里我们将介绍如何构建一个使用 react-intl 的语言选择器。

*继续关注我们的* [*Medium*](https://tech.gadventures.com/) *和*[*Twitter*](https://twitter.com/gadventurestech)*获取更多科技世界片段据 G！*

想去 G 冒险公司工作吗？加入我们不断成长的团队，环游世界！ [*查看我们所有的职位，今天就申请*](https://gadventures.com/careers) *。*

* * *