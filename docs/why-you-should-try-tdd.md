# 为什么你应该尝试 TDD

> 原文：<https://dev.to/dmerejkowsky/why-you-should-try-tdd>

*最初发表于[我的博客](https://dmerej.info/blog/post/why-you-should-try-tdd/)。*

TL；DR:你应该尝试使用测试驱动开发，因为它会把你变成一个更好的程序员。

不服气？我来详细说明一下。

# 简介

在整篇文章中，我将使用取自我目前从事的项目的代码: [tsrc](https://github.com/tankerapp/tsrc)

这是一个命令行工具，用于管理多个 git 存储库。

基本上，它从一个“清单”文件中读取信息，以知道要克隆或更新什么库，就像这样:

```
$  mkdir workspace
$  cd workspace
$  tsrc init <manifest-url> 
```

Enter fullscreen mode Exit fullscreen mode

清单 URL 是包含清单文件的存储库的 git url。

然后，当所有的存储库都被克隆后，您可以运行:

```
$  tsrc sync 
```

Enter fullscreen mode Exit fullscreen mode

更新所有存储库。

更准确地说，我将把重点放在我为`tsrc`添加组支持时编写的测试上。也就是说，当与`tsrc init -g foo <manifest-url>`一起使用时，只有在清单的`foo`组中定义的存储库才会被克隆。

# 强化技能

进行 TDD 时，有一个 3 步循环:

*   红色:写一个失败的测试
*   绿色:通过测试
*   重构:清理你刚刚制造的混乱

因为这三个步骤是独立的，并且有不同的目标，你可以用它们来练习一些特定的技能。

## 红色相

在红色阶段，你唯一关心的是如何编写一个失败的测试。

通常测试由三部分组成:

*   安排:初始化测试所需的资源或对象
*   表演:练习生产代码
*   断言:检查前一个动作的实际结果，并检查它们是否符合预期

同样，通过关注考试的每一部分，你将获得特定的技能。

### 安排和行动

要给`tsrc`添加组支持，我们首先需要一个系统来定义这样的元素组。

但是在完全不知道产品代码是什么样子的情况下，我们如何编写测试呢？

一种常见的技术是“从中间”开始编写测试。您编写您想要的代码(“act”部分)，然后向前向后工作，找出如何执行“arrange”和“assert”部分。

定义组的一个简单方法是用组名和元素集调用一个函数。(我们将使用集合，因为我们不想要重复的元素):

```
<something>("one", {"foo", "bar"})
<something>("two", {"spam", "eggs"} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们要检查每个组只包含存在的元素:

```
<something>.set_elements({"foo", "bar", "spam", "eggs", "other"})
<something>("one", {"foo", "bar"})
<something>("two", {"spam", "eggs"} 
```

Enter fullscreen mode Exit fullscreen mode

“安排”部分大部分已经完成，现在我们准备写断言:

```
<something>.set_elements({"foo", "bar", "spam", "eggs", "other"})
<something>("one", {"foo", "bar"})
<something>("two", {"spam", "eggs"}
<something>.get_elements(groups=["one", "two"]) == {"foo", "bar", "spam", "eggs"} 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候写真正的代码了，例如使用一个`GroupList`类:

```
group_list = GroupList(elements={"foo", "bar", "spam", "eggs", "other"})
group_list.add("one", {"foo", "bar"})
group_list.add("two", {"spam", "eggs"}
assert group_list.get_elements(groups=["one", "two"]) == {"foo", "bar", "spam", "eggs"} 
```

Enter fullscreen mode Exit fullscreen mode

注意生产代码的*架构*和*设计*是如何被测试“驱动”的。

还要注意，即使产品代码可能必须包含一个`Group`类，我们也不必让它成为公共 API 的一部分。

所以，通过练习 Act 和安排部分测试，你会做得更好，命名事物，设计好的和可测试的 API。

### 断言

专注于断言也会帮助你写出更好的测试。

在开始时，您可能会倾向于只使用测试框架提供的断言，但是随着时间的推移，您将学会如何编写自己的断言，以便测试代码更容易阅读。

我们举个例子。

当您运行`tsrc init -g foo`，`tsrc`将解析一个‘manifest’文件，寻找名为‘foo’的组，对于‘foo’组的每个项目，将寻找它的 URL 和路径，并在工作区内克隆它。

下面是这个测试的样子:

```
def test_init(tsrc_cli, git_server, workspace):
    git_server.add_group("foo", ["bar", "baz"])
    git_server.add_repo("other")
    manifest_url = git_server.manifest_url

    tsrc_cli.run("init", manifest_url, "--group", "foo")

    assert workspace.root_path.joinpath("bar").exists()
    assert not workspace.root_path.joinpath("other").exists() 
```

Enter fullscreen mode Exit fullscreen mode

我不会在这里解释`tsrc_cli`、`git_server`和`workspace`参数是如何工作的<sup id="fnref1">、 [1](#fn1) 、</sup>，我的观点是让你意识到如果这样写的话，测试会更具可读性:

```
 def repo_exists(workspace, repo_path):
    return workspace.root_path.joinpath(repo_path).exists()

def assert_cloned(workspace, repo_path):
    assert repo_exists(workspace, repo_path)

def assert_not_cloned(workspace, repo_path):
    assert not repo_exists(workspace, repo_path)

def test_init(tsrc_cli, git_server, workspace):
    git_server.add_group("foo", ["bar", "baz"])
    git_server.add_repo("other")
    manifest_url = git_server.manifest_url

    tsrc_cli.run("init", manifest_url)

    assert_cloned(workspace, "bar")
    assert_not_cloned(workspace, "other") 
```

Enter fullscreen mode Exit fullscreen mode

有人说:*你应该编写读起来像一个写得很好的规范的测试*。因此，通过练习测试中的“断言”部分，您将更好地用代码表达规范，这是一项非常有用的技能。

## 绿色阶段

所以第一阶段是关于命名事物，设计好的 API 和用代码表达需求。

这个阶段就是要把事情做完。

这里你唯一关心的是编写测试通过所需的最少的代码。

因此，实践这个阶段将有助于你保持事情简单，避免功能蠕变和过度设计。

这也会帮助你遵循 YAGNI 原则。

## 重构阶段

最后一个阶段是让代码*变得干净*。这里您唯一关心的是在不改变行为的情况下更改代码。

这里有很多技术需要学习，比如说*提取*类。

让我们看一个例子。

下面是`tsrc`处理清单的方式:

*   `tsrc init`将清单库克隆到一个隐藏的`<workspace>/.tsrc/manifest`目录中。
*   使用`sync`时，它:
    *   从克隆的路径运行`git fetch && git reset --hard origin/master`
    *   读取克隆路径中的`manifest.yml`以确保它是最新的。

这意味着，如果清单被更改为添加或删除存储库，`tsrc sync`将会注意到这一点。

这个实现是在一个`Workspace`类中完成的:

```
class Workspace:
    def __init__(self, root_path):
        self.root_path = root_path
        hidden_path = self.root_path.joinpath(".hidden")
        self.manifest_clone_path = hidden_path.joinpath("manifest")
        self.manifest = None

    def init_manifest(self, url,  branch="master", tag=None):
        """ Called by `tsrc init`: clone the manifest in self.manifest_clone_path """
        self._clone_manifest(url, branch, tag)

    def get_repos(self):
        """ Get the list of repos to work with """
        return self.manifest.get_repos() 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要增加团体支持。

注意，永久存储关于组的信息是必要的:在运行`tsrc init -g foo`之后，我们需要`tsrc sync`只更新`foo`组中的存储库。

所以我们更新工作区类:

```
 class Workspace:
    def __init__(self, root_path):
        self.root_path = root_path
        hidden_path = self.root_path.joinpath(".hidden")
        self.manifest_clone_path = hidden_path.joinpath("manifest")
        self.manifest_config_path = hidden_path.joinpath("manifest.yml")
        self.manifest = None

    def init_manifest(self, url,  branch="master", tag=None, groups=None):
        self._clone_manifest(url, branch, tag)
        self._save_manifest_config(url, branch, tag, groups)

    def _clone_manifest(self, url, branch, tag):
        ...

    def _save_manifest_config(self, url, branch, tag, groups):
        """ Dump the manifest config to self.manifest_config_path """
        ...

    def _load_manifest_config(self):
        """ Load the manifest config from self.manifest_config_path """
        ...

    def get_repos(self):
        config = self._load_manifest_config()
        groups = config.get("groups")
        return self.manifest.get_repos(groups=groups) 
```

Enter fullscreen mode Exit fullscreen mode

而这里有一个*码闻*。您会注意到单词“manifest”在 Workspace 类中随处可见。

代码告诉我们某个地方少了一个类。

事实上，看看我们如何引入一个新的“LocalManifest”类:

```
class LocalManifest:
    """ Represent a manifest that has been cloned locally inside the
    hidden <workspace>/.tsrc directory, along with its configuration

    """
    def __init__(self, workspace_path):
        hidden_path = workspace_path.joinpath(".tsrc")
        self.clone_path = hidden_path.joinpath("manifest")
        self.cfg_path = hidden_path.joinpath("manifest.yml")
        self.manifest = None

    def init(self, url, branch, tag, groups):
        ...
        self.clone_manifest(url, branch, tag)
        self.save_config(url, branch, tag, groups)

    def save_config(self):
        ...

    def load_config(self):
        ...

    def get_repos(self):
        config = self.load_config()
        groups = config.get("groups")
        return self.manifest.get_repos(groups)

class Workspace:

  def init(self, manifest_url, branch, tag, groups):
      self.local_manifest.init(manifest_url, branch, tag, groups)

  def get_repos(self):
      return self.local_manifest.get_repos()
  ... 
```

Enter fullscreen mode Exit fullscreen mode

实践这种重构在任何情况下都是有用的，即使你根本没有使用 TDD。

# 改变你的工作方式

做 TDD 不仅能提高你的技能，还能改变你的工作方式。

## 少了恐惧

通过实践 TDD，你将会编写和运行很多测试*。所以，如果你做得好，你会得到一套你可以信任的测试，当你重构或者添加新特性的时候，你会更有信心。*

 *## 花较少的时间调试

让我们看看根据您所处的阶段，调试是如何工作的:

*   红色阶段:仅仅通过编写新的测试来引入 bug*是不太可能的。但这确实发生了。例如，您的新测试可能会以一种不明显的方式改变全局状态，并触发其他测试失败。但是我不确定你怎么能在*生产代码*中引入一个错误。*
*   绿色阶段:因为你所关心的是通过测试，通常你会尽可能少地尝试*修改*生产代码，而不是尝试只*添加*新代码。如果您这样做，就不太可能会出现 bug。
*   重构阶段:这是最有可能出现 bug 的时候。毕竟，你是在*改变已经工作的代码*。但是，如果您有一个写得很好的测试套件，根据哪些测试失败以及如何失败，您应该对 bug 在哪里有一个很好的了解。

底线是:你实践 TDD 越多，你提高重构技能越多，你花费在调试上的时间就越少。

## 更快的上下文切换

我们都知道会议结束后很难马上回去工作。

如果你实践 TDD，并且总是在重构阶段结束时提交，当你回去工作时，你可以很快知道你在哪里:

*   没有 git diff:特性的最后一个 bug 修复已经完成，你可以开始新的工作了。
*   有一点不同:
    *   测试通过了:是时候重构了
    *   测试失败:是时候编写产品代码了

如果你愿意，你也可以在红色阶段的末尾做一个临时提交，比如‘为特性#3 添加失败的测试’,但是我不想让它成为公共 git 历史的一部分。(这意味着您应该用以下提交来挤压它)

## 做得更快

有时你会意识到你刚刚写的产品代码已经完成了。

让我给你看一个例子。

我们之前看到的`GroupList`必须进行修改，以允许组之间相互包含。

我已经编写了测试来确保如果这个组不存在的话，`get_elements()`方法会抛出:

```
# in test_groups.py def test_unknown_group():
    group_list = GroupList(elements={"a", "b", "c"})
    with pytest.raises(GroupNotFound):
        group_list.get_elements(groups=["no-such-group"])

# in groups.py class GroupError:
    pass

class GroupNotFound(GroupError):
    ...

class GroupList:
    ...
    def add(self, name, elements):
        group = Group(name, elements)
        self.groups[name] = group

    def get_elements(groups=None):
        if not groups:
            return self.all_elements
        res = set()
        for group_name in groups:
            if group_name not in self.groups:
                raise GroupNotFound(group_name)
            group = self.groups[group_name]
            for element in groups.elements:
                res.add(element) 
```

Enter fullscreen mode Exit fullscreen mode

然后为了实现组包含，我首先编写了一个失败的测试:

```
def test_includes():
    group_list = GroupList(elements={"a", "b", "c"})
    group_list.add("default", {"a", "b"})
    group_list.add("other", {"c"}, includes={"default"})
    actual = group_list.get_elements(groups={"other"})
    assert actual == {"a", "b", "c"} 
```

Enter fullscreen mode Exit fullscreen mode

然后我写了一个递归算法，提取了`_rec_get_elements`方法中的
组解析:
，通过了测试

```
class GroupList:

    ...
    def add(self, name, elements, includes=None):
        group = Group(name, elements, includes=includes)
        self.groups[group.name] = group

    def get_elements(groups=None):
        res = set()
        self._rec_get_elements(res, groups):

    def _rec_get_elements(self, res, groups):
        for group_name in groups:
            if group_name not in self.groups:
                raise GroupNotFound(group_name)
            group = self.groups[group_name]
            for element in groups.elements:
                res.add(element)
            self._rec_get_elements(res, group.includes) 
```

Enter fullscreen mode Exit fullscreen mode

之后我担心在`includes`中可能有不存在的组，所以我写了一个我认为会失败的测试:

```
def test_unknown_include():
    group_list = GroupList(elements={"a", "b", "c"})
    group_list.add("invalid", {"a"}, includes={"no-such-group"})
    with pytest.raises(GroupError):
        group_list.get_elements(groups=["invalid"]) 
```

Enter fullscreen mode Exit fullscreen mode

但是没有，测试已经通过了，因为递归方法已经抛出了`GroupNotFound`异常。

请注意，我仍然需要对代码进行一点点修补，以便`GroupNotFound`异常可以引用“父组”,但关键是代码的一般结构不需要做太多改变。

相信我，比你预期的更快“完成”是一种很好的感觉:)

# 一些建议

## 不放弃

精通 TDD 的唯一方法是通过练习。在你体验到我在前面的段落中谈到的所有美好事物之前，你将会花费相当长的时间，但是我确信它们是值得的。所以*请*别太早放弃。

## 谨防警告

我在我的博客上谈到了它们:[当 TDD 失败的时候](https://dmerej.info/blog/post/when-tdd-fails/)

## 避免经典初学者的错误

鲍勃大叔的博客上有一篇很好的文章:[放弃 TDD](http://blog.cleancoder.com/uncle-bob/2016/03/19/GivingUpOnTDD.html)

## 入门

在一个现有的大代码库中进行 TDD 是相当具有挑战性的，尤其是如果还没有一个好的测试套件已经写好的话。

即使是这种情况，您也需要花费相当多的时间来熟悉测试套件，以便您知道当某些测试失败时该做什么。

这里有两种更简单的开始方式:

*   当你从头开始写一个项目时(就像你的下一个微服务，如果你喜欢这个的话)
*   添加新的依赖项时。

从头开始一个新项目没什么好说的，除了机会不经常出现，但是我确实有关于依赖性的事情要说，所以开始吧:

## 添加新的依赖关系

这里有一个过程，当你试图基于一个新的依赖关系添加一个新的功能时，比如说一个新的库。

基本上，您编写一次性的测试代码来了解库是如何工作的，只有这样，您才能编写测试产品代码的新测试。

还是那句话，我们以`tsrc`为例。

我试图确保在清单文件不正确的情况下，我能够抛出非常详细的错误，例如:

```
# OK:
groups:
  default:
     repos: [a, b]
  other:
    includes: [default]

# BAD: includes should be a list
groups:
  default:
     repos: [a, b]
  other:
    repos: [c]
    includes: default

# BAD: repos key is missing:
groups:
  default: [a, b] 
```

Enter fullscreen mode Exit fullscreen mode

我了解了`schema`库，所以我写了一个测试来检查我是否理解库是如何工作的:

```
# in test_schema.py import schema

def test_invalid_schema():
    foo_cfg = { "foo": {"bar": 42 }
    foo_schema = schema.Schema(
        {"foo": {"bar": str}}
    )
    with pytest.raises(schema.SchemaError) as e:
        foo_schema.validate(foo_cfg)
    assert "42 should be instance of 'str'" in e.value.details 
```

Enter fullscreen mode Exit fullscreen mode

注意:这种测试还可以帮助您检查您的构建工具是否配置正确。

然后，我编写了一个新的测试，考虑如何将模式解析集成到现有的代码中，这里是通过向我的`parse_config_file`函数添加一个`schema`参数:

```
# in test_config.py def test_parse_config_invalid_schema():
    foo_yml = tmp_path.joinpath("foo.yml")
    foo_yml.write_text(textwrap.dedent(
        """
        foo:
            bar: 42
        """
    ))
    foo_schema = schema.Schema(
        {"foo": {"bar": str}}
    )
    with pytest.raises(tsrc.InvalidConfig) as e:
        tsrc.parse_config_file(foo_yml, schema=foo_schema)
    assert "42 should be instance of 'str'" in e.value.details

# in config.py from schema import SchemaError

def parse_config_file(path, schema=None):
    ...

    cfg = yaml_load(path)

    if schema:
        try:
           schema.validate(cfg)
        except ShemaError as schema_error:
            raise InvalidConfig(path, schema_error) 
```

Enter fullscreen mode Exit fullscreen mode

请注意`test_parse_config_invalid_schema`看起来非常像最初的`test_invalid_schema`，所以在这个功能完成后，我只需删除现在无用的`test_schema.py`文件。

顺便说一下，编写抛弃式测试的过程只是为了帮助您以后编写更好的测试，这是一种您可以在许多情况下应用的技术，添加新的依赖项只是这种技术的一个例子。

# 结论

这就是我今天要说的。改天见！

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。

* * *

1.  有 pytest 夹具，如果你需要了解 [↩](#fnref1)*