# 编写干净的代码

> 原文：<https://dev.to/gonedark/writing-clean-code>

我最近开始了一份新工作。每一项新工作都会带来新的代码库。这大概是我第二十份工作了。所以我看过很多代码库。

不幸的是，它们都遭受着同样的基本问题- *不一致性*。可能是多年代码修补、大型团队、易手或以上所有情况的结果。

这就产生了一个问题，因为**我们读代码远远多于写代码**。当我阅读一个新的代码库时，这些不一致的地方分散了我对真正代码的注意力。我的重点转移到缩进和变量跟踪的平凡事物上，而不是重要的业务逻辑。

这些年来，我发现我[童子军](https://jason.pureconcepts.net/2015/01/are-you-a-boy-scout/)以同样的方式建立了一个新的代码库。我应用了三个简单的实践来清理代码并提高其可读性。

为了演示，我将把这些应用到我前几天刚读过的下面的真实代码中。

```
function check($scp, $uid){
  if (Auth::user()->hasRole('admin')){
    return true;
  }
  else {
  switch ($scp) {
    case 'public':
      return true;
      break;
    case 'private':
      if (Auth::user()->id === $uid)
        return true;
      break;
    default: return false;
  }
  return false;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 采用代码样式

我知道我是第 1647 个说“格式化你的代码”的人。但显然还是要说。我工作过的几乎所有代码库都没有采用一种代码风格。有了强大的 ide、预提交钩子和 CI 管道，一致地格式化代码库几乎不需要任何努力。

如果目标是提高代码的可读性，那么采用一种代码风格是唯一的、最好的方法。最后，你采用哪种代码风格并不重要。只是你要坚持不懈地运用它。一旦您或您的团队就代码风格达成一致，配置您的 IDE 或找到一个工具来自动格式化代码。

由于我们的代码是 PHP，我选择采用 [PSR-2 代码风格](http://www.php-fig.org/psr/psr-2/)。我在 [PHPCodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer) 中使用了 *PHP 代码美化器*来自动修复代码格式。

下面是采用代码风格后的相同代码。缩进让我们更容易看到代码的结构。

```
function check($scp, $uid)
{
    if (Auth::user()->hasRole('admin')) {
        return true;
    } else {
        switch ($scp) {
            case 'public':
                return true;
            break;
            case 'private':
                if (Auth::user()->id === $uid) {
                    return true;
                }
                break;
            default:
                return false;
        }
        return false;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 恰当地命名事物~~清楚地~~

是的，其他的你已经听过很多了。我知道给事物命名很难。困难的原因之一是命名事物没有明确的规则。这都是关于上下文的。并且代码中的上下文经常变化。

使用这些上下文来引出一个名字。一旦你找到了一个清晰的名字，就把它应用到所有的上下文中，把它们联系起来。这将创建一致性，并使跟踪代码库中的变量变得更加容易。

不要担心严格使用传统的命名约定。我经常发现代码库混合搭配。一个清晰的名字远比`snake_case` vs `camelCase`重要。只要在当前环境中始终如一地应用它。

如果你卡住了，用一个临时的名字，继续编码。我会经常给事物命名`$bob`或`$whatever`，以避免陷入*难题*。一旦我完成了其余部分的编码，我就返回并重命名这个变量。到那时，我有了更多的背景知识，并且经常能找到一个清晰的名字。

清晰的名称将有助于未来的读者更快地理解这些代码。他们不一定要*完美*。目标是为未来的读者增强信号。也许他们可以用他们所能承受的智力逐渐改进命名。

分析完这段代码后，我有了更多的上下文来选择更清晰的名称。应用清晰的名称不仅提高了可读性，还增强了上下文，使代码的意图更容易理解。

```
function canView($scope, $owner_id)
{
    if (Auth::user()->hasRole('admin')) {
        return true;
    } else {
        switch ($scope) {
            case 'public':
                return true;
            break;
            case 'private':
                if (Auth::user()->id === $owner_id) {
                    return true;
                }
                break;
            default:
                return false;
        }
        return false;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 避免嵌套代码

有一些关于嵌套代码的硬性规则。许多开发人员认为应该只允许一个嵌套层次。一般来说，我倾向于忽略带有硬数字的规则。他们觉得任意给定的代码是如此的易变。

更多的是嵌套代码往往是不必要的。我见过一个函数的整个主体被包装在一个`if`中。我见过几层巢。我真的见过空的`else`街区。通常添加保护子句、反转条件逻辑或利用`return`可以移除*需要*来嵌套代码。

在这种情况下，我将利用现有的`return`语句并翻转`switch`来从代码中移除大部分嵌套。

```
function canView($scope, $owner_id)
{
    if ($scope === 'public') {
        return true;
    }

    if (Auth::user()->hasRole('admin')) {
        return true;
    }

    if ($scope === 'private' && Auth::user()->id === $owner_id) {
        return true;
    }

    return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

说到底，编码就是写作。作为一名作者，你对你的读者负有责任。保持一致的风格、词汇和流程是确保可读性的最简单的方法。删除或更改这些并保持可读性，你不会。

想看看这些实践的效果吗？我正在主持一个一小时的免费研讨会，我将通过现场编码演示这些实践中的每一个，甚至更多。[报名](https://workshopsbyjmac.com)以确保你的位置。