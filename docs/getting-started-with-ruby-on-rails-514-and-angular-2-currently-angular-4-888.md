# 开始使用 Ruby on Rails 5.1.4 和 Angular 2+(目前是 Angular 4)

> 原文：<https://dev.to/techiediaries/getting-started-with-ruby-on-rails-514-and-angular-2-currently-angular-4-888>

[![Getting Started with Ruby On Rails 5.x and Angular 2+ (currently Angular 4)](img/b9fdb8c063fb319df3feeb84635f3a5d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GmtBLPVV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.techiediaries.cimg/content/ruby-on-rails-5-angular-2.png)

## 简介

在整个教程中，我们将通过一个实际的 CRUD 例子来学习如何将 **Angular 2+** (目前是 **Angular 4** )集成到一个 **Ruby on Rails 5.x** (目前是 **Rails 5.1.4** ) web 应用程序中。

我们将一起逐步构建一个 **Angular 2+** 和一个 **Rails 5.x** backend CRUD(创建、读取、更新和删除)示例应用程序，该应用程序演示了集成客户端和服务器端这两个框架的基本概念。

本教程有一些您需要满足的基本要求，它们是:

*   我假设你已经熟悉了 Ruby on Rails 的框架。
*   我还假设你有一些 Angular 2+(目前 Angular 4)的工作经验。
*   您已经在本地开发机器上安装了 **Ruby** 语言(您可以使用 **RVM** 或 Ruby 版本管理器轻松地将其安装在您的机器上)。
*   您还在本地机器上安装了 **NodeJS** 平台。

除了这些需求之外，你已经准备好了，因为我们将涵盖你创建一个简单(或者不简单)的 web 应用程序所需的一切，在后端(或者服务器端)使用 **Ruby on Rails** ，在前端(或者客户端)使用 **Angular 2+** 。

这些是我们将在本教程中涉及的要点:

*   如何在 **Rails 5.1.4** 上安装 Ruby？
*   如何创建一个 **RoR 5.1.4** web 应用？
*   **Ruby on Rails 5.1.4** API 模式。
*   如何安装 **Angular 2+** CLI？
*   如何创建一个 **Angular 2+** 项目或 web app？
*   如何将 **Angular 2+** 集成到 **Ruby on Rails 5.1.4** web app 中？
*   **Angular 2+** 和 **Ruby on Rails 5.1.4** 如何通信？
*   在 **Angular 2+** 如何处理 CORS 问题以促进当地发展？
*   如何在 **Ruby on Rails 5.1.4** 中禁用本地开发的 CORS 头文件？
*   如何用 **Ruby on Rails 5** 构建 API 后端？
*   如何创建**角度 2+** 组件？
*   如何创建 **Angular 2+** 提供商或服务？
*   如何在 **Angular 2+** 中添加路由、数据绑定等特性？
*   如何与 **Angular 2+** 可观测量和承诺一起工作？
*   如何将 **Bootstrap 4** 与 **Angular 2+** 整合在一起，为 app 的 UI 设计风格？
*   如何使用表单？
*   如何使用 *ActivatedRoute* 获取**角度 2+** 中的路线参数？

这篇文章可以被看作是一篇**入门 Ruby on Rails 5 和 Angular 2+** 的教程，它说我们将只深入讨论一些基本概念，其他的观点只是为了给你一些入门的途径，同时也让你从枯燥的细节中解脱出来。

现在让我们从在 Rails 5.1.4 上安装 **Ruby 开始。**

## 在 Rails 上安装 Ruby 5 . 1 . 4

你可能会在安装 RoR 5.1.4 时发现一些问题，因为它是最新的，可能需要新版本的 Ruby 和一些更新的 gems，所以我将向你展示我在本地开发机器上安装 RoR 5.1.4 时出现一些错误后的完整过程。

第一步，你最好有一个更新版本的 RVM 或 Ruby 版本管理器，用来在同一台机器上安装不同版本的 Ruby。

要安装或更新 **RVM** 并获得最新版本的 Ruby，请运行以下命令:

```
curl -L https://get.rvm.io | bash -s stable --ruby 
```

“- ruby”标志将安装 ruby 的最新稳定版本。

一旦这个过程完成，你可以使用这些命令来验证 Ruby 和 **gem** 包管理器的安装版本。

```
ruby -v
gem -v 
```

现在你已经准备好在全局 gemset 上安装 **Ruby on Rais 5.x** 了，但更好的是，你还可以创建一个项目特定的 gemset，然后安装 **RoR 5** 。

```
$ rvm use ruby-2.4.1@rails5.1.4 --create 
```

然后使用 **gem** 管理器安装最新版本的 Rails。

```
$ gem install rails
$ rails -v 
```

恭喜你！您已经安装了最新稳定版本的 Ruby on Rails 。

## 创建 Ruby on Rails 5.x API Only App

在 MAC 或 Linux 上打开您的终端，或者在 Windows 系统上打开您的命令提示符，然后运行以下命令来创建一个新的仅支持 API 的 Rails web 应用程序，该应用程序使用 PosgtreSQL 数据库系统(或者如果您愿意，也可以使用 **MySQL** 数据库)

```
$ rails new ruby5-angular2-app --api -T -d postgresql 
```

完成后，在应用程序的根文件夹中导航，然后创建数据库

```
$ cd ruby5-angular2-app
$ rails db:create 
```

## 创建 Rails API 资源

让我们创建一些名为**产品**、**订单**和**客户**的资源:

```
$ rails generate scaffold product name:string
$ rails generate scaffold order reference:string
$ rails generate scaffold customer name:string 
```

然后迁移我们的数据库

```
$ rails db:migrate 
```

## 添加数据进行测试

让我们创建一个种子文件 **db/seeds.rb** 并将一些产品、订单和客户放入其中，这样我们就可以处理一些数据。

```
Product.create!([
  { name: 'Product 001' },
  { name: 'Product 002' },
  { name: 'Product 003' },
  { name: 'Product 004' }
])
Customer.create!([
  { name: 'Customer 001' },
  { name: 'Customer 002' },
  { name: 'Customer 003' },
  { name: 'Customer 004' }
])
Order.create!([
  { reference: 'ORD001' },
  { reference: 'ORD002' },
  { reference: 'ORD003' },
  { reference: 'ORD004' },
  { reference: 'ORD005' }
]) 
```

接下来运行以下命令来播种数据库

```
$ rails db:seed 
```

现在我们已经准备好启动 Rails 服务器了，所以继续在您的终端或命令提示符下运行以下命令:

```
$ rails server 
```

您应该能够使用您的 web 浏览器通过**[http://localhost:3000](http://localhost:3000)**导航到您新创建的 Rails 应用程序

您还可以使用 web 浏览器测试您的端点。

*   [产品 http://localhost:3000/products . JSON](http://localhost:3000/products.json)。
*   [http://localhost:3000/customers . JSON](http://localhost:3000/customers.json)面向客户。
*   [http://localhost:3000/orders . JSON](http://localhost:3000/orders.json)订单。

## 简介有角 2+(也有角 4)

**Angular** 是一个使用 **TypeScript** 构建 web 应用前端的框架，实际上你可以使用 **TypeScript** 、 **JavaScript** 或 **Google Dart** 但是官方和推荐的语言是 **TypeScript** 。 **TypeScript** 是 **JavaScript** 的超集，增加了经典的 OOP 概念(如 **Java** 、 **C++** 、 **SmallTalk** 等)。)和强类型，因为浏览器只理解普通的 **JavaScript** 所以我们需要 transpilers 来编译 **TypeScript** 源代码到 **JavaScript** 。

一个 **Angular 2+** (目前是 **Angular 4** )项目包含许多文件(配置+源文件)，并且有一个自以为是的目录结构(至少这个项目是用官方的 **Angular** CLI 生成的)，所以它需要有自己单独的目录，最好是在 Rails 的项目目录中。

## 安装角度 2+ CLI

通过 **Angular 2+** CLI，您可以快速创建一个 **Angular** 项目，而无需麻烦的 Webpack 和 TypeScript 配置。在你能使用它之前，它需要从 **npm** 安装

```
$ npm install -g @angular/cli 
```

同样，这需要您在本地开发机器上安装 **NodeJS** 和 **NPM** 。

## 生成一个有角度的 2+项目

接下来在您的工作文件夹中导航

```
$ cd ruby5-angular2-app 
```

然后生成一个新的**角度 2+** 应用

```
$ ng new frontend 
```

一旦完成，在你的应用内导航，然后运行 **ng serve** 用一个实时重载本地开发服务器在本地提供服务。

```
$ cd frontend
$ ng serve 
```

使用网络浏览器导航至**[http://localhost:4200](http://localhost:4200)**

就这样，你现在可以开始开发你的前端应用了。

## 添加自举 4

打开 **src/index.html** 然后从 CDN 添加 **Bootstrap 4** 文件

```
<head>
  <meta charset="utf-8">
  Frontend
  <base href="/">

  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">

</head>
<body>
  <app-root></app-root>
  <script src="https://code.jquery.com/jquery-3.1.1.min.js" integrity="sha256-hVVnYaiADRTO2PzUGmuLJr8BLUSjGIZsDYGmIJLv2b8=" crossorigin="anonymous"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>

</body> 
```

## 创建 Angular 2+提供商/服务

与远程 API 后端的直接交互将发生在**提供者**中，以鼓励关注点的分离，因此让我们创建一个与我们的 Rails 后端对话的提供者或服务，它可以通过 **DI** 或**依赖注入**注入到我们的组件中。

```
ng g service api 
```

接下来，您需要将其导入并添加到 **app.module.ts** 中的提供者列表中

```
/*Other imports*/
import { ApiService } from './api.service';
//
@NgModule({
  declarations: [
    AppComponent,
    ProductAddComponent,
    ProductListComponent,
    OrderAddComponent,
    CustomerAddComponent,
    OrderListComponent,
    CustomerListComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [ApiService],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

现在让我们添加与我们的 **Rails** 后端远程端点对话的代码。

在您喜欢的代码编辑器或 IDE 中打开 **src/app/api.service.ts** (我使用的是其他代码 IDE 中对 **TypeScript** 支持最好的 **Visual Studio 代码**，然后添加:

从 *@angular/common/http* 导入 *HttpClient*

```
import { HttpClient } from '@angular/common/http'; 
```

接下来声明一个变量来保存远程后端的地址

```
API_URL : string = "http://localhost:3000/"; 
```

接下来通过组件构造函数注入 *HttpClient* :

```
constructor(public http: HttpClient) { } 
```

最后添加 CRUD 方法:

```
// read method    
  public get(path) {

      var endpoint = this.API_URL + path;
      return this.http.get(endpoint);

  }

  // create method 
  public post(path:string,body:any) {

      var endpoint = this.API_URL + path;
      return this.http.post(endpoint,body);

  }
  // delete method    
  public delete(path:string){

    var endpoint = this.API_URL + path;
    return this.http.delete(endpoint);

  }  
  // update method    
  public update(path:string, body:any){
    var endpoint = this.API_URL + path;
    return this.http.put(endpoint,body);
  } 
```

现在有了这些方法，我们可以与我们的后端通信。

## 创建有角度的 2+组件

现在是时候创建 Angular 组件来创建和列出产品、订单和客户了。

使用 Angular CLI，您可以通过运行以下命令动态生成这些组件:

```
ng g component product-add
ng g component order-add
ng g component customer-add

ng g component product-list
ng g component order-list
ng g component customer-list 
```

## Rails 5.x 和 Angular 2 集成

关于如何将 Angular 2+与 Rails 集成，有很多不同的观点:*使用 Ruby on Rails 资产 pipline 来为 Angular 的资产(JavaScript 和 CSS bundles)提供 RoR 视图。为此，我们需要使用像 **SystemJS** 这样的构建系统，这是第一个与 Angular 一起使用的构建系统，但 Angular 团队不再推荐它了。*为前端(Angular 2+ SPA 或单页应用程序)和后端(仅 Ruby on Rails API 应用程序)创建完全独立的应用程序。

## 在 Ruby on Rails 5.1.4 中启用 CORS 头文件

如果您尝试从运行在不同端口上的 Angular 应用程序与 Rails 后端通信，您将得到**不允许访问错误**:

```
XMLHttpRequest cannot load http://localhost:3000/products?_page=1&_limit=10\. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://127.0.0.1:4200' is therefore not allowed access. The response had HTTP status code 500. 
```

所以我们需要代理请求或者在 **RoR 5** 应用中启用 **CORS** (跨源资源共享)。

按照以下步骤启用**轨道 5** 中的 CORS:

*   取消**gem 文件**中 **rack-cors** 的注释

## -运行捆绑包安装

将**config/initializer/CORS . Rb**更改为:

```
Rails.application.config.middleware.insert_before 0, Rack::Cors do
allow do
    origins '*'

    resource '*',
    headers: :any,
    expose: ['access-token', 'expiry', 'token-type', 'uid', 'client'],
    methods: [:get, :post, :put, :patch, :delete, :options, :head]
end
end 
```

## 添加路由

您首先需要将 *RouterModule* 导入并添加到 app 的模块声明中

```
import { RouterModule } from '@angular/router';
import { ProductAddComponent } from './product-add/product-add.component';
import { ProductListComponent } from './product-list/product-list.component';
import { OrderAddComponent } from './order-add/order-add.component';
import { CustomerAddComponent } from './customer-add/customer-add.component';
import { OrderListComponent } from './order-list/order-list.component';
import { CustomerListComponent } from './customer-list/customer-list.component';
/*Other imports*/

@NgModule({
  declarations: [
    AppComponent,
    ProductAddComponent,
    ProductListComponent,
    OrderAddComponent,
    CustomerAddComponent,
    OrderListComponent,
    CustomerListComponent
  ],    
  imports: [
    BrowserModule,
    RouterModule.forRoot([
      {
        path: 'products',
        component: ProductListComponent
      },
      {
        path: 'orders',
        component: OrderListComponent
      },
      {
        path: 'customers',
        component: CustomerListComponent
      },
      {
        path: 'products/add',
        component: ProductAddComponent
      },
      {
        path: 'products/add/:id',
        component: ProductAddComponent
      },          
      {
        path: 'orders/add',
        component: OrderAddComponent
      },
      {
        path: 'orders/add/:id',
        component: OrderAddComponent
      },          
      {
        path: 'customers/add',
        component: CustomerAddComponent
      },
      {
        path: 'customers/add/:id',
        component: CustomerAddComponent
      },          
    ]),
  ],
  providers: [ApiService],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

接下来，我们需要将路由器插座和链接添加到我们的主应用程序组件。所以打开**src/app/app . component . html**然后添加:

```
<div class="container">
  <div class="jumbotron">
    <h1>Demo</h1>
    <h2>Angular 2+ & Bootstrap 4 & Ruby on Rails 5.1.4 </h2>
  </div>
  <div class="panel panel-primary">
    <div class="panel-heading" style="background: greenyellow;">

        <a routerLink="/products">Products</a>
        <a routerLink="/orders">Orders</a>
        <a routerLink="/customers">Customers</a>
        <a routerLink="/products/add">Add Products</a>
        <a routerLink="/orders/add">Add Orders</a>
        <a routerLink="/customers/add">Add Customers</a>

    </div>
    <div class="panel-body">

        <router-outlet></router-outlet>

    </div>
  </div>
</div> 
```

## 添加型号

创建一个文件 **src/app/product.ts** ，然后添加以下代码。

```
export class Product{
    public id:number;
    public name:string;

} 
```

创建一个文件 **src/app/order.ts** ，然后添加以下代码。

```
export class Order{
    public id:number;
    public reference:string;

} 
```

创建一个文件 **src/app/customer.ts** ，然后添加以下代码。

```
export class Customer{
    public id:number;
    public name:string;

} 
```

## 在 Angular 2+中处理表单？

在使用 Angular 2+中的 *ngModel* 将输入绑定到组件的数据变量之前，您需要导入 **src/app/app.module.ts** 中的表单模块，并将其添加到*导入列表*

```
import { FormsModule } from '@angular/forms';
  /* ... */ 

  imports: [
    BrowserModule,
    FormsModule,
    HttpClientModule,
    /* ... */ 
```

## 在组件中实现 CRUD 方法

添加路由和 API 服务后，让我们在之前生成的组件中实现 CRUD 方法。

让我们从*产品列表组件*开始

首先打开**src/app/product-list/product-list . component . html**，然后添加一个 HTML 表格来列出产品:

```
<div class="panel panel-default" style="margin-top:10px;">
<div class="panel-heading">
    Products
</div>
<div class="panel-body" style="position:relative">
    <div class="table-responsive">
        <table class="table">
            <thead>
                <tr>
                    <th>Actions</th>
                    <th *ngFor="let column of columns">

                    </th>
                </tr>
            </thead>
            <tbody>

                <tr *ngFor="let row of rows">
                    <button (click)="delete(row['id'])">delete</button> ||
                    <button (click)="update(row['id'])">update</button>

                    <td *ngFor="let column of columns">

                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</div> 
```

接下来打开**src/app/product-list/product-list . component . ts**，然后执行以下步骤:

进口*路由器*、*蜂业*和*产品*

```
import { Router } from '@angular/router';
import { ApiService } from '../api.service';
import { Product } from '../product'; 
```

注入*路由器*和*养蜂*

```
constructor(public apiService: ApiService , public router : Router) {
} 
```

声明*列*和*行*变量，它们保存表格列的名称和产品数据

```
public columns = ['id','name'];
public rows : Array<Product>; 
```

添加代码以检索 *ngOnInit* 钩子中的产品

```
ngOnInit() {
    this.apiService.get("products").subscribe((data : Product[])=>{
    console.log(data);
    this.rows = data;
    });
} 
```

最后添加了*删除*和*更新*方法

```
public delete(id:string){

    console.log("delete : " + id);
    var path = 'products/' + id;
    this.apiService.delete(path).subscribe((r)=>{

    this.rows = this.rows.filter((p,i)=>{

        if(Number(id) === p.id ) 
        {
        return false;
        }
        return true;
    },this.rows)

    });

}
public update(id:string){
    console.log("update : " + id );
    this.router.navigateByUrl('/products/add/' + id);
} 
```

delete 方法向 RoR 5 后端发送一个 HTTP DELETE 请求，按 id 删除资源，然后过滤*行*数组，从数组中删除已删除的产品，而不刷新页面。

更新方法简单地导航到 **products/add/:id** 和对应的*行*的 *id* 。

现在让我们看一下 *ProductAddComponent* 的实现，如果将 *id* 传递给路由，它将用于添加新产品或更新现有产品。

打开**src/app/product-add/product-add . component . html**然后添加一个表单:

```
<form (ngSubmit)="onSubmit()">

        <div class="form-group">
            <label class="col-xs-4 control-label" for="">Product name: </label>
            <div class="col-xs-8">
                <input type="text" style="width: 300px" class="form-control" required
                    [(ngModel)]="product.name" name="productName">
            </div>
            <div>
            <input type="submit" value="Create" />
            </div>        
        </div>
    </form> 
```

接下来打开**src/app/product-add/product-add . component . ts**并遵循以下步骤:

*   进口*产品*、*蜂业*和*活动路线*

*ActivateRoute* 用于获取路线参数，在我们的例子中，我们获取产品的 *id* 进行更新。

```
import { Product } from '../product';
    import { ApiService } from '../api.service';
    import { ActivatedRoute } from '@angular/router'; 
```

通过构造器组件注入 *ApiService* 和 *ActivatedRoute*

```
constructor(public apiService: ApiService , public acRoute : ActivatedRoute) { } 
```

声明一个变量来保存产品

```
public product : Product = new Product(); 
```

当组件被初始化时，检查该路线是否有一个 *id* 参数，如果有，发送一个请求以获取具有该 *id* 的产品

```
ngOnInit() {

    this.acRoute.params.subscribe((data : any)=>{
    console.log(data.id);
    if(data && data.id){
        this.apiService.get("products/"+data.id).subscribe((data : Product)=>{
        this.product = data;
        });
    }
    else
    {
        this.product = new Product();
    }
    })
} 
```

最后创建 *onSubmit()* 方法

```
public onSubmit(){
    console.log("Adding a product: " + this.product.name);
    if(this.product.id){
    this.apiService.update("products/"+this.product.id,this.product).subscribe((r)=>{
        console.log(r);
        alert("Product updated !");
    })
    }
    else
    this.apiService.post("products",this.product).subscribe((r)=>{
    console.log(r);
    this.product = new Product();
    alert("Product added !");

    });
} 
```

这个方法检查产品是否有 id，如果有，它发送一个 PUT 请求来更新它，如果没有，它发送一个 POST 请求来创建它。

就这样，您可以对其他组件进行同样的操作，这留给读者作为练习。

## 结论

在本教程中，我们已经看到了如何在后端使用 **Ruby on Rails 5.1.4** 和在前端使用 **Angular 2+** (目前是 **Angular 4** )构建一个简单的示例 CRUD 应用程序。在本系列的下一篇教程中，我们将看到如何在我们的 web 应用程序中实现 JWT 认证。如果你有任何与本教程相关的问题，请在下面留言，我会非常乐意帮助你。感谢阅读，并在下一个教程中看到你的。