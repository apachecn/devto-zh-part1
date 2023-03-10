# TypeScript 或 Dart 中的 Angular 应用程序？

> 原文：<https://dev.to/jvarness/angular-apps-in-typescript-or-dart-9hi>

编辑:如果你不熟悉 TypeScript 语法，你可能会发现[这篇由](https://dev.to/abelagoi/learn-the-basis-of-typescript-to-start-writingangular-87i) [@abelagoi](https://dev.to/abelagoi) 撰写的文章很有帮助。如果你是 Dart 新手，我会试着写一些关于 Dart 的东西，因为[还没有其他人](https://dev.to/t/dart)写过它:

*Edit2: [飞镖初学者](https://dev.to/jvarness/dart-for-beginners-bc3)T3】*

在一场永无止境的斗争中，为了确定哪种语言编译成 JS 是最好的，谷歌提供了许多编写 Angular 应用程序的方法，这让一切变得更加混乱。编写 Angular 应用程序的两种主要方式是使用[打字稿](https://github.com/angular/angular)或[飞镖](https://github.com/dart-lang/angular)。

话虽如此...你应该用什么？让我们比较一下在[打字稿](https://angular.io/tutorial)和[飞镖](https://webdev.dartlang.org/angular/tutorial)中开发英雄之旅应用程序是什么样子的。

## 语言文字

[TypeScript](https://www.typescriptlang.org/) 是由微软构建和维护的 JavaScript 的类型化超集。你可以把它想象成 JavaScript，但是要有合适的类型。在 TypeScript 中，您可以定义接口、创建类，同时为访问您喜欢的 JavaScript 库提供一流的支持。你可以**在你的打字稿代码中写 JavaScript** ，它会**像预期的那样工作**。

这是非常重要的，因为如果你有一个喜欢的 NPM 库，你想继续使用，你可以用 TypeScript 写代码，就像用 JavaScript 写一样，而且**一切都很顺利**。

[Dart](https://www.dartlang.org/) 是由 Google 创建和维护的强类型语言(从 [Dart 2.0](https://www.dartlang.org/dart-2.0#strong-mode-and-static-typing) 开始需要)，具有第一手的[异步支持](https://www.dartlang.org/guides/language/language-tour#asynchrony-support)。它可以被编译成 JavaScript，但是有一些[的怪癖](https://www.dartlang.org/guides/language/language-tour#booleans)你需要注意，并且与 TypeScript 不同，对调用 JavaScript 库的支持是通过 Dart 内置的[互操作性](https://api.dartlang.org/stable/1.24.2/dart-js/dart-js-library.html)库实现的。

如果您有一个最喜欢的 NPM 库，并且希望在 Angular 应用程序中继续使用它，您可能会倾向于使用 TypeScript。我个人不会责怪你，因为 Dart 和 JavaScript 之间的互操作听起来不是特别好。

然而，Dart 语言与 [pub](https://pub.dartlang.org/) 打包在一起，它允许你从一个集中的 repo 下载可重用的包，就像 NPM 一样。pub 中的包被分类，以向您展示该库将与 web、Flutter 或服务器/命令行开发一起工作，因此如果您看到一个只适用于 [Flutter](https://flutter.io/) 的库，您可能不想使用它，也不能使用它(稍后我可能会发布另一个关于 Flutter 的帖子，但我们会看到)。

## 创建项目

Angular 的 Dart 版本和 Angular 的 TypeScript 版本都提供了命令行界面，创建新项目非常容易。

对于打字稿，使用 NPM:

```
$ npm install -g @angular/cli
$ ng new my-angular-project 
```

Enter fullscreen mode Exit fullscreen mode

对于 Dart，使用 pub:

```
$ pub global activate angular_cli
$ ngdart new my_angular_project 
```

Enter fullscreen mode Exit fullscreen mode

请注意，对于 Dart，项目名称使用下划线而不是连字符。命名 Dart 包或项目时使用下划线被认为是一种约定。

你可能注意到的另一件事是，如果你键入`ng --help` vs `ngdart --help` , `ng`有一个它能做的事情的清单，而`ngdart`有三个命令可用:`help`、`generate`和`new`。

当第一次创建项目时，您会注意到 TypeScript 和 Dart 项目之间有许多明显的不同。在 TypeScript 中，您会注意到有一个`src/`文件夹和一个`e2e/`文件夹，您的项目源代码和端到端测试分别存在于其中。在 Dart 中，您将拥有一个包含项目运行所需的少量 HTML 的`web/`文件夹，以及一个包含所有 Dart 源代码的`lib/`文件夹。对于 Dart 项目，将所有的 Dart 源代码放在`lib/`文件夹中是一种常见的做法。

要在 TypeScript 中启动项目，您可以键入:

```
$ ng serve 
```

Enter fullscreen mode Exit fullscreen mode

要在 Dart 中启动项目，您可以键入:

```
$ pub serve 
```

Enter fullscreen mode Exit fullscreen mode

## 编写代码

我已经在[打字稿](https://github.com/jvarness/angular-hello)和[飞镖](https://github.com/jvarness/jvarness_hello_ngdart)中完成了很多英雄之旅教程。

如果你浏览这两个代码库，你可能会注意到，在 TypeScript 中，Angular 支持[模块](https://github.com/jvarness/angular-hello/blob/master/src/app/app.module.ts)，这有助于在 Angular 项目中驱动大量的依赖注入/反转，而 Dart 没有。我个人并不觉得这太麻烦，因为在 Angular Dart 中可以通过使用指令和提供者来实现许多 DI 原则，但是 TypeScript 中的模块确实有助于保持代码简洁，并且有助于分离职责。你可能会注意到，在 Dart 代码库中，我的 [`AppComponent`](https://github.com/jvarness/jvarness_hello_ngdart/blob/master/lib/app_component.dart) 相当于上面链接的`AppModule`。它必须处理路由配置，并且知道一些不同的提供者和指令，否则它就不需要知道这些。

幸运的是，Angular Dart 团队正在努力将[依赖注入添加到 Angular Dart 的 5.0 版本中。同样，缺乏 DI 支持并不方便，但这并不妨碍您编写代码。](https://github.com/dart-lang/angular/projects/4)

您可能还会注意到，两个代码库之间的代码有一些惊人的相似之处。只看每个项目中的`HeroDetailComponent`，你可能会注意到[打字稿](https://github.com/jvarness/angular-hello/blob/master/src/app/hero-detail.component.ts)版本和[镖](https://github.com/jvarness/jvarness_hello_ngdart/blob/master/lib/src/hero_detail_component.dart)版本之间只有一些差异。在这一点上主要是语法上的差异。

例如，Dart 中该组件的构造函数如下:

```
HeroDetailComponent(this._heroService, this._routeParams, this._location); 
```

Enter fullscreen mode Exit fullscreen mode

而在 TypeScript 中，它看起来像:

```
constructor(
    private heroService : HeroService,
    private route: ActivatedRoute,
    private location: Location
  ) {} 
```

Enter fullscreen mode Exit fullscreen mode

这里的主要区别是 TypeScript 有关键字告诉你什么是 private。在 Dart 中，如果一个成员以`_`为前缀，那么它就是一个私有字段。

您可能注意到的另一件事是，关于方法，TypeScript 方法看起来非常简单:

```
 ngOnInit() : void {
    this.route.params
      .switchMap((params : Params) => this.heroService.getHero(+params['id']))
      .subscribe(hero => this.hero = hero);
  }

  goBack() : void {
    this.location.back();
  }

  saveHero(): void {
    this.heroService.updateHero(this.hero).then(() => this.goBack());
  } 
```

Enter fullscreen mode Exit fullscreen mode

每个方法都返回 void。在 Dart 中，因为组件中的所有方法都是异步的，所以 Dart 需要更多的语法:

```
 @override
  Future<Null> ngOnInit() async {
    int id = int.parse(_routeParams.get('id') ?? '', onError: (_) => null);
    if (id != null) hero = await _heroService.getHero(id);
  }

  void goBack() => _location.back();

  Future<Null> save() async {
    await _heroService.update(hero);
    goBack();
  } 
```

Enter fullscreen mode Exit fullscreen mode

在 Dart 中，当定义了异步方法时，不能返回`void`。相反，在 Dart 中定义`void`异步方法的方式是让它返回`Future<Null>`。这只是告诉消费者，虽然这个方法是一个异步方法，但它不返回任何东西。

## 应用观感

老实说，我无法告诉你这两者之间的区别。这两个应用程序的工作方式相同。

在打字稿中:

[![ts](img/969ec626c0f48dff3ccd6dafa99dedf3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--69XOdLd---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/27wpbk6mpzuks244um6z.png)

在 Dart 中:

[![dart](img/36510d57d125390e0d1a91f7b7b0c944.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MqeL-0Se--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x6lvcrg8i5h55903rhd1.png)

我知道这种差异令人震惊。

Dart 应用程序中的工具让我忘记了我在 TypeScript 中拥有的所有酷工具，除了我不能在我的服务中添加延迟来模拟真实环境。每当我尝试时，我总是得到一个异常，并放弃了让应用程序感觉真实的尝试。

编辑 3:我最终还是让它工作了。问题是我没有正确使用 [`Future.delayed`](https://api.dartlang.org/stable/1.24.2/dart-async/Future/Future.delayed.html) 。我只是把我的模拟英雄列表作为第二个参数放进去，而不是像他们在[教程](https://webdev.dartlang.org/angular/tutorial/toh-pt4#appendix-take-it-slow)中建议的那样使用闭包或函数。

在这两种情况下，我能够更改代码、保存和重新加载应用程序，我的更改会得到反映，而无需启动或停止服务器。我觉得我有足够的资源和库来完成我需要的东西(当然，这是一个教程，所有的库都已经开发了，但是你知道...).

## 整体

老实说，我真的看不出仅仅做教程有什么不同。我会说，如果你有很多你喜欢使用的现有 JS 库，并且想继续用于你的 Angular 应用程序，你应该使用 TypeScript。如果你想学习一些新的东西，并且你不喜欢 JavaScript，不要害怕用 Dart 编写你的应用程序。

我希望您喜欢这个使用 Dart 和 TypeScript 的角度开发概述。你做过 Dart 或者 TypeScript 的 app 吗？你喜欢哪一点？让我们知道！