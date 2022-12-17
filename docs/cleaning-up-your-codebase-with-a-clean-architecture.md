# 用干净的架构清理你的代码库

> 原文：<https://dev.to/barryosull/cleaning-up-your-codebase-with-a-clean-architecture>

我们来谈谈软件架构。我们大多数人都知道 MVC，它几乎是所有 web 框架的基础。但是，随着产品的增长，MVC 的问题将开始出现。即使是一个相对简单的产品也可能以臃肿和混乱的代码库而告终。MVC 是我们开始的地方，但是当你需要超越它的时候你会怎么做？

在我们继续之前，让我们检查一下为什么我们在解释这个答案上有这么多困难。

以下是一个常见的对话(无论如何是针对开发人员的)

devA:“我们的代码库真的很乱，我们如何清理它？”devB:“我们需要重构它，将代码移入对象，分离关注点。”
天神:“好，太好了。我们如何做到这一点？”
devB:“我们将使用设计模式并遵循坚实的原则。”

这是我们通常停下来的地方，我们似乎认为这是一个足够好的答案，让人们开始。这就像你问一个木匠怎么做桌子，他只是指着他的工具说“用那些”。这个答案在技术上是正确的，但是它并没有告诉我们事情的全部，而且对于学习编写软件(或者制作表格)的人来说，它肯定是没有用的。*工具很重要，你需要知道它们是什么，但它们只是过程的一小部分。

## 学习模式是不够的

这是最令人恼火的部分，仅仅学习模式是不够的。事实上，你可能在学会它们后马上就处于一个更糟糕的境地，因为你突然有了电动工具，却不知道该用在哪里。

学习这些模式打开了一个全新的问题范围，在你能有效地使用它们之前，你必须回答这些问题。

*   我应该在哪里使用设计模式？
*   我如何决定使用哪一个？
*   我在哪里画出我的抽象线条？
*   什么时候应该使用接口？

没有一些关于如何回答这些问题的指导原则，开发人员最终会在对象之间创建随机的边界，尽可能地使用模式。这导致了不一致的代码，比没有这些“设计”时更糟糕。

[这里有一个关于这个真实问题的笑话示例](https://github.com/EnterpriseQualityCoding/FizzBuzzEnterpriseEdition)

难怪人们抱怨“设计”和“模式”，我们没有很好地解释如何有效地使用它们。

## 从哪里开始

对我来说，当我了解了清洁架构**的时候，一切都豁然开朗了。

一个干净的架构是拆分代码的指南，为某些概念的归属指明了方向。有许多口味，但它们都有相同的核心概念。

*   按层分离您的代码
*   将抽象放在内层
*   将实现细节放在外层
*   依赖只能指向内，不能指向外。

这里有一篇关于这个概念的很棒的文章，它比我能更好地理解它。

[https://8 thlight . com/blog/uncle-bob/2012/08/13/the-clean-architecture . html](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html)

很酷吧？如果你以前没看过，那真是大开眼界。

它给出了如何分离你的关注点和在哪里划定你的界限的指导方针。一旦划定界限，模式就变得明显了。这是把小麦和谷壳分开的好工具。

让我们看一个例子来理解我的意思。

## 举个例子

这里我们有一个简单的用例来设置用户的个人资料图片。这个类由一个控制器和一个控制台命令在内部使用，所以代码非常简洁。

```
<?php
namespace App\Usecases;

use Ramsey\Uuid\Uuid;
use SplFileInfo;
use Aws\S3\S3Client;
use League\Flysystem\AwsS3v3\AwsS3Adapter;
use League\Flysystem\Filesystem;
use Domain\User;

class SetProfileImage 
{
    private $filesystem;

    public function __construct()
    {
        $client = S3Client::factory([
                'credentials' => [
                    'key' => getenv('AWS_ACCESS_KEY'),
                    'secret' => getenv('AWS_SECRET'),
                ],
                'region' => getenv('AWS_REGION'),
                'version' => 'latest',
        ]);

        $adapter = new AwsS3Adapter($client, 'bucket-o-images');

        $this->filesystem = new Filesystem($adapter);
    }

    public function handle(Uuid $user_id, SplFileInfo $image): Uuid
    {
        $image_id = Uuid::uuid4();

        $filepath = "profle_image/$image_id.".$file->getExtension()
        $image_contents = $image->fread($image->getSize());
        $this->filesystem->write($filepath, $image_contents);

        $user = User::find($user_id->toString());
        $user->setProfileImage($image_id);
        User::where('id', $user_id->toString())->update($user->toArray());

        return $image_id;
    }
} 
```

以上有什么问题？嗯，尽管它是浓缩的性质，它实际上有五个概念混合在一起，搅浑了水。

*   我们的应用
*   配置
*   AWS S3
*   飞行系统
*   雄辩的 ORM

你必须理解这些概念中的每一个，才能理解这个类在内部试图做什么。为了理解这样一个简单的类，你需要记住的概念数量惊人。

更糟糕的是，它们并不一致。他们各自用自己的语言描述他们的解决方案，使用的细节和概念不能很好地融合。比如像`find`、`where`、`update`这样的 ORM 语言，在 Flysystem 语言中是不存在的。将应用程序概念融入其中，您就会明白为什么事情会变得令人困惑。

这就像试图阅读一本书，它在随机点上在法语、英语和俄语之间切换，有时从一个词到另一个词。当然，最终你可以读它，但是你会在过程中犯很多错误，到最后你会沮丧得一塌糊涂。所以让我们试着清理一下。

## 划分图层

首先，我们需要知道我们要从应用层移除什么，所以让我们以语言为指南。

因为我们希望这种语言是以应用为中心的，所以我们需要去掉以下概念中特有的单词。

*   配置
*   飞行系统
*   AWS S3
*   雄辩的 ORM

相反，我们将把它们替换为使用应用程序语言的集成点，而不是实现。

这就是设计模式的用武之地。通过观察你想做什么，而不是你如何去做，你可以发现正在出现的共同模式。

看代码，`image`和`user`分别是核心/领域概念，但是使用它们的一些代码是纯实现。这些是我们的集成点，所以让我们深入到我们真正在“图像”和“用户”上做什么。

在我们的例子中，很明显我们真的想做两件事

*   存储和检索用户
*   存储图像

其他的都是实现细节。存储和检索东西显然是存储库模式。因此，让我们创建两个新的应用程序概念，UserRepository 和 ImageRepository，并将它们实现为接口。

### 申请等级

```
<?php

namespace App\Usecases;

use Ramsey\Uuid\Uuid;
use SplFileInfo;

class SetProfileImage 
{
    private $image_repo;
    private $user_repo;

    public function __construct(ImageRepo $image_repo, UserRepository $user_repo)
    {
        $this->image_repo = $image_repo;
        $this->user_repo = $user_repo;
    }

    public function handle(Uuid $user_id, SplFileInfo $image): Uuid
    {
        $image_id = Uuid::uuid4();

        $this->image_repo->store($image_id, $image);

        $user = $this->user_repo->get($user_id);

        $user->setProfileImage($image_id);

        $user_repository->store($user);

        return $image_id;
    }
}

namespace App\Services;

use Ramsey\Uuid\Uuid;
use SplFileInfo;

interface ImageRepository 
{
    public function store(Uuid $image_id, SplFileInfo $image);
}

namespace App\Services;

use Uuid;
use Domain\User;

interface UserRepostiory
{
    public function get(Uuid $user_id): User

    public function store($user);
} 
```

这是应用程序级别的重组。我们已经将语言和概念精炼到应用程序需要的最低限度，表达了我们的意图。我们还创建了隐藏在旧实现中的两个新概念，并将它们归结为一个简单的模式，即存储库模式。

顺便说一句，我们在应用层添加设计模式语言没有问题，因为它是开发人员之间的共享语言，它有助于清晰而不是模糊。

现在让我们看看实现。

### 基础设施

```
<?php
namespace Infrastructure\App\Services;

use App\Services\ImageRepository;
use Ramsey\Uuid\Uuid;
use Aws\S3\S3Client;
use League\Flysystem\AwsS3v3\AwsS3Adapter;
use League\Flysystem\Filesystem;

class S3ImageRepository implements ImageRepository
{
    private $filesystem;

    public function __construct()
    {
        $client = S3Client::factory([
                'credentials' => [
                    'key' => getenv('AWS_ACCESS_KEY'),
                    'secret' => getenv('AWS_SECRET'),
                ],
                'region' => getenv('AWS_REGION'),
                'version' => 'latest',
        ]);

        $adapter = new AwsS3Adapter($client, 'bucket-o-images');

        $this->filesystem = new Filesystem($adapter);
    }

    public function store(Uuid $image_id, SplFileInfo $image)
    {
        $filepath = "profle_image/$image_id.".$file->getExtension()
        $image_contents = $image->fread($image->getSize());
        $this->filesystem->write($filepath, $image_contents);
    }
}

namespace Infrastructure\App\Services;

use App\Services\UserRepostiory;
use Ramsey\Uuid\Uuid;
use Domain\User;

class EloquentUserRepostiory implements UserRepostiory
{
    public function get(Uuid $user_id):User 
    {
        return User::find($user_id->toString());
    }

    public function store($user)
    {
        User::where('id', $user_id->toString())->update($user->toArray());
    }
} 
```

就这样，我们把用例的代码分成了两层。

## 为什么这样比较好？

### 易于阅读

从语言的角度来看，这要干净得多。它关注的是我们的应用程序想要做什么的语言，而不是它打算如何做。这种连贯的语言降低了理解的障碍。简而言之，为了理解它，需要解析的概念更少了。

### 测试

测试起来容易多了。原作有太多的变化，测试起来很困难。我们的验收测试必须连接到 S3 和数据库，以证明代码有效。这将把我们的测试与我们的实现结合起来，使得代码变得脆弱并且修改起来昂贵。

在新版本中，每一层都可以单独测试，对用例进行单元测试，对实现进行集成测试。这使得我们的验收测试更容易验证，我们只需要检查输出，而不是副作用。

### 我们正在有效地使用模式

在上文中，我们以对应用程序有意义的方式使用了存储库模式，而不是使用我们当时喜欢的任何模式。这使事情保持集中，并阻止我们疯狂的设计模式。在设计模式失败了这么久之后，有效地使用它们感觉真的很好。

### 联轴器松动

以上实际上是[依赖倒置原则](https://en.wikipedia.org/wiki/Dependency_inversion_principle)的一个[实](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)) ***例子。我们已经颠倒了代码的依赖性。应用程序不再依赖于数据库，而是数据库依赖于应用程序。简而言之，这意味着数据库中的更改将不再影响业务逻辑，因此更改它的风险更小。

### 改变实现

另一个好处是，切换实现非常容易。假设我们需要转换存储机制，比如从 MySQL 转换到 MongoDB。我们没有删除用例，而是用 MongoDB 库创建了一个新的接口实现。嘣，我们完了。在应用程序级别不需要任何更改。

这个例子强调了在编写分层代码时，语言作为向导是多么有用。新引入的 mongodb 语言并没有渗透到应用层的语言中，它保持不变，不需要任何改变。这是一个明确的信号，表明我们已经正确地分离了我们的层。

## 为什么会这样差？

说实话，并不全是阳光和玫瑰，所以我们来解决`class Room`中的`const ELEPHANT = true;`；
首先，代码库更大了，现在有更多的文件和更多的代码需要阅读。写的时间也比较长。你必须花时间将代码分层，花时间考虑你的代码的设计，所以这里有一个牺牲的元素。如果它是一次性软件，你只想出门，再也不想看到它，这可能会碍事。

我不认为以上是足够的理由不这样做。软件会发生变化，尤其是如果它是产品的核心，所以现在花额外的时间来节省以后的时间是值得的。

另外，你做得越多，你就会做得越好。最终，您会一次又一次地注意到相同的模式，并且您将能够在开始时创建抽象，直观地将您想要做的事情与您正在使用的工具分开。

## 我们能更进一步吗

综上所述，我们几乎在任何地方都在使用 Uuid 库。我们可以将它从代码中抽象出来，使用一个接口来代替，这样我们就有了一个在内部使用 Uuid 库的 ID 接口。

我们没有成功的原因很简单，不值得努力。清晰度的回报会太小。我们几乎在代码库的每一层都使用 Uuid，我们发现解释 Uuid 库的额外语言的开销非常小。因此，我们只是将它作为核心*****层的一部分，而不是将其视为实现细节。这意味着我们可以直接使用它的任何其他层。

## 什么时候做

以上提出了重要的一点，这种关注点分离的方法是一种在需要时使用的技术，有时你并不需要它。上面我们做了一个判断，什么要提取到它自己的层中，什么要成为核心的一部分。还有其他的指导方针，但实际上归结为清晰。

如果你发现一个层中的东西变得混乱，考虑将代码/概念移动到另一个层，或者如果需要的话创建这个层。

## 编写干净的代码

编写干净的代码很难，随意使用模式只会让事情变得更糟。这就是为什么我建议应用干净架构的思维模式，这是一种清理代码的简单技术。它帮助了我，我希望它能帮助你。

*我认为这是因为我们把所有的注意力都放在工具上，而不是过程上。每个人都忙于争论哪个凿子是最好的，他们开始认为工具就是设计软件的全部。

* *也称为六角形架构或洋葱架构。沃恩·弗农在他的书《实现领域驱动设计》中向我介绍了这个概念，从此我再也没有回头看

* * *史上最烂的双关语

* * * *唯一一个我没有重新访问过的软件就是那种没能产生价值的软件。如果它被使用，它将会改变。

* * * * *快速建议，“核心”应该只包括独立的代码，不应该包括任何连接到外部服务或有副作用的代码，如数据库代码。