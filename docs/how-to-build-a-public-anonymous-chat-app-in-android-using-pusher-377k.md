# 如何使用 Pusher 在 Android 中构建公共匿名聊天 app

> 原文：<https://dev.to/mezie/how-to-build-a-public-anonymous-chat-app-in-android-using-pusher-377k>

今天，我们将学习如何用 Android 建立一个公共匿名聊天室。

在本教程中，我们将使用 React Native 来构建我们的 Android 应用程序。React Native 允许您仅使用 JavaScript 构建移动应用程序。它使用与 React 相同的设计，允许您从声明性组件构建丰富的移动 UI。要了解更多关于 React Native 的信息，请访问[这里](https://facebook.github.io/react-native/)。

## 设置 React Native

首先，如果我们还没有 React Native CLI，我们需要安装它。要安装 React Native，我们运行:

```
npm install -g react-native-cli 
```

安装完 CLI 后，就该创建我们的项目了。打开一个终端，使用以下命令创建一个名为`pubchat`的新项目:

```
react-native init pubchat 
```

我们等待 React Native 完成所有安装，然后我们可以将目录更改为新项目，并使用以下命令运行应用程序:

```
//change directory to pubchat
cd pubchat
//run the application
react-native run-android 
```

**请注意，在运行`run-android`命令之前，您应该运行一个仿真器，或者通过`adb`** 连接一个 Android 设备。

你可以从[https://Facebook . github . io/React-Native/docs/Android-setup . html](https://facebook.github.io/react-native/docs/android-setup.html)了解更多关于设置 React 原生 Android 应用的信息

此时，我们应该看到这种屏幕:

[![](img/34bfc283ca445e17e62b190226bb80dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a6wfJNUH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.pusher.com/wp-content/uploads/2017/04/build-public-anonymous-chat-app-android-pusher-first.png)

但是，您可能会遇到这样的错误:

[![](img/da9f2007e9790af46a5b98a951ed3bb4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xQ_b5hsl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.pusher.com/wp-content/uploads/2017/04/build-public-anonymous-chat-app-android-pusher-error.png)

要修复该错误，您只需运行以下命令:

```
react-native start 
```

## 设置推动器

此时，React Native 已经准备好并设置好了。我们需要设置 Pusher，以及获取我们的应用程序凭据。

我们需要在 [Pusher](https://pusher.com/signup) 上注册并创建一个新的应用程序，还需要复制我们的秘密、应用程序密钥和应用程序 id。

[![](img/bfc863cca6df29dae61319e5ebd0982b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9eDIz9bw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.pusher.com/wp-content/uploads/2017/04/build-public-anonymous-chat-app-android-pusher-setup.png)

然后，我们需要安装所需的库:

```
npm install native-base pusher-js pusher express body-parser --save 
```

在上面的 bash 命令中，我们安装了 4 个包。我将在下面解释这四个包的作用:

*   Native-base:React Native 必不可少的跨平台 UI 组件。这有助于我们减少自己编写和设计 UI 组件的时间。
*   pusher-js:这是官方的 Pusher JavaScript 客户端。我们将使用它的 React 本地库来订阅和监听应用程序中的事件。
*   pusher:这是 Node.js 的官方 Pusher 库。我们将在我们的 API 中使用 Node.js，所以这个库将会派上用场。
*   express:这是一个 Node.js web 框架，我们将使用它来创建我们的 API。
*   body-parser:Express 使用这个库来解析主体请求。

安装完这些包后，我们需要将它们与 React Native 链接起来，因此我们运行以下命令:

```
react-native link 
```

* *请注意，因为我们将使用 Fetch 来执行 AJAX 请求，我们需要转到位于`android/app/src/AndroidManifest.xml`的 android 清单，并添加以下权限:**

```
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /> 
```

## 创建我们的 API

首先，让我们创建一个名为`server.js`的新文件，它作为我们根文件夹中的 API，并将以下内容放入其中:

```
// server.js

//require express
var express = require('express')
//define app as in instance of express
var app = express()
//require bosy-parser
var bodyParser = require('body-parser')
//require pusher
var Pusher = require('pusher')
//use bodyparser as a middle ware
app.use(bodyParser.json())
//instantiate pusher
const pusher = new Pusher({
  appId: 'XXX_APP_ID',
  key: 'XXX_APP_KEY',
  secret: 'XXX_APP_SECRET',
  cluster: 'XXX_APP_CLUSTER',
  encrypted: true
});
//set cors middleware
app.use(function(req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
  next();
});
//handle route postfunction
app.post('/', function (req, res) {
    pusher.trigger("pubchat", "message_sent", { message : req.body.message, name : "Anonymous" });
    res.send({
        message:'message_sent'
    });
})
//listen on port and serve the app
app.listen(3000, function () {
  console.log('Example app listening on port 3000!')
}) 
```

上面的代码块是我们的 Express 服务器设置。在文件的开头，我们分别需要 Node.js 的 Express、Body-parser 和 Pusher 库。我们还初始化了一个新的 Pusher 对象，将我们的`appId`、`key`、`secret`传递给它，并将对象的输出设置为一个名为`pusher`的常量。

接下来，我们使用中间件函数为请求设置 CORS 报头。

最后，我们为`\`路由创建一个 post 处理程序，然后用一个名为`message_sent`的事件对一个名为`pubchat`的通道进行`Pusher`触发。

让我们记下这个服务器上使用的通道名和事件名。频道名称将被订阅，而我们将在我们的 React 本机应用程序中监听事件。

这是我们在服务器端进行 API 调用所需要的。

接下来，我们进入命令行并运行:

```
node server.js 
```

## 精心制作应用程序

现在，让我们用以下内容替换我们的`index.android.js`:

```
// index.android.js

/**
 * Sample React Native App
 * https://github.com/facebook/react-native
 * @flow
 */

import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  TextInput,
  ScrollView
} from 'react-native';
// import native base components
import { Container, Content, Footer, Button} from 'native-base';
//import pusher
import Pusher from 'pusher-js/react-native'
//react-native class
export default class pubchat extends Component {
//load constructor
constructor(props){
  super(props);
  //declare an array of messages
  var messages_array = [];
  // declare initial states
   this.state ={
    messages_array,
    text:'' 
   }

  //instantiate pusher
  var pusher = new Pusher('XXX_APP_KEY', {
    cluster: 'XXX_APP_CLUSTER'
  });
  //subscribe to the public chat channel
  var my_channel = pusher.subscribe('pubchat');
  //bind and listen for chat events
  my_channel.bind("message_sent", (data)=> {
     this.state.messages_array.push(data);
        this.setState({
          text:''
        })
  });
}

  //function that sends messahe
  send_message(){
    //check that the text input isnt empty
    if(this.state.text !=""){
      fetch('XXX_IP_TO_MY_ROUTE', {
        method: 'POST',
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          message: this.state.text
        })
      })
      .then((response) => response.json()) 
      .then((responseJson) => {}) 
      .catch((error) => { console.error(error); });
    }
  }

  //function that loops over our messages and displays them
  loop(){
      var element = [];
     for (var index = 0; index < this.state.messages_array.length; index++) {

            element.push(<View key={"container"+index} >
                            <Text key = {"author"+index}>
                              {this.state.messages_array[index].name}
                            </Text>
                            <Text key = {index} style={styles.bubble_you} >
                              {this.state.messages_array[index].message}
                            </Text>
                        </View>);
        }
         return element;
  };

  //render function that actually shows the page
  render() {
    //execute the loop function and store its response into a variable
    myloop = this.loop();

    return (
      <Container>
      <ScrollView >
        <View style={styles.container}>
          <Text style={styles.welcome}>
            Welcome to the public chat room!
          </Text>
              {myloop}
        </View>
        </ScrollView>
        <Footer >
          <TextInput
            value ={this.state.text}
            style={{width: '80%'}}
            placeholder="Enter Your message!"
            onChangeText={(text) => this.setState({text})}
          />
          <Button onPress={()=> this.send_message()}>
            <Text>Send</Text>
          </Button> 
        </Footer>
      </Container>
    );
  }
}

//stylesheet 
const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
  bubble_you: {
    color: '#fff',
    backgroundColor: '#00b0ff',
  width: '50%',
  borderRadius: 25,
  padding: 7,
  marginBottom: 2,
  },
});

AppRegistry.registerComponent('pubchat', () => pubchat); 
```

上面，我们已经导入了原生基本组件来帮助我们进行 UI 样式化，而不是默认的 React 原生组件。接下来，我们为 React Native 导入 Pusher，然后声明 React Native 类。

我们通过创建一个构造函数来继续，在我们的构造函数中，声明了两个状态，即:`messages_array`和`text`，它们分别代表我们的消息数组以及正在键入的当前文本。

接下来，我们实例化 Pusher，传入我们的`APP_KEY`。然后，我们订阅我们从名为`pubchat`的服务器发送到的频道，我们还收听从我们的服务器触发的`message_sent`事件。

在监听`message_sent`事件时，我们推送到达我们`messages_array`状态的数据，并且还将我们的`text`状态设置为空。

接下来，我们创建一个将消息发送到服务器的函数，这样就可以将消息发送到 Pusher。在这个函数中，我们首先检查状态是否为空，以避免向服务器发送空消息。

接下来，我们使用 React Native 提供的`fetch` API 向我们之前创建的服务器发送一个 AJAX 请求。

**注意:如果你使用 IP 地址如`127.0.0.1`或`localhost`，请求很可能会失败。这是因为，在 React Native 中，`localhost`或`127.0.0.1`指的是内部应用。请改用您系统的网络 IP。**

接下来，我们定义一个`loop`函数，该函数循环遍历所有消息，并将它们推入一个返回的数组中。该函数将用于在 UI 上显示所有消息。

下一个函数是我们的`render`函数，是一个原生的 React 原生函数。首先，我们声明一个名为`myloop`的变量，并将其设置为我们的`loop`函数。

在我们的 return 语句中，呈现了`myloop`变量，因此它可以显示其内容。另外，看看我们这里的`footer`标签。在`footer`标签中，我们有一个`text input`和一个`button`。

文本输入文本用于在文本使用按钮的`onChangeText`事件改变时设置`text`状态。注意，我们的按钮也调用`send_message`函数，只要它被绑定到它的`onPress`函数。

最后，我们定义了一些样式表。

此时，如果我们重新加载我们的应用程序，我们的应用程序将如下所示:

[![](img/8de3108bb047948e30607a14783a534d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--glgr-UsB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.pusher.com/wp-content/uploads/2017/04/build-public-anonymous-chat-app-android-pusher-lookslike.png)

此时，一旦我们的服务器启动并运行，我们应该转到应用程序，输入一条消息，然后发送。

这是我们所构建的演示:

[![](img/73cc8116085695a2620f1930f5df10c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KhyGIbuS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://blog.pusher.com/wp-content/uploads/2017/04/build-public-anonymous-chat-app-android-pusher-demo.gif)

完整的源代码可以在 [Github](https://github.com/ammezie/android-pubchat) 上找到以供参考。

## 结论

在本文中，我们演示了如何使用 React Native 在 Android 中创建一个公共匿名聊天应用程序。首先，我们已经获得了重要的设计选择，上面的案例应该可以帮助你填补漏洞，并给出你可以获得的其他设计选择的一部分的轮廓。

这个教程在多大程度上帮助你开始使用 Pusher 和 Android 应用程序？你是否有其他想谈论的用例或场景？请在评论中告诉我们。

这篇文章最初由作者发布在 [Pusher 博客](https://blog.pusher.com/build-public-anonymous-chat-app-android-pusher)上。