# 使用 TDD 模式#1 在 Laravel 5.5 上构建高级博客准备环境和数据库

> 原文：<https://dev.to/kris/building-advance-blog-on-laravel-55-with-tdd-mode-1-prepare-environment-and-database-d98>

[![](img/f826998cf943ae123265be19a19cd0f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Lxl2tHQk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmKvFjSmRchikZ4NDki74Mw.png)

嘿，伙计们，这是本系列的第一篇文章，我将向你们展示如何建立一个有很酷的功能的博客，比如活动提要、过滤文章、文章订阅、提到用户、通知、Algolia 搜索等等

而浏览器没有必要多 80%的工作者在终端上以测试驱动的开发模式

这个系列我不得不使用 cloud9 web IDE，因为它很容易设置系统，让我们专注于开发

如果你需要尝试 cloud9 注册，我已经在我的大学账户中添加了这个表单，它提供了克隆我的项目的能力

[https://medium . com/media/ba e5e 45 a2 AE 7 ed 9997 b 9075 f 8 EFD BF 2 f/href](https://medium.com/media/bae5e45a2ae7ed9997b9075f8efdbf2f/href)>**重要提示:如果您需要我的帮助，请注册此表单，然后我会发送邀请至电子邮件**

如果你决定从头开始一个新项目，那么就去 c9.io/new

[![](img/b62315e41d7f8f730422ee4057af436e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--goQZH3nI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ao8tlTQCwYq3KF2OrK5o_-g.png)

填写如下图所示的表格，不要忘记选择团队，因为我很容易接受您的问题

然后创建一个工作空间

等一下，因为你有一个免费用户

[![](img/572a4297990a5f3b1b3bbdd59e082334.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QdEzO-jJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHtA8-G-QPvLHyuw6ZFhCYQ.png)

工作空间成功加载时。你已经看到这个窗口的左手边是文件管理器的大区域，是可以打开文件或浏览器的编辑区。底部是终端或命令行

不再需要你学习曲线。

让我们开始吧

### 升级 php 版本

目前 php 的版本是 5.5.9，但是 laravel 5.5 要求 PHP >= 7.0.0。所以我们需要先升级 php

[![](img/d90b1b5dcd56a3ec977baecaef89b88a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DhdMuO25--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/696/1%2Ak4He0fBfbir1l5bQ8Vxi8w.png)

复制下面的命令并粘贴到终端

```
sudo add-apt-repository ppa:ondrej/php -y ; sudo apt-get update -y ; sudo apt-get install php7.0-curl php7.0-cli php7.0-dev php7.0-gd php7.0-intl php7.0-mcrypt php7.0-json php7.0-mysql php7.0-opcache php7.0-bcmath php7.0-mbstring php7.0-soap php7.0-xml php7.0-zip -y ; sudo mv /etc/apache2/envvars /etc/apache2/envvars.bak ; sudo apt-get remove libapache2-mod-php5 -y ; sudo apt-get install libapache2-mod-php7.0 -y ; sudo cp /etc/apache2/envvars.bak /etc/apache2/envvars 
```

像这样等着…..

如果成功了，你又得到了 php -v

[![](img/e92b6de3d206761b2f3b17dad23fa328.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bvIDMZZE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/931/1%2AQW5KsEk9HwTa4I9m6kz5rQ.png)

### 安装 Laravel

这是 ubuntu docker image composer 安装就绪，然后您可以使用命令

```
composer create-project --prefer-dist laravel/laravel blog 
```

如果一切正常

[![](img/cc991bf4008771e8427c7ee25014be5f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0BP5ZU-y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AcpKpaWXv2Uae8g_wRh7kXw.png)

你已经看到了这个和这个

[![](img/322a81694419a35f282be1bb921d1f51.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7Bs4DWhT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/161/1%2A2HS8HG58Ns-gumQTyXPmPg.png)

然后我不喜欢在子文件夹上工作，所以我们把博客文件夹里的东西都移到根目录下

[![](img/c0f82e19ffbc8acf237263c59c937f07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q_t2GN4W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/191/1%2Ax56N3JiTjrTNs7_W_wodPQ.png)

看起来更舒服

### 准备数据库

让我们先说一下，如果我们在测试模式下，我不使用 mysql，我们在测试模式下不需要它，我们使用 Sqlite

#### 安装 Sqlite

```
sudo apt-get install php7.0-sqlite 
```

[![](img/e1c79fe7fcf3ff7ed4813ab5661c91eb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PZ7xvGbi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/789/1%2AtCynrBmfgTrSl6OcmIxs8A.png)

并使用 sqlite 类型进行测试

[![](img/0a4f8397ab22a6434b2a8ff1513fbc40.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jbJ6KvtS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/488/1%2AByR5mbvugvhlxzF0JokNrQ.png)

#### [T1。测试环境](#env-for%C2%A0testing)

接下来，我们将. env.example 重命名为. env.testing，然后打开并将 APP_ENV 从 local 更改为 testing，将 DB_CONNECTION 更改为 sqlite，最后移除红色突出显示

[![](img/3631b93b330b9c00bad99c48e2d84234.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t1va8TlH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/691/1%2AAam__d4k4FnzfFn-VAyg3Q.png)

接下来，我们测试这个文件是否使用了生成密钥

PHP artisan key:generate-env = testing 在参数中指定测试环境

[![](img/078f9d031d0d765b2c3ea375ec0b8064.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--er-UNtis--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/704/1%2ACpaXeOP-nPxRN-6ewRSiHQ.png)

仍然有效

[![](img/84e39d7964ec298fdc389d1059e3bbd7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GJx4vbK2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/698/1%2A9kiIICq0JTbBwxgSsPA3eg.png)

#### 创建 SQLite 数据库

默认情况下，在 config/database.php sqlite 配置中，使用数据库名 database.sqlite 并存储在数据库文件夹中，因此我们遵循以下规则

[![](img/b54bf2005746700f5dbdaa6ff6ef33b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hDgYR16M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AP3AxZdbqbve-jR-PzIVlAQ.png)

在终端类型中触摸 database/database.sqlite，然后文件立即出现

[![](img/c1d2d72d6a5415bf07449bdf9ce5b67a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J8qVBqPg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/188/1%2AVliErbiEs_lbPsVEsF9yNg.png)

然后

当我们开始创建博客时，我们需要这张表

*   用户
*   邮政
*   评论

关系很容易建立

*   用户有许多帖子和评论
*   帖子有很多评论

Laravel 已经有了用户模型和迁移，所以我们从后期模型开始

#### 发布模型并迁移

类型 php artisan make:model Post -m 用于创建模型和迁移

[![](img/4aae0bd2d76b9f157b308135005fb3ec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Oqv7wnC9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/512/1%2AZqaRxgtjthK3HHhcFVZ1yA.png)

文件夹迁移中仍会出现文件

[![](img/3671e5dca919ea2dd4d58bce3957b092.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VwC05Y-N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AqjK1phMElgOuGbyRhQ6R3A.png)

当我们查看应用程序文件夹时，我们会看到帖子模型

[![](img/f1df2cdd8573521d654d5011e16f1074.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WvETOJjd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/996/1%2AtjgiWg7P_4ca7JhMKu5JdQ.png)

接下来，我们创建一个迁移表

返回迁移文件

插入三列

```
$table->string('title',50);
            $table->integer('user_id');
            $table->text('body'); 
```

在这个位置

[![](img/2b31aedee8003865871c49e37c397b5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qvyD_TAU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/814/1%2ALOIVt5n3xfli980_ra3nVQ.png)

然后输入 php artisan migrate - env=testing

[![](img/6e028f2b41bb02a8b80abe7759bf6fd2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--02kh4a6s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/531/1%2A-bjU5nu77Hw8RCCWHNDnhA.png)

然后打开 SQLite shell SQLite 3 database . SQLite

。表格显示所有表格

[![](img/df3bc78283a8b138a90c03f2864fb1e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uyEWDuwU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/580/1%2AxFmiZL6D1YtEFRozUaEFmg.png)

现在我们已经发布了帖子和用户表

#### 注释模型、迁移和控制器

我们仍然缺少注释表，所以也要创建表、迁移和控制器。cd 到根目录并键入命令 php artisan make:model Comment -mc

[![](img/915fd3c3e288e44f85a3e5e9c6a1f893.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oYDozB-k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/626/1%2Ai06M_bdx0AOx5bND1iqUGg.png)

-mc 是告诉 artisan 生成迁移和控制器文件的可选参数

查找根目录，您会看到

[![](img/227c4e666bd628f9c92b9a14c1d22df0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8mR9RAB2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/372/1%2AY33Cg-MmGGNXTFgrAHipKw.png)

并再次打开迁移文件，我们插入三列

[![](img/048bc80a116278204ac295bbb6d77399.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--43OrWF0B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/740/1%2A6qJE7xNuVHNH4unKtfNCdw.png)

保存和 php artisan 迁移- env=testing

[![](img/4b10c25438c6948de1e0b7b699108788.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Li22FU3P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/660/1%2AgfWSjKtCzOrrmHGQrmRmAg.png)

再次在数据库中查找

[![](img/fe1181e32c42d4e9cf97c6739afdbbf5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vHL5xVaR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/503/1%2AKLIkFEkcC8gx_-pRBn1LBg.png)

让我们以工厂的种子数据结束

#### 种子数据与工厂

打开 UserFactory.php 并填充该功能

```
$factory->define(App\Post::class, function (Faker $faker) {

return [
        'user_id' => function(){
            return factory('App/User')->creat()->id;
        },
        'title' => $faker->sentence,
        'body' => $faker->paragraph
    ];
}); 
```

我们将 post 表列和 faker 方法绑定在一起，并将 user_id 与 user faker 绑定在一起

像这样

[![](img/12039d9781ff866c4238161be6b17ae0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9xcs27f8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AgYlCa4uNg54rfjr3rJUWiQ.png)

然后转到终端，键入 php artisan tinker - env=testing 并使用 factory('App\post '，50)->create()生成 50 行 Post 数据

[![](img/bfc0ab4e5d66b686035e20e4e3d9bca0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9mM_J4sQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AaRMRAZFaaZE6EDKS7nt8vQ.png)

现在我们看一张表来确定

<figure>[![](img/2024e775e498a8c6ead4464b4be67a7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hXnxzPuG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AFOiVOBM94y9JNWu7DYNe_Q.png) 

<figcaption>数据来自发文表</figcaption>

</figure>

因为这个命令也会生成用户数据

[![](img/b0974f213dcd172c5e30d6de5c778f09.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QHL5ZGdP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ayq8i1DFv7DBXC6mr_T0RpA.png)

接下来我们生成评论数据

再次打开**Userfactory.php**复制后工厂并粘贴

然后我们将**帖子**修改为**评论**，并将**标题**替换为 **post_id**

```
$factory->define(App\Comment::class, function (Faker $faker) {

return [
        'user_id' => function(){
            return factory('App\User')->create()->id;
        },
        'post_id' => function(){
            return factory('App\Post')->create()->id;
        },
        'body' => $faker->paragraph
    ];

}); 
```

像这样

[![](img/bb77fc76526c3fd980ff17ce9080f4fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pzUNZz0y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-wHiFK44cfzmMpvwhZiQ7Q.png)

现在我们需要在数据库中重新播种数据，因为当生成**注释**时，将会生成**用户**和**帖子**与 PHP artisan migrate:refresh-env = testing 并发

那是结果

[![](img/acc6d2a62dd79d289a74839672f3ce35.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oE7UWH0S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/744/1%2AQnwY8cqSC1nsSU1NIDqIdw.png)

好了再回到**匠匠**这个情况与之前的
不同

```
$post = factory('App\Post',50)->create() 
```

我们使用$post 从工厂分配数据

[![](img/a20e90e5bcf2a78dc32d70aedb367768.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--12-RQ1Df--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/846/1%2AKBRduymlPSorYI0RBk1nVA.png)

当输入时，它生成数据并分配给$post

接下来，我们使用$post 来生成评论数据

```
$post->each(function($post){ 
     factory(‘App\Comment’,10)->create([‘post_id’=>$post->id]);
 }); 
```

我们使用$post 循环，每篇文章生成 10 条评论，并添加外键

[![](img/ecac4436f61b315018dbc5df5e7b20d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9LWnn7ja--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2_tdswNV1xD8Y_-pEk_uWg.png)

好吧，那是工作

我们在第一章中完成

**如果你喜欢这个，请给我 50 个掌声，点击下面的鼓励继续前进**

**这个** [**项目 Github 资源库**](https://github.com/krissnawat/advance-blog-in-laravel-5.5-in-tdd) **你可以启动它**

> [下一集第二集准备第一次测试检查这里](https://medium.com/lordofdev/building-advance-blog-on-laravel-5-5-with-tdd-mode-2-prepare-the-first-test-80c4687cfca6)
> 
> * * *