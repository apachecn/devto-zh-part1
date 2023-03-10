# 如果你没有先使用 test，那你就错了

> 原文：<https://dev.to/nkdagility/you-are-doing-it-wrong-if-you-are-not-using-test-first-17af>

许多团队正在努力交付现代软件，因为他们没有按照测试优先的原则进行构建。测试优先向我们保证我们已经构建了正确的东西，我们所构建的是客户所要求的，并且当我们改变东西时，我们不会无意中破坏任何东西。

## TL；DR；

虽然实现测试优先需要时间、努力、奉献和纪律，但投资回报是巨大的。测试优先的一种常见形式是测试驱动开发(TDD ),我们可以使用它来满足客户的更多期望，最小化我们的维护成本，并减少生产中的回归和错误。最终，如果没有在测试优先的文化中工作，你将[无法自信地进行持续交付](https://nkdagility.com/continuous-deliver-sprint/)。

对于一个[专业开发团队](https://nkdagility.com/training/courses/professional-scrum-developer-training/)来说，唯一的问题是如何开始。

## 如果你没有先使用 test，那你就错了

如果你先在维基百科上查找 Test，你会被重定向到测试驱动开发(TDD)页面，我认为这是不正确的。虽然 TDD 是一种可以说是最有效的测试形式，但它绝不是全部。我能在完全没有自动化的情况下首先实现测试吗:是的。我能在完全没有自动化的情况下进行 TDD 吗:不能。你看到我的矛盾了吗…

> 如果你不先写测试就构建应用程序，那么你就做错了。您的过程将导致大量的返工，不太容易维护，也不太可能满足客户的需求。 <small>**苏格兰软件谚语**(随便编的，我是苏格兰人)</small>

不幸的是，虽然上面的谚语是绝对正确的，但有许多狂热分子不接受你可以在没有自动化的情况下进行测试优先。就像**过程战争**一样，**实践战争**正在我们周围展开，尽管我们想努力成为不可知论者，但成为无神论者是不可能的。

首先，您将会听到许多与测试相关的不同术语:

*   **测试驱动开发(TDD)**–在编写代码之前创建的自动化测试，以验证我们是否需要该代码。
*   **验收测试驱动开发(ATDD)**–验证业务功能的测试，无论是自动的还是手动的
*   **行为驱动开发(BDD)**–一种自动化测试，它验证您希望您的应用程序具有的特定行为。
*   等等…>所有这些话题，以及更多，都包含在由 Scrum.org 和微软联合打造的专业 Scrum 开发者(PSD)培训中，作为 Scrum & DevOps 的唯一官方团队培训。

这些术语都符合特定的生态位，现代软件开发的进化将会产生更多。找到能解决你具体问题的方法，并不断调整，直到你找到对你、你的团队和你的组织都有用的方法。

## 考验第一的本质

Test First 有两个主要目标，这两个目标同等重要。

第一个是专业地验证你已经建立的东西。软件开发是复杂的，当代码被执行时，很容易产生意想不到的结果。这不是拙劣编程的结果，而是任务复杂性的结果。在 19 世纪，管道工将烟泵入管道，以确保没有泄漏，因为如果对烟足够好，对水也足够好。这种心态导致了我们现在所称的软件开发中的“冒烟测试”,其实现的结果是生产中的错误。与软件开发相比，管道是简单的；现代软件比 10 年前的软件复杂许多倍。我们接受低质量的交付和昂贵的维护已经太久了。

第二个目标是缩短你的反馈循环。我们的工程师离问题产生的时间越近，他们就能越快发现问题，解决问题的成本也就越低。不幸的是，在大多数软件中不可能说出什么是“正确的”,开发者只是猜测。如果存在问题，质量保证(QA)或用户验收测试(UAT)就会发现问题，这种态度往好了说是不专业，往坏了说是无能。我们想让**知道**我们刚刚写的东西不仅满足了客户的期望，而且在我们能想到的任何情况下都准确地完成了我们希望它做的事情。

> ***测试优先让我们从简单地测试质量走向从一开始就构建质量*** <small>**杰夫·莱文森**，波音公司的建筑师</small>

从根本上说，解决最接近问题根源的问题要便宜得多。我们拖得越久，发现缺陷的代价就越大。在生产中发现的一个 bug 比在开发中发现的同一个 bug 的修复成本高 10 倍。

测试第一的三个优点:

1.  验证构建所要求的内容
2.  验证我们构建的内容是否如我们预期的那样工作
3.  验证变更没有破坏原始意图

理想情况下，我们希望我们的测试尽可能接近代码，但也尽可能容易被客户理解。理想情况下，我们所有的代码都是自动化的，我们有一个可执行的规范。这是一种平衡行为…

### 业务验证–我们已经完成了要求的内容

获得我们正在构建正确事物的确认是真正能够构建正确事物的关键。这听起来很简单，但是我们通常会怎么做呢？

好吧，我们通常把我们的需求，无论我们通常以什么形式制作它们，交给我们的编码员来转化成工作软件。我们分别给我们的测试人员相同的需求，他们创建一堆测试来验证我们所构建的。

你注意到这个工作流程的问题了吗？

> 最糟糕的情况是我们完全按照客户的要求来生产！T3】

如果我们不按照要衡量的标准来建造，我们怎么能建造出符合标准的东西呢？我们需要反过来，首先创建测试(度量)，然后编写代码使测试通过。既然我们已经消除了不可避免的耗时返工，我们可以利用这些时间为我们的客户创造更多的价值。

此外，我们更有可能满足客户的期望，因为这些测试为我们提供了更多的关注点。

### 开发人员验证–我们所构建的东西如我们预期的那样工作

现在是 2017 年，九十年代末的牛仔时代和早期的顽皮时代已经一去不复返了。随着[使用现代的源代码控制](https://dev.to/mrhinsh/getting-started-with-a-modern-source-control-system-and-devops-13og-temp-slug-4418693)，软件工程师再也不能躲在他们的管理层后面“不批准进行单元测试”，或者改变“单元测试”的含义以允许他们绕过需求。这是关于专业性的，所有的开发人员，不管他们在做什么，都应该验证他们所做的工作。

> 如果你没有做必要的尽职调查来验证你所创作的作品是否符合预期，并且没有持续这样做，你就不是专业人士。
> 
> –[专业的 Scrum 团队构建有效的软件](https://dev.to/mrhinsh/professional-scrum-teams-build-software-that-works-4aie-temp-slug-8239734)

这里有两个主要的增值。第一个是，当一个编码者创建功能时，它完全按照他的意图去做，我们有一个记录，和一个可执行的规范，关于这个意图是什么。第二个在后面。当我们以后添加功能时，我们知道我们何时破坏了现有的功能，这是这项工作最有价值的部分之一。

你能想象如果你能使用这个可执行的规范来验证所有未来的变化而不破坏你的应用程序，那将是多么令人惊奇的事情吗？

### 资源

这些资源应该可以帮助您开始构建更好的应用程序:

*   [敏捷经济学 Scrum 的价值和意义](http://www.scrum.org/About/All-Articles/articleType/ArticleView/articleId/644/Agile-Economics-The-Dollars-and-Sense-of-Scrum)(Scrum.org)
*   [采用测试优先开发](http://www.scrum.org/About/All-Articles/articleType/ArticleView/articleId/642/Adopting-Test-First-Development)(Scrum.org)
*   [由外向内的测试驱动开发](http://pluralsight.com/training/courses/TableOfContents?courseName=outside-in-tdd&highlight=mark-seemann_outside-in-tdd-m2-spiking*2!mark-seemann_outside-in-tdd-m1-walking-skeleton*7#outside-in-tdd-m2-spiking) (Pluralsight $)
*   [测试首次开发-第 1 部分](http://pluralsight.com/training/courses/TableOfContents?courseName=test-first-development-1&highlight=david-starr_tfd-01-m01-introduction*10,12,13!scott-allen_tfd-writing-unit-tests-i*5,7!david-starr_tfd-01-m06-isolation*11,2#tfd-01-m01-introduction) (Pluralsight $)

## 谁是裸体敏捷有限公司——马丁·辛舍尔伍德

马丁·辛舍尔伍德认为，每个公司都值得拥有高质量的软件，并定期交付，以满足客户的需求。他的目标是帮助您缩短周期时间，加快上市时间，并最大限度地减少实现目标过程中的组织摩擦。马丁是[naked Agility Limited](https://nkdagility.com)的创始人/CEO，同时也是[的专业 Scrum 培训师](https://www.scrum.org/martin-hinshelwood)和[微软 MVP: Visual Studio 和开发技术](https://mvp.microsoft.com/en-us/PublicProfile/4021815?fullName=Martin%20John%20Hinshelwood)。他从 2000 年开始交付软件，从 2010 年开始在 Visual Studio、Azure、Team Services 和 Scrum 的 DevOps &敏捷性方面提供咨询、指导和培训。你可以打 hello@nkdagility.com 的电话找到马丁。

帖子[你做错了，如果你没有使用测试优先](https://nkdagility.com/you-are-doing-it-wrong-if-you-are-not-using-test-first/)首先出现在[马丁辛舍尔伍德-裸体敏捷有限公司](https://nkdagility.com)上。