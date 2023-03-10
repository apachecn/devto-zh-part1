# 使用 React Native 和 Pusher 创建乘车预订应用程序

> 原文：<https://dev.to/wernancheta/creating-a-ride-booking-app-with-react-native-and-pusher-1gf1>

在本教程中，我们将使用 React Native 和 Pusher 创建一个乘车预订应用程序。我们将创建的应用程序将类似于优步、Lyft 或 Grab 等流行的乘车预订应用程序。React Native 将用于为司机和乘客创建一个 Android 应用程序。Pusher 将用于两者之间的实时通信。

## 你会创造什么

就像市场上的任何其他乘车预订应用程序一样，会有一个司机应用程序和一个乘客应用程序。乘客应用程序将用于预订乘车，而司机应用程序只接收来自乘客应用程序的任何请求。出于一致性目的，我们将该应用程序简称为“grabClone”。

## App 流量

我们将要创建的克隆将与任何乘车预订应用程序具有几乎相同的流程:乘客预订乘车→应用程序寻找司机→司机接受请求→司机接乘客→司机开车到目的地→乘客付钱给司机。

在这里，我只想向您展示这个过程在应用程序中的样子。这样你就会对你要创造的东西有一个清晰的认识。

1.  该应用程序确定用户的位置，并在地图中显示出来(注意:此时需要启用 GPS)。
    [![Default screen](img/4194a18db0939c9d8d3b292d0309ddc1.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--xHkwTmux--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500612181544_Screenshot_2017-07-20-10-30-07.png)

2.  从乘客应用程序中，用户点击“预订乘车”。

3.  将打开一个模式，允许乘客选择他们想去的地方。
    [![Current location](img/278f9f6702333e4a1e5f96d95f4881ca.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--KGxU1r_T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500612225092_Screenshot_2017-07-20-10-30-48.png)

4.  该应用程序要求乘客确认他们的目的地。
    [![Confirm destination](img/edaa7253b36df71a992ed265586214f8.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--jLJBfgck--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500612322001_Screenshot_2017-07-20-10-31-03.png)

5.  一旦确认，该应用程序将向司机应用程序发送请求，以搭载乘客。当应用程序等待驱动程序接受请求时，会显示一个加载动画。
    [![waiting for driver](img/9821f96848bebba6e88e83dbfe99b172.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--fZCfiDYl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500612898340_Screenshot_2017-07-20-10-31-15.png)

6.  驱动程序应用程序接收请求。从这里，司机可以接受或拒绝请求。
    [![driver receives request](img/e17f90f011d7cf40000e3da71637d670.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--RUDuZcus--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500612408897_Screen%2BShot%2B2017-07-20%2Bat%2B10.34.21%2BAM.png)

7.  一旦司机接受请求，司机的详细信息就会显示在乘客应用程序中。
    [![Driver details are sent to passenger](img/8b7e2660d2986eaf36d0ce78d72176f8.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--6fqeC87N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500613002366_Screenshot_2017-07-20-10-32-39%2B1.png)

8.  乘客应用程序会在地图上显示驾驶员的当前位置。
    [![Current location of driver](img/2005b51641ba4b3a25499f4ec54f268f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--eHcOCU5U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500613036704_Screenshot_2017-07-20-10-34-03.png)

9.  一旦司机在乘客位置的 50 米范围内，他们就会看到一个警告，说司机就在附近。

10.  一旦司机在乘客位置的 20 米范围内，司机应用程序就会向乘客应用程序发送一条消息，表明司机就要到了。
    [![Notification near drop-off point](img/0c983d890ae415bdbbfd5a1a1c7ce966.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--TXSQOMzj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500613085385_Screenshot_2017-07-20-10-35-43.png)

11.  接完乘客后，司机开车去目的地。

12.  一旦司机距离目的地不到 20 米，司机应用程序就会向乘客应用程序发送一条消息，告诉他们已经非常接近目的地了。
    [![Notification 20 meters near drop-off point](img/29eef0ef0c510e54c342ea15d9a4c548.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--lLOvm-mP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500613201701_Screenshot_2017-07-20-10-39-18.png)

此时，乘坐结束，乘客可以预订另一次乘坐。驾驶员也可以自由地接受任何到来的乘车请求。

## 先决条件

*   [**推手账号**](https://pusher.com/) - [注册一个推手账号](https://dashboard.pusher.com/accounts/sign_up)或[用您已有的账号](https://dashboard.pusher.com/accounts/sign_in)登录。一旦你创建了一个账户，创建一个新的应用程序→选择“React”作为前端技术→选择“Node.js”作为后端技术。![create Pusher app](img/7aa2bae103b787a9534b776bedd7b9e9.png)

接下来，单击“应用程序设置”选项卡，并选中“启用客户端事件”。这使我们能够让司机和乘客应用程序直接相互通信。
[![Enable client events](img/dd26fe9c88b871e1d50b9bb912a130fe.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--9eYbOnuG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500610209176_Screen%2BShot%2B2017-07-20%2Bat%2B4.31.54%2BPM.png)

最后，点击“应用密钥”并复制凭证。如果你担心定价问题，[Pusher sandbox 计划相当慷慨](https://dashboard.pusher.com/plans)，所以你可以在测试应用时免费使用。

*   [**安装 Android Studio**](https://developer.android.com/studio/index.html)——你并不是真的需要 Android Studio，但它自带的 Android SDK 正是我们需要的。谷歌也不再为它提供单独的下载。
*   安装 React Native——我推荐的方法是以本地方式构建项目。当你在 React Native 网站上时，点击“用本机代码构建项目”标签，并按照那里的说明进行操作。 [expo](https://expo.io/) 客户端非常适合快速制作应用原型，但它并没有为我们测试这个应用所需的地理定位功能提供一个快速的方法。
*   [**Genymotion**](https://www.genymotion.com/fun-zone/)——用于测试驱动 app。我们使用这个而不是默认的 Android 模拟器，因为它附带了一个 GPS 模拟工具，允许我们搜索特定的位置，并将其用作模拟设备的位置。它使用谷歌地图作为界面，你也可以移动标记。这使我们能够模拟一辆行驶的车辆。
    安装 Genymotion 后，您需要登录您的帐户才能添加设备。对我来说，我已经安装了谷歌 Nexus 5x 进行测试。
    [![Genymotion](img/c4a05c88e0611e884943228aa3e406b6.png)T8】](https://res.cloudinary.com/practicaldev/image/fetch/s--f98UiVJO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500610583449_Screen%2BShot%2B2017-07-20%2Bat%2B3.39.49%2BPM.png)

*   **Android 设备** -这将用于测试乘客应用程序。一定要检查手机的安卓版本。如果版本低至 4.2，那么你需要通过 Android SDK 管理器安装额外的软件包。这是因为，默认情况下，React 本机目标 API 版本 23 或更高。这意味着你的手机 Android 版本至少需要 6.0 版本，否则应用程序将无法运行。如果已经安装了 Android Studio，打开 Android Studio →点击“配置”→选择“SDK 管理器”即可访问 SDK 管理器。然后在“SDK 平台”下，检查您想要支持的 Android 版本。
    T3![Android SDK Manager](img/2c7996c22e51bfa6c1ea093cdea1790e.png)T5】

当你在那里时，点击“SDK 工具”,并确保你也安装了与我相同的工具:

[![Android SDK Tools](img/fdff079df86f1c34ee096e9f5ec5bc37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rglgnnmL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500610476415_Screen%2BShot%2B2017-07-20%2Bat%2B3.34.53%2BPM.png)

*   额外的电脑 -这是可选的。我只是把它包含在这里，因为 React Native 一次只能在一个设备或模拟器上运行应用程序。因此，您需要做一些额外的工作来运行这两个应用程序，稍后您会看到这一点。

## 创建认证服务器

现在是我们动手的时候了。首先，让我们在认证服务器上工作。这是必需的，因为我们将从应用程序发送[客户端事件](https://pusher.com/docs/client_api_guide/client_events)，客户端事件要求推送器通道是私有的，并且私有通道的访问受到限制。这就是授权服务器的用武之地。这是 Pusher 了解试图连接的用户是否确实是该应用程序的注册用户的一种方式。

从安装依赖项开始:

```
npm install --save express body-parser pusher 
```

接下来，创建一个`server.js`文件并添加以下代码:

```
var express = require('express');
var bodyParser = require('body-parser');
var Pusher = require('pusher');

var app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));

var pusher = new Pusher({ // connect to pusher
  appId: process.env.APP_ID, 
  key: process.env.APP_KEY, 
  secret:  process.env.APP_SECRET,
  cluster: process.env.APP_CLUSTER, 
});

app.get('/', function(req, res){ // for testing if the server is running
  res.send('all is well...');
});

// for authenticating users
app.get("/pusher/auth", function(req, res) {
  var query = req.query;
  var socketId = query.socket_id;
  var channel = query.channel_name;
  var callback = query.callback;

  var auth = JSON.stringify(pusher.authenticate(socketId, channel));
  var cb = callback.replace(/\"/g,"") + "(" + auth + ");";

  res.set({
    "Content-Type": "application/javascript"
  });

  res.send(cb);
});

app.post('/pusher/auth', function(req, res) {
  var socketId = req.body.socket_id;
  var channel = req.body.channel_name;
  var auth = pusher.authenticate(socketId, channel);
  res.send(auth);
});

var port = process.env.PORT || 5000;
app.listen(port); 
```

我不再详述上面的代码是做什么的，因为它已经在[认证用户](https://pusher.com/docs/authenticating_users)的文档中解释过了。
为了简单起见，我实际上没有添加代码来检查用户是否真的存在于数据库中。您可以在`/pusher/auth`端点中通过检查用户名是否存在来做到这一点。这里有一个例子:

```
var users = ['luz', 'vi', 'minda'];
var username = req.body.username;

if(users.indexOf(username) !== -1){
  var socketId = req.body.socket_id;
  var channel = req.body.channel_name;
  var auth = pusher.authenticate(socketId, channel);
  res.send(auth);
}

// otherwise: return error 
```

稍后连接到客户端的 Pusher 时，不要忘记传入`username`。

完成后，尝试运行服务器:

```
node server.js 
```

在你的浏览器上访问`http://localhost:5000`看看它是否工作。

## 部署认证服务器

因为 Pusher 必须连接到 auth 服务器，所以它需要可以从互联网访问。你可以使用 [now.sh](https://zeit.co/now) 来部署认证服务器。您可以使用以下命令安装它:

```
npm install now 
```

安装完成后，您现在可以导航到存放`server.js`文件的文件夹并执行`now`。您将被要求输入您的电子邮件并验证您的帐户。

验证您的帐户后，执行以下操作，将您的 Pusher 应用程序设置作为环境变量添加到 now.sh 帐户，以便您可以从服务器内部使用它:

```
now secret add pusher_app_id YOUR_PUSHER_APP_ID
now secret add pusher_app_key YOUR_PUSHER_APP_KEY
now secret add pusher_app_secret YOUR_PUSHER_APP_SECRET
now secret add pusher_app_cluster YOUR_PUSHER_APP_CLUSTER 
```

接下来，部署服务器，同时提供您已经添加的秘密值:

```
now -e APP_ID=@pusher_app_id -e APP_KEY=@pusher_app_key -e APP_SECRET=@pusher_app_secret APP_CLUSTER=@pusher_app_cluster 
```

这允许您从服务器内部访问 Pusher 应用程序设置，如下所示:

```
process.env.APP_ID 
```

now.sh 返回的部署 URL 是您稍后将用来将应用程序连接到 auth 服务器的 URL。

## 创建司机 App

现在你可以开始创建驱动程序了。

首先，创建一个新的 React 本机应用程序:

```
react-native init grabDriver 
```

**安装依赖关系**

一旦完成，在`grabDriver`目录中导航并安装我们需要的库。这包括用于与 pusher 一起工作的 [pusher-js](https://github.com/pusher/pusher-js) ，用于显示地图的 [React Native Maps](https://github.com/airbnb/react-native-maps/) ，以及用于将坐标反向地理编码为实际地名的 [React Native Geocoding](https://github.com/marlove/react-native-geocoding) :

```
npm install --save pusher-js react-native-maps react-native-geocoding 
```

一旦安装了所有的库，React Native Maps 还需要一些额外的步骤才能工作。首先是链接项目资源:

```
react-native link react-native-maps 
```

接下来，您需要创建一个 Google 项目，从 [Google 开发者控制台](https://console.developers.google.com/apis/credentials)获取 API 密钥，并启用 [Google Maps Android API](https://console.developers.google.com/apis/api/maps-android-backend.googleapis.com/overview) 和 [Google Maps 地理编码 API](https://console.developers.google.com/apis/api/geocoding-backend.googleapis.com/overview) 。之后，打开项目目录中的`android\app\src\main\AndroidManifest.xml`文件。在`<application>`标签下，添加一个包含服务器 API 密钥的`<meta-data>`。

```
<application>
    <meta-data
      android:name="com.google.android.geo.API_KEY"
      android:value="YOUR GOOGLE SERVER API KEY"/>
</application> 
```

在那里，在默认权限下添加以下内容。这允许我们检查网络状态并从设备请求地理位置数据。

```
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" /> 
```

还要确保它与用 Genymotion 安装的设备使用相同的 API 版本。正如我之前所说，如果它的版本 23 或以上，你真的不需要做任何事情，但如果它低于这个版本，那么它必须准确的应用程序工作。

```
<uses-sdk
        android:minSdkVersion="16"
        android:targetSdkVersion="23" /> 
```

最后，由于我们将主要使用 Genymotion 来测试驱动程序，您需要遵循这里的中的[说明。如果链接断开，您需要做的是:](https://www.genymotion.com/help/desktop/faq/#google-play-services)

1.  参观 opengapps.org。
2.  选择 x86 作为平台。
3.  选择与您的虚拟设备相对应的 Android 版本。
4.  选择 nano 作为变体。
5.  下载 zip 文件。
6.  将 zip 安装程序拖放到新的 Genymotion 虚拟设备中(仅限 2.7.2 和更高版本)。
7.  按照弹出的说明进行操作。

我们需要这样做，因为 React 原生地图库主要使用谷歌地图。我们需要添加 Google Play 服务，以便它能够正常工作。与大多数已经安装了该功能的 Android 手机不同，由于知识产权的原因，Genymotion 在默认情况下没有安装该功能。因此，我们需要手动安装它。

如果你在这篇文章发布后的某个时候读到它，请务必检查一下[安装文档](https://github.com/airbnb/react-native-maps/blob/master/docs/installation.md)以确保你没有遗漏任何东西。

**编写驱动程序**

现在，您已经准备好开始编写应用程序了。首先打开`index.android.js`文件，用以下代码替换默认代码:

```
import { AppRegistry } from 'react-native';
import App from './App';
AppRegistry.registerComponent('grabDriver', () => App); 
```

这样做的目的是导入`App`组件，这是应用程序的主要组件。然后，它被注册为默认组件，因此它将呈现在屏幕上。

接下来，创建`App.js`文件并从 React 原生包中导入我们需要的东西:

```
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View,
  Alert
} from 'react-native'; 
```

还要导入我们之前安装的第三方库:

```
import Pusher from 'pusher-js/react-native';
import MapView from 'react-native-maps';

import Geocoder from 'react-native-geocoding';
Geocoder.setApiKey('YOUR GOOGLE SERVER API KEY'); 
```

最后，导入`helpers`文件:

```
import { regionFrom, getLatLonDiffInMeters } from './helpers'; 
```

`helpers.js`文件包含以下内容:

```
export function regionFrom(lat, lon, accuracy) {
  const oneDegreeOfLongitudeInMeters = 111.32 * 1000;
  const circumference = (40075 / 360) * 1000;

  const latDelta = accuracy * (1 / (Math.cos(lat) * circumference));
  const lonDelta = (accuracy / oneDegreeOfLongitudeInMeters);

  return {
    latitude: lat,
    longitude: lon,
    latitudeDelta: Math.max(0, latDelta),
    longitudeDelta: Math.max(0, lonDelta)
  };
} 

export function getLatLonDiffInMeters(lat1, lon1, lat2, lon2) {
  var R = 6371; // Radius of the earth in km
  var dLat = deg2rad(lat2-lat1);  // deg2rad below
  var dLon = deg2rad(lon2-lon1); 
  var a = 
    Math.sin(dLat/2) * Math.sin(dLat/2) +
    Math.cos(deg2rad(lat1)) * Math.cos(deg2rad(lat2)) * 
    Math.sin(dLon/2) * Math.sin(dLon/2)
    ; 
  var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a)); 
  var d = R * c; // Distance in km
  return d * 1000;
}

function deg2rad(deg) {
  return deg * (Math.PI/180)
} 
```

这些函数用于获取 React 本地地图库显示地图所需的纬度和经度增量值。另一个函数(`getLatLonDiffInMeters`)用于确定两个坐标之间的米距离。稍后，这将允许我们通知用户他们是否已经彼此接近，或者他们何时接近他们的目的地。

接下来，创建主应用程序组件并声明默认状态:

```
export default class grabDriver extends Component {

  state = {
    passenger: null, // for storing the passenger info
    region: null, // for storing the current location of the driver
    accuracy: null, // for storing the accuracy of the location
    nearby_alert: false, // whether the nearby alert has already been issued
    has_passenger: false, // whether the driver has a passenger (once they agree to a request, this becomes true)
    has_ridden: false // whether the passenger has already ridden the vehicle
  }
}
// next: add constructor code 
```

在构造函数中，初始化将在整个应用程序中使用的变量:

```
constructor() {
  super();

  this.available_drivers_channel = null; // this is where passengers will send a request to any available driver
  this.ride_channel = null; // the channel used for communicating the current location
  // for a specific ride. Channel name is the username of the passenger

  this.pusher = null; // the pusher client
}

// next: add code for connecting to pusher 
```

在安装组件之前，连接到您之前创建的 auth 服务器。务必更换按钮键、`authEndpoint`和`cluster`的值。

```
componentWillMount() {
  this.pusher = new Pusher('YOUR PUSHER KEY', {
    authEndpoint: 'YOUR PUSHER AUTH SERVER ENDPOINT',
    cluster: 'YOUR PUSHER CLUSTER',
    encrypted: true
  });

  // next: add code for listening to passenger requests
} 
```

现在您已经连接到了 auth 服务器，现在您可以开始监听来自乘客应用程序的请求。第一步是订阅一个私人频道。这个频道是所有乘客和司机订阅的。在这种情况下，它被司机用来监听乘车请求。它需要是一个私有通道，因为出于安全原因，[客户端事件](https://pusher.com/docs/client_api_guide/client_events#trigger-events)只能在私有和在线通道上触发。因为前缀的缘故，你知道这是一个私人频道。

```
this.available_drivers_channel = this.pusher.subscribe('private-available-drivers'); // subscribe to "available-drivers" channel 
```

接下来听一下`client-driver-request`事件。您知道这是一个客户端事件，因为前缀是`client-`。客户端事件不需要服务器干预就能工作，消息直接从客户端发送到客户端。这就是为什么我们需要一个认证服务器来确保所有试图连接的用户都是应用程序的真正用户。

回到代码，我们通过调用我们订阅的通道上的`bind`方法来监听客户端事件，并将事件的名称作为第一个参数传入。第二个参数是当这个事件从另一个客户端(从任何使用乘客应用程序请求乘车的人)触发时，您想要执行的功能。在下面的代码中，我们显示了一条警告消息，询问司机是否要接受乘客。请注意，该应用程序假设任何时候都只能有一名乘客。

```
// listen to the "driver-request" event
this.available_drivers_channel.bind('client-driver-request', (passenger_data) => {

  if(!this.state.has_passenger){ // if the driver has currently no passenger
    // alert the driver that they have a request
    Alert.alert(
      "You got a passenger!", // alert title
      "Pickup: " + passenger_data.pickup.name + "\nDrop off: " + passenger_data.dropoff.name, // alert body
      [
        {
          text: "Later bro", // text for rejecting the request
          onPress: () => {
            console.log('Cancel Pressed');
          },
          style: 'cancel'
        },
        {
          text: 'Gotcha!', // text for accepting the request
          onPress: () => {
            // next: add code for when driver accepts the request
          }  
        },
      ],
      { cancelable: false } // no cancel button
    );

  }

}); 
```

一旦司机同意搭载乘客，我们就订阅他们的私人频道。该频道仅用于驾驶员和乘客之间的交流，这就是为什么我们使用唯一的乘客用户名作为频道名称的一部分。

```
this.ride_channel = this.pusher.subscribe('private-ride-' + passenger_data.username); 
```

与`available-drivers`通道不同，在我们做任何事情之前，我们需要监听订阅何时真正成功(`pusher:subscription_succeeded`)。这是因为我们将立即触发一个发送给乘客的客户端事件。这个事件(`client-driver-response`)是一个握手事件，让乘客知道他们向其发送请求的司机仍然有空。如果乘客在那个时候仍然没有搭车，乘客应用程序会触发相同的事件，让司机知道他们仍然可以搭车。在这一点上，我们更新状态，以便用户界面相应地改变。

```
this.ride_channel.bind('pusher:subscription_succeeded', () => {
   // send a handshake event to the passenger
  this.ride_channel.trigger('client-driver-response', {
    response: 'yes' // yes, I'm available
  });

  // listen for the acknowledgement from the passenger
  this.ride_channel.bind('client-driver-response', (driver_response) => {

    if(driver_response.response == 'yes'){ // passenger says yes

      //passenger has no ride yet
      this.setState({
        has_passenger: true,
        passenger: {
          username: passenger_data.username,
          pickup: passenger_data.pickup,
          dropoff: passenger_data.dropoff
        }
      });

      // next: reverse-geocode the driver location to the actual name of the place

    }else{
      // alert that passenger already has a ride
      Alert.alert(
        "Too late bro!",
        "Another driver beat you to it.",
        [
          {
            text: 'Ok'
          },
        ],
        { cancelable: false }
      );
    }

  });

}); 
```

接下来，我们使用地理编码库来确定驱动程序当前所在位置的名称。在幕后，它使用 Google 地理编码 API，并且通常返回街道名称。一旦我们得到回应，我们就触发`found-driver`事件，让乘客知道应用程序已经为他们找到了司机。这包含驱动程序信息，如名称和当前位置。

```
Geocoder.getFromLatLng(this.state.region.latitude, this.state.region.longitude).then(
  (json) => {
    var address_component = json.results[0].address_components[0];

    // inform passenger that it has found a driver
    this.ride_channel.trigger('client-found-driver', { 
      driver: {
        name: 'John Smith'
      },
      location: { 
        name: address_component.long_name,
        latitude: this.state.region.latitude,
        longitude: this.state.region.longitude,
        accuracy: this.state.accuracy
      }
    });

  },
  (error) => {
    console.log('err geocoding: ', error);
  }
);  
// next: add componentDidMount code 
```

一旦组件被挂载，我们使用 [React Native 的地理位置 API](https://facebook.github.io/react-native/docs/geolocation.html) 来监视位置更新。每次位置改变时，传递给`watchPosition`函数的函数都会被执行。

```
componentDidMount() {
  this.watchId = navigator.geolocation.watchPosition(
    (position) => {

      var region = regionFrom(
        position.coords.latitude, 
        position.coords.longitude, 
        position.coords.accuracy
      );
      // update the UI
      this.setState({
        region: region,
        accuracy: position.coords.accuracy
      });

      if(this.state.has_passenger && this.state.passenger){
        // next: add code for sending driver's current location to passenger
      }
    },
    (error) => this.setState({ error: error.message }),
    { 
      enableHighAccuracy: true, // allows you to get the most accurate location
      timeout: 20000, // (milliseconds) in which the app has to wait for location before it throws an error
      maximumAge: 1000, // (milliseconds) if a previous location exists in the cache, how old for it to be considered acceptable 
      distanceFilter: 10 // (meters) how many meters the user has to move before a location update is triggered
    },
  );
} 
```

接下来，将驾驶员的当前位置发送给乘客。这将更新乘客应用程序上的 UI，以显示司机的当前位置。稍后，当我们继续编写乘客应用程序时，您将看到乘客应用程序如何绑定到该事件。

```
this.ride_channel.trigger('client-driver-location', { 
  latitude: position.coords.latitude,
  longitude: position.coords.longitude,
  accuracy: position.coords.accuracy
}); 
```

接下来，我们想通知乘客和司机他们已经彼此靠近了。为此，我们使用来自`helpers.js`文件的`getLatLonDiffInMeters`函数来确定乘客和司机之间的米数。因为司机在接受请求时已经收到了乘客的位置，所以只需要得到司机的当前位置，并将其传递给`getLanLonDiffInMeters`函数，就可以得到以米为单位的差值。从那里，我们简单地根据米数通知司机或乘客。稍后，您将看到乘客应用程序是如何接收这些事件的。

```
var diff_in_meter_pickup = getLatLonDiffInMeters(
  position.coords.latitude, position.coords.longitude, 
  this.state.passenger.pickup.latitude, this.state.passenger.pickup.longitude);

if(diff_in_meter_pickup <= 20){

  if(!this.state.has_ridden){
    // inform the passenger that the driver is very near
    this.ride_channel.trigger('client-driver-message', {
      type: 'near_pickup',
      title: 'Just a heads up',
      msg: 'Your driver is near, let your presence be known!'
    });

    /*
    we're going to go ahead and assume that the passenger has rode 
    the vehicle at this point
    */
    this.setState({
      has_ridden: true
    });
  }

}else if(diff_in_meter_pickup <= 50){

  if(!this.state.nearby_alert){
    this.setState({
      nearby_alert: true
    });
    /* 
    since the location updates every 10 meters, this alert will be triggered 
    at least five times unless we do this
    */
    Alert.alert(
      "Slow down",
      "Your passenger is just around the corner",
      [
        {
          text: 'Gotcha!'
        },
      ],
      { cancelable: false }
    );

  }

}

// next: add code for sending messages when near the destination 
```

在这一点上，我们假设司机已经搭载了乘客，他们现在正前往目的地。所以这次我们得到了当前位置和下车点之间的距离。一旦他们距离下车点 20 米，司机应用程序就会向乘客发送消息，告诉他们已经非常接近目的地了。一旦完成，我们假设乘客将在几秒钟内下车。因此，我们解除了正在收听的事件的绑定，并取消了乘客私人频道的订阅。这就有效的切断了司机和乘客 app 之间的联系。唯一保持打开的连接是`available-drivers`通道。

```
var diff_in_meter_dropoff = getLatLonDiffInMeters(
  position.coords.latitude, position.coords.longitude, 
  this.state.passenger.dropoff.latitude, this.state.passenger.dropoff.longitude);

if(diff_in_meter_dropoff <= 20){
  this.ride_channel.trigger('client-driver-message', {
    type: 'near_dropoff',
    title: "Brace yourself",
    msg: "You're very close to your destination. Please prepare your payment."
  });

  // unbind from passenger event
  this.ride_channel.unbind('client-driver-response');
  // unsubscribe from passenger channel 
  this.pusher.unsubscribe('private-ride-' + this.state.passenger.username);

  this.setState({
    passenger: null,
    has_passenger: false,
    has_ridden: false
  });

}

// next: add code for rendering the UI 
```

驾驶员应用程序的用户界面仅显示地图以及驾驶员和乘客的标记。

```
render() {
  return (
    <View style={styles.container}>
      {
        this.state.region && 
        <MapView
          style={styles.map}
          region={this.state.region}
        >
            <MapView.Marker
              coordinate={{
              latitude: this.state.region.latitude, 
              longitude: this.state.region.longitude}}
              title={"You're here"}
            />
            {
              this.state.passenger && !this.state.has_ridden && 
              <MapView.Marker
                coordinate={{
                latitude: this.state.passenger.pickup.latitude, 
                longitude: this.state.passenger.pickup.longitude}}
                title={"Your passenger is here"}
                pinColor={"#4CDB00"}
              />
            }
        </MapView>
      }
    </View>
  );
}
// next: add code when component unmounts 
```

在组件卸载之前，我们通过调用`clearWatch`方法来停止位置监视器:

```
componentWillUnmount() {
  navigator.geolocation.clearWatch(this.watchId);
} 
```

最后，添加样式:

```
const styles = StyleSheet.create({
  container: {
    ...StyleSheet.absoluteFillObject,
    justifyContent: 'flex-end',
    alignItems: 'center',
  },
  map: {
    ...StyleSheet.absoluteFillObject,
  },
}); 
```

## 创建乘客 App

乘客应用程序将与司机应用程序非常相似，所以我不再详细介绍相似的部分。继续创建新应用程序:

```
react-native init grabClone 
```

**安装依赖关系**

您还需要安装相同的库，外加几个库:

```
npm install --save pusher-js react-native-geocoding github:geordasche/react-native-google-place-picker react-native-loading-spinner-overlay react-native-maps 
```

另外两个库是 [Google Place Picker](https://github.com/q6112345/react-native-google-place-picker) 和 [Loading Spinner Overlay](https://github.com/joinspontaneous/react-native-loading-spinner-overlay) 。尽管我们使用了 Google Place Picker 的一个[分支](https://github.com/geordasche/react-native-google-place-picker)，因为 React 原生地图的兼容性问题在最初的回购中尚未解决。

因为我们已经安装了相同的库，所以您可以返回到我们做了一些额外配置以使库工作的部分。做完这些就回来。

接下来，Google Place Picker 还需要一些额外的配置才能工作。首先，打开`android/app/src/main/java/com/grabClone/MainApplication.java`文件，在最后一次导入后添加以下内容:

```
import com.reactlibrary.RNGooglePlacePickerPackage; 
```

在`getPackages()`功能下添加您刚刚导入的库。当你在那里的时候，也要确保`MapsPackage()`也被列出来。

```
protected List<ReactPackage> getPackages() {
  return Arrays.<ReactPackage>asList(
      new MainReactPackage(),
      new MapsPackage(),
      new RNGooglePlacePickerPackage() // <- add this
  );
} 
```

接下来，打开`android/settings.gradle`文件，将它们添加到`include ':app'`指令的正上方:

```
include ':react-native-google-place-picker'
project(':react-native-google-place-picker').projectDir = new File(rootProject.projectDir,         '../node_modules/react-native-google-place-picker/android') 
```

在那里，还要确保 React 原生贴图的资源也已添加:

```
include ':react-native-maps'
project(':react-native-maps').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-maps/lib/android') 
```

接下来，打开`android/app/build.gradle`文件并在`dependencies`下添加以下内容:

```
dependencies {
  compile project(':react-native-google-place-picker') // <- add this
} 
```

最后，确保 React 本机映射也已编译:

```
compile project(':react-native-maps') 
```

**乘客 App 编码**

打开`index.android.js`文件并添加以下内容:

```
import { AppRegistry } from 'react-native';
import App from './App';
AppRegistry.registerComponent('grabClone', () => App); 
```

就像驱动 app 一样，也是用`App.js`作为主要组件。继续导入库。它也使用相同的`helpers.js`文件，所以你也可以从驱动程序中复制它。

```
import React, { Component } from 'react';
import { StyleSheet, Text, View, Button, Alert } from 'react-native';

import Pusher from 'pusher-js/react-native';
import RNGooglePlacePicker from 'react-native-google-place-picker';
import Geocoder from 'react-native-geocoding';
import MapView from 'react-native-maps';
import Spinner from 'react-native-loading-spinner-overlay';

import { regionFrom, getLatLonDiffInMeters } from './helpers'; 

Geocoder.setApiKey('YOUR GOOGLE SERVER API KEY'); 
```

创建组件并声明默认状态:

```
export default class App extends Component {
  state = {
    location: null, // current location of the passenger
    error: null, // for storing errors
    has_ride: false, // whether the passenger already has a driver which accepted their request
    destination: null, // for storing the destination / dropoff info
    driver: null, // the driver info
    origin: null, // for storing the location where the passenger booked a ride
    is_searching: false, // if the app is currently searching for a driver
    has_ridden: false // if the passenger has already been picked up by the driver
  };

  // next: add constructor code
} 
```

为了简单起见，我们在构造函数中声明了乘客的用户名。我们还初始化推送通道:

```
constructor() {
  super();
  this.username = 'wernancheta'; // the unique username of the passenger
  this.available_drivers_channel = null; // the pusher channel where all drivers and passengers are subscribed to
  this.user_ride_channel = null; // the pusher channel exclusive to the passenger and driver in a given ride
  this.bookRide = this.bookRide.bind(this); // bind the function for booking a ride
}
// next: add bookRide() function 
```

当用户点击“图书乘车”按钮时，执行`bookRide()`功能。这将打开一个地点选择器，允许用户选择他们的目的地。一旦选定一个地点，应用程序会向所有司机发送乘车请求。正如您在前面的驱动程序中看到的，这将触发一个警告，在驱动程序中显示，询问驱动程序是否要接受请求。此时，加载程序将继续旋转，直到驱动程序接受请求。

```
bookRide() {

  RNGooglePlacePicker.show((response) => {
    if(response.didCancel){
      console.log('User cancelled GooglePlacePicker');
    }else if(response.error){
      console.log('GooglePlacePicker Error: ', response.error);
    }else{
      this.setState({
        is_searching: true, // show the loader
        destination: response // update the destination, this is used in the UI to display the name of the place
      });

      // the pickup location / origin
      let pickup_data = {
        name: this.state.origin.name,
        latitude: this.state.location.latitude,
        longitude: this.state.location.longitude
      };

      // the dropoff / destination
      let dropoff_data = {
        name: response.name,
        latitude: response.latitude,
        longitude: response.longitude
      };

      // send a ride request to all drivers
      this.available_drivers_channel.trigger('client-driver-request', {
        username: this.username,
        pickup: pickup_data,
        dropoff: dropoff_data
      });

    }
  });
}
// next: add _setCurrentLocation() function 
```

`_setCurrentLocation()`函数获取乘客的当前位置。注意，这里我们使用的是`getCurrentPosition()`,而不是之前在驱动程序中使用的`watchPosition()`。两者唯一不同的是`getCurrentPosition()`只获得一次位置。

```
_setCurrentLocation() {

  navigator.geolocation.getCurrentPosition(
    (position) => {
      var region = regionFrom(
        position.coords.latitude, 
        position.coords.longitude, 
        position.coords.accuracy
      );

      // get the name of the place by supplying the coordinates 
      Geocoder.getFromLatLng(position.coords.latitude, position.coords.longitude).then(
        (json) => {
          var address_component = json.results[0].address_components[0];

          this.setState({
            origin: { // the passenger's current location
              name: address_component.long_name, // the name of the place
              latitude: position.coords.latitude,
              longitude: position.coords.longitude
            },
            location: region, // location to be used for the Map
            destination: null, 
            has_ride: false, 
            has_ridden: false,
            driver: null    
          });

        },
        (error) => {
          console.log('err geocoding: ', error);
        }
      );

    },
    (error) => this.setState({ error: error.message }),
    { enableHighAccuracy: false, timeout: 10000, maximumAge: 3000 },
  );

}

// next: add componentDidMount() function 
```

当组件安装完成后，我们需要设置乘客的当前位置，连接到 auth 服务器并订阅两个通道:可用的司机通道和乘客的私有通道，后者仅用于与司机的通道进行通信，搭车请求被发送到该通道。

```
componentDidMount() {

  this._setCurrentLocation(); // set current location of the passenger
  // connect to the auth server
  var pusher = new Pusher('YOUR PUSHER API KEY', {
    authEndpoint: 'YOUR AUTH SERVER ENDPOINT',
    cluster: 'YOUR PUSHER CLUSTER',
    encrypted: true
  });

  // subscribe to the available drivers channel
  this.available_drivers_channel = pusher.subscribe('private-available-drivers');

  // subscribe to the passenger's private channel
  this.user_ride_channel = pusher.subscribe('private-ride-' + this.username);

  // next: add code for listening to handshake responses

} 
```

接下来，添加由驱动程序侦听握手响应的代码。当司机接受乘车请求时，这是从司机应用程序发送的。这允许我们确定乘客仍然在寻找搭车。如果乘客回答“是”，那么这是司机发送信息的唯一时间。

```
this.user_ride_channel.bind('client-driver-response', (data) => {
  let passenger_response = 'no';
  if(!this.state.has_ride){ // passenger is still looking for a ride
    passenger_response = 'yes';
  }

  // passenger responds to driver's response
  this.user_ride_channel.trigger('client-driver-response', {
    response: passenger_response
  });
});

// next: add listener for when a driver is found 
```

司机通过触发`client-found-driver`事件发送他们的信息。正如您在前面的驱动程序中看到的，它包含了驱动程序的名称以及它们当前的位置。

```
this.user_ride_channel.bind('client-found-driver', (data) => {
  // the driver's location info 
  let region = regionFrom(
    data.location.latitude,
    data.location.longitude,
    data.location.accuracy 
  );

  this.setState({
    has_ride: true, // passenger has already a ride
    is_searching: false, // stop the loading UI from spinning
    location: region, // display the driver's location in the map
    driver: { // the driver location details
      latitude: data.location.latitude,
      longitude: data.location.longitude,
      accuracy: data.location.accuracy
    }
  });

  // alert the passenger that a driver was found
  Alert.alert(
    "Orayt!",
    "We found you a driver. \nName: " + data.driver.name + "\nCurrent location: " + data.location.name,
    [
      {
        text: 'Sweet!'
      },
    ],
    { cancelable: false }
  );      
});
// next: add code for listening to driver's current location 
```

在这一点上，乘客现在可以从驾驶员那里听到位置变化。我们只需在每次触发该事件时更新用户界面:

```
this.user_ride_channel.bind('client-driver-location', (data) => {
  let region = regionFrom(
    data.latitude,
    data.longitude,
    data.accuracy
  );

  // update the Map to display the current location of the driver
  this.setState({
    location: region, // the driver's location
    driver: {
      latitude: data.latitude,
      longitude: data.longitude
    }
  });

}); 
```

接下来是在特定实例上触发的事件。它的主要目的是向乘客发送关于司机位置的更新(`near_pickup`)，以及当他们已经接近下车位置(`near_dropoff`)。

```
this.user_ride_channel.bind('client-driver-message', (data) => {
  if(data.type == 'near_pickup'){ // the driver is very near the pickup location
    // remove passenger marker since we assume that the passenger has rode the vehicle at this point
    this.setState({
      has_ridden: true 
    });
  }

  if(data.type == 'near_dropoff'){ // they're near the dropoff location
    this._setCurrentLocation(); // assume that the ride is over, so reset the UI to the current location of the passenger
  }

  // display the message sent from the driver app
  Alert.alert(
    data.title,
    data.msg,
    [
      {
        text: 'Aye sir!'
      },
    ],
    { cancelable: false }
  );        
});

// next: render the UI 
```

UI 由加载微调器(仅在应用程序搜索司机时可见)、标题、预订乘车的按钮、乘客位置(`origin`)及其目的地以及地图组成，地图最初显示用户的当前位置，然后在预订乘车后显示司机的当前位置。

```
render() {

  return (
    <View style={styles.container}>
      <Spinner 
          visible={this.state.is_searching} 
          textContent={"Looking for drivers..."} 
          textStyle={{color: '#FFF'}} />
      <View style={styles.header}>
        <Text style={styles.header_text}>GrabClone</Text>
      </View>
      {
        !this.state.has_ride && 
        <View style={styles.form_container}>
          <Button
            onPress={this.bookRide}
            title="Book a Ride"
            color="#103D50"
          />
        </View>
      }

      <View style={styles.map_container}>  
      {
        this.state.origin && this.state.destination &&
        <View style={styles.origin_destination}>
          <Text style={styles.label}>Origin: </Text>
          <Text style={styles.text}>{this.state.origin.name}</Text> 
          <Text style={styles.label}>Destination: </Text>
          <Text style={styles.text}>{this.state.destination.name}</Text>
        </View>  
      }
      {
        this.state.location &&
        <MapView
          style={styles.map}
          region={this.state.location}
        >
          {
            this.state.origin && !this.state.has_ridden &&
            <MapView.Marker
              coordinate={{
              latitude: this.state.origin.latitude, 
              longitude: this.state.origin.longitude}}
              title={"You're here"}
            />
          }

          {
            this.state.driver &&
            <MapView.Marker
              coordinate={{
              latitude: this.state.driver.latitude, 
              longitude: this.state.driver.longitude}}
              title={"Your driver is here"}
              pinColor={"#4CDB00"}
            />
          }
        </MapView>
      }
      </View>
    </View>
  );
} 
```

最后，添加样式:

```
const styles = StyleSheet.create({
  container: {
    ...StyleSheet.absoluteFillObject,
    justifyContent: 'flex-end'
  },
  form_container: {
    flex: 1,
    justifyContent: 'center',
    padding: 20
  },
  header: {
    padding: 20,
    backgroundColor: '#333',
  },
  header_text: {
    color: '#FFF',
    fontSize: 20,
    fontWeight: 'bold'
  },  
  origin_destination: {
    alignItems: 'center',
    padding: 10
  },
  label: {
    fontSize: 18
  },
  text: {
    fontSize: 18,
    fontWeight: 'bold',
  },
  map_container: {
    flex: 9
  },
  map: {
   flex: 1
  },
}); 
```

## 运行 App

现在，您已经准备好运行应用程序了。正如我在前面的**先决条件**部分提到的，你可能需要两台机器，每台机器运行一个应用程序。这将允许您为两者启用日志记录(`console.log`)。但是如果你只有一台机器，那么你必须按照特定的顺序运行它们:首先是乘客应用，然后是司机应用。

继续将您的 Android 设备连接到您的计算机，并运行以下命令:

```
react-native run-android 
```

这将在您的设备上编译、安装和运行应用程序。一旦它运行，终止观察和断开您的设备从计算机上。

[![compile app](img/db0fe50b32afb29b4b881165d1176c07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pxdhgc3b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500609946898_Screen%2BShot%2B2017-07-21%2Bat%2B12.04.31%2BPM.png)

接下来，打开 Genymotion 并启动您之前安装的设备。这一次，运行驱动程序。一旦应用程序运行，你会看到一个空白的屏幕。这是正常的，因为应用程序需要一个位置来渲染一些东西。你可以点击模拟器界面右上角的“GPS ”,然后启用 GPS。

[![enable GPS on Genymotion](img/402bb37c7aa7f33b51b342c434ea2446.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Itv7gR0l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500608355479_Screen%2BShot%2B2017-07-21%2Bat%2B7.35.46%2BAM.png)

如果需要，您也可以单击地图按钮并选择特定位置:

[![Change location](img/96f3eeedb0205f41b27cb0155cbf9473.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FWFcQRjL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_6E04D0395F3E9633E1AA8A9CFF9F8EA297AF4AA1392D072366B2CFB9DE43A3C7_1500608476706_Screen%2BShot%2B2017-07-21%2Bat%2B7.39.41%2BAM.png)

一旦您选择了一个位置，应用程序中的地图用户界面将显示您选择的相同位置。

接下来，您现在可以按照前面的**应用流程**部分的步骤进行操作。请注意，您可以通过在 Genymotion 地图 UI 周围单击来模拟移动的车辆。如果乘客已经预订了一次乘坐，并且司机已经接受了请求，那么它应该开始更新乘客应用和司机应用中司机的当前位置。

如果您使用两台机器，那么您可以简单地在两台机器上运行`react-native run-android`。一个应连接到您的设备，另一个应打开 Genymotion 模拟器。

## 结论

就是这样！在本教程中，你已经学习了如何使用 Pusher 来创建一个乘车预订应用程序。如您所见，您构建的应用程序非常简单。我们只坚持构建乘车预订应用程序中最重要的部分。如果你愿意，你可以给这个应用添加更多的功能，也许可以用在你自己的项目上。你可以在它的 [Github repo](https://github.com/anchetaWern/grabClone) 上找到这个应用使用的源代码。

最初发表在[推手博客](https://blog.pusher.com/creating-ride-booking-app-react-native-pusher/)上。