# 使用 Google 的 Firestore 和 AngularFire2 创建 Ionic 3/Angular 4 CRUD 应用程序

> 原文：<https://dev.to/techiediaries/create-ionic-3angular-4-crud-application-with-googles-firestore-and-angularfire2-3l9h>

在本教程中，我们将使用新的谷歌 Firestore 数据库，通过 Angularfire2 的 CRUD(创建、读取、更新和删除)方法创建一个简单的 Ionic 3 移动应用程序。我们将首先介绍 Firestore 数据库及其与旧 Firebase 实时数据库相比的功能，然后我们将搭建一个新的 Ionic 3 项目，设置我们的 Firebase 帐户并启用 Firestore 数据库，然后我们将把新数据库与我们的 Ionic 应用程序集成，并创建 CRUD 方法，这些方法可用于创建、读取、更新和删除数据库集合中的简单产品文档，让我们开始吧！

[![](img/5ba1414494db523133a137c55e846af3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RxL6aqiQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.techiediaries.cimg/content/ionic2.jpg)

谷歌的云 Firestore 是一个新的基于 NoSQL 的面向文档的数据库，可以与其他 firebase 基础设施工具一起使用，为您的移动和网络应用程序创建一个后端。

与基于 SQL 的数据库不同，NoSQL 云 Firestore 允许开发人员使用组织成集合的文档(键值对的容器)来存储您的数据，而不是表、行和列，或者 Firebase 实时数据库使用的 JSON 对象(形成一个平面 JSON 树)。

> 这意味着它是一个完全托管的产品，从头开始构建，可自动扩展。Cloud Firestore 是一个多区域复制数据库，可确保数据一旦提交，即使在遇到意外灾难时也能持久保存。不仅如此，尽管是一个分布式数据库，但它也具有很强的一致性，消除了棘手的边缘情况，使构建应用程序变得更容易，无论规模如何。[来源](https://firebase.googleblog.com/2017/10/introducing-cloud-firestore.html)

在本教程中，您将学习如何使用 *AngularFire 5* 通过从头构建一个示例 CRUD(创建、读取、更新和删除)移动应用程序，将 Google 的云 Firestore 集成到您的 Ionic 3 应用程序中。

## 云 Firestore 功能与 Firebase 实时数据库对比

谷歌的云 Firestore 在现有的 Firebase 实时数据库上提供了许多功能。当您使用各种 NoSQL 概念(如集合、子集合和可以存储键值对的文档)构建具有复杂数据库需求的应用程序时，这将使事情变得更加简单。

Cloud Firestore 是传统 Firebase 实时数据库的下一代，它提供了更强大的链式数据查询、实时同步、离线持久性支持(使用客户端 SDK)和自动扩展(得益于与谷歌云平台的集成)。

它还与现有的 firebase 基础设施和谷歌云平台进行了很好的集成。

## 第一步及要求

您需要创建或登录到您的 [Firebase 帐户](https://firebase.google.com/)，以启用新数据库，然后按照以下步骤操作:

转到 firebase 控制台，并创建一个新项目。

点击左侧的*数据库*，然后选择 Firestore 数据库。

接下来，您需要设置安全规则，以便在测试模式下启动。

在这个简单的演示中，我们将使用 Firebase 匿名认证和对数据库的公共访问，因此从 Firebase 控制台的左侧，选择*认证*，然后转到*登录*方法选项卡，然后启用匿名。

你也可以选择添加一个收藏到你的 Firestore 数据库。一旦你引用了一个不存在的集合，它也可以从你的代码中自动创建。

接下来，您需要设置一个开发环境来使用 Ionic。你可以开始开发最低要求的 Ionic 应用。首先**从官方[网站](https://nodejs.org/en/download/)安装 Node.js 和 npm** 如果它们还没有安装在你的机器上，然后**使用 npm 安装 Ionic CLI** 。

```
npm install -g @ionic/cli 
```

Enter fullscreen mode Exit fullscreen mode

您可能需要在该命令前添加 *sudo* 。

现在，您可以通过运行以下命令来创建一个新的 Ionic 3 应用程序:

```
ionic start ionic-firestore-crud blank 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个基于空白模板的 Ionic 应用程序。

接下来，简单来说，你需要遵循以下步骤:

*   首先导航到新创建的项目文件夹
*   接下来安装 npm 的 firebase JavaScript 库、 *angularfire2* 和 *promise-polyfill* (只有在将应用部署到 iOS 或 Android 设备时才需要 *promise-polyfill* ，以避免使用 firebase 时出现 promise 错误)
*   然后将与 Firestore 数据库交互的代码添加到组件中。

所以让我们从第一步开始。

## 整合云 Firestore 与 Ionic

我们将使用 *Angularfire2* 来集成 Cloud Firestore 和 Ionic 3，因为这是一个新产品，你需要安装最新版本的 Firebase JavaScript SDK 和 *AngularFire2* ，所以回到你的终端或命令提示符，运行以下命令:

```
npm install firebase@latest angularfire2@latest promise-polyfill --save 
```

Enter fullscreen mode Exit fullscreen mode

我们已经配置了新的 Firestore 数据库，并启用了匿名身份验证。现在我们需要将我们的凭证添加到 Ionic 3/Angular 应用程序中。因此，请点击 Firebase 控制台的*项目概述*菜单项，获取所需的凭证，然后点击*将 Firebase 添加到您的 web 应用*。

您应该能够看到，并且可以将您的数据库凭据复制到剪贴板。

现在回到您的 Ionic 项目，然后创建一个用于单独存储配置凭证的文件(您可以将其命名为:`config.ts`)，然后将您的凭证复制到这里:

```
touch config.ts 
```

Enter fullscreen mode Exit fullscreen mode

这是 credentails 对象应该看起来的样子，但是当然有你自己的值:

```
export const credentials = {
    firebase: {      
        apiKey: '<YOUR_API_KEY>',      
        authDomain: '<YOUR_AUTH_DOMAIN>',      
        databaseURL: '<YOUR_DATABASE_URL>',      
        projectId: '<YOUR_PROJECT_ID>',      
        storageBucket: '<YOUR_STORAGE_BUCKET>',      
        messagingSenderId: '<YOUR_MESSAGING_SENDER_ID>'    
    }  
}; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，你需要将 Firebase 和 Firestore 连接到你的 Ionic 3/Angular 主模块。你只需要简单地导入 *AngularFireModule* 、 *AngularFirestoreModule* 和 *AngularFireAuthModule* 模块，并将它们添加到 *imports* 数组中。还要确保从`config.js`导入*凭证*，然后将对象 *credentials.firebase* 传递给*AngularFireModule*:
的 *initializeApp()* 方法

```
/* ... */
import { AngularFireModule } from 'angularfire2';
import { AngularFirestoreModule } from 'angularfire2/firestore';
import { AngularFireAuthModule } from 'angularfire2/auth';
import { AngularFireDatabaseModule } from 'angularfire2/database';

import { credentails } from './config';

/* ... */ 

  imports: [
    /* ... */
    AngularFireModule.initializeApp(credentials.firebase),
    AngularFirestoreModule.enablePersistence(),
    AngularFireAuthModule,
    AngularFireDatabaseModule
  ],
  providers: [], 
```

Enter fullscreen mode Exit fullscreen mode

您可以选择调用 *AngularFirestoreModule* 模块上的 *enablePersistence()* 方法，该模块在 web 浏览器中启用离线支持，因此用户在离线时仍然可以访问应用程序。

现在，为了能够使用 Firestore，您需要将 *AngularFirestore* 服务注入到组件的构造器中。

```
import { AngularFirestore } from 'angularfire2/firestore';
import { AngularFirestoreCollection } from 'angularfire2/firestore';
import { AngularFireDatabase } from 'angularfire2/database';
import { AngularFireAuth } from 'angularfire2/auth';
import { Observable } from 'rxjs/Observable';

interface Product {
  name: string,  
  description: string,
  quantity: number
}

products: Observable<Product[]>;
productsCollectionRef: AngularFirestoreCollection<Product>;

constructor(public afAuth: AngularFireAuth, afs: AngularFirestore) {
    this.afAuth.auth.signInAnonymously();
    this.productsCollectionRef = this.afs.collection('products'); 
    this.products = this.productsCollectionRef.valueChanges();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们首先导入所需的依赖项，然后注入 *AngularFireAuth* 作为 *afAuth* 和 *AngularFirestore* 作为 *afs* 。在构造函数中，我们调用`this.afAuth.auth.signInAnonymously()`来使用 Firebase 匿名选项登录用户。

接下来，我们创建对保存有*产品*文档的*产品*集合的引用。请注意，创建对集合的引用实际上不会发送任何请求，因此您也可以创建对尚未创建的集合的引用(在这种情况下，将自动创建集合)。

接下来我们称之为*。集合引用上的 valueChanges()* 方法，该方法返回监听集合上任何更改的可观察对象。

当构建 CRUD 应用程序时，你将需要你正在检索的文档的*id*，这样你就可以更新和删除它们，这就是你需要使用的*。snapshotChanges()* 而不是*。大多数情况下是 valueChanges()* ，因为它返回文档的 id*和其他信息。*

### 检索或查询文档

现在让我们用类似于:
的东西来显示模板中的产品

```
<ion-list>
  <ion-list-header>
    Products
  </ion-list-header>
  <ion-item *ngFor="let product of products | async">
    <h1></h1>
    <p></p> 
    <p></p>
  </ion-item>
</ion-list> 
```

Enter fullscreen mode Exit fullscreen mode

**async** 是一个内置管道，允许你轻松订阅 Observables，而无需手动调用*。组件类中的 subscribe()* 方法。

因此，在模板中，我们订阅了可观察的*产品*，然后我们对结果进行循环，并使用`<ion-list>`显示名称、描述和数量。

### 创建或添加单据

您可以使用集合引用的`.add()`方法来添加一个新文档:向您的组件
添加一个方法 *createProduct()*

```
createProduct(name: string, description: string, quantity: number) {

    this.productsCollectionRef.add({ name: name, description: description, quantity: quantity });

} 
```

Enter fullscreen mode Exit fullscreen mode

### 更新文档

您也可以使用*进行更新。对由*返回的文档使用 update()* 方法。doc(id)* 方法，该方法采用您想要更新的文档的 *id* 。

```
updateProduct(product: Product) {
  this.productsCollectionRef.doc(product.id).update({ name: 'NEW_NAME', description : 'NEW_DESC' , quantity : product.quantity + 100 /*NEW QUANTITY*/ });
} 
```

Enter fullscreen mode Exit fullscreen mode

### 删除文档

删除文档和更新文档的工作原理是一样的，您只需通过调用*来获取想要删除的文档的引用。集合上的 doc(id)* 方法然后调用*。删除()*方法。

```
deleteProduct(product: Product) {
  this.productsCollectionRef.doc(product.id).delete();
} 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们已经创建了任何 CRUD 应用程序所需的四个方法。

## 结论

在本教程中，我们看到了如何使用 Ionic 3 和新的 Google 云 Firestore 数据库构建一个简单的 CRUD 应用程序。