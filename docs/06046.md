# 制造中的思考

> 原文：<https://dev.to/quarterto/thinking-in-make-1826>

好的，但是*是一个 makefile 文件。实际上是什么？*

## Make 是为运行任务准备的

这里有一个简单的 makefile，尽可能的简单，而不是一个空文件:

```
do-a-thing:
    echo “did the thing” 
```

Enter fullscreen mode Exit fullscreen mode

将其保存为`makefile`，并在目录中运行`make do-a-thing` :

```
⟩ make do-a-thing
echo “did the thing”
did the thing 
```

Enter fullscreen mode Exit fullscreen mode

告诉 Make 运行什么，它就运行什么。类似于`do-a-thing:`的每一行都指定了一个*目标*，名字后面的缩进行(需要用实际的制表符缩进，对不起，空格爱好者)是为该目标运行的 shell 命令。

## Make 是用于生成文件的

当目标中的命令创建文件时，您应该让 Make 知道它。如果目标的名称与其创建的文件名称相同，Make 可以跟踪:

```
foo.txt:
    echo lorem ipsum > foo.txt 
```

Enter fullscreen mode Exit fullscreen mode

```
⟩ make foo.txt
echo lorem ipsum > foo.txt
⟩ make foo.txt
make: Nothing to be done for ‘foo.txt’ 
```

Enter fullscreen mode Exit fullscreen mode

等等什么？没什么可做的？当文件存在时，做好记录，并意识到它不需要做任何事情。如果它运行这些命令，也不会改变，所以它不需要运行它们。移除该文件，Make 将重新创建它。

```
⟩ rm foo.txt
⟩ make foo.txt
echo lorem ipsum > foo.txt 
```

Enter fullscreen mode Exit fullscreen mode

## Make 用于从其他文件生成文件

 *假设你正在生成一个文件，它依赖于另一个文件的内容。也许你只是在复制它:

```
foo.txt:
    cp bar.txt foo.txt 
```

Enter fullscreen mode Exit fullscreen mode

`make`在你更新`bar.txt` :
时不做任何事情

```
⟩ make foo.txt
cp bar.txt foo.txt
⟩ echo hello > bar.txt
⟩ make foo.txt
make: Nothing to be done for ‘foo.txt’ 
```

Enter fullscreen mode Exit fullscreen mode

这和上一个例子一样:文件存在，所以`make`不会再创建它。如果你想让`foo.txt`在`bar.txt`更新的时候更新，你需要告诉`make`这件事，把它标记为`foo.txt` :
的*先决条件*

```
foo.txt: bar.txt
    cp bar.txt foo.txt 
```

Enter fullscreen mode Exit fullscreen mode

目标名称中冒号后面的所有内容都是该目标的先决条件。它可以是文件名、另一个目标的名称，甚至是要生成的文件的另一个目标的名称。先决条件也可以被链接:作为先决条件的目标也可以有先决条件，依此类推。

再次运行这个例子会如你所愿:如果你更新`bar.txt`并再次运行`make foo.txt`，它就会被更新。如果`bar.txt`没有更新，`make`什么都不会做:

```
⟩ make foo.txt
cp bar.txt foo.txt
⟩ echo hello > bar.txt
⟩ make foo.txt
cp bar.txt foo.txt 
```

Enter fullscreen mode Exit fullscreen mode

## Make 可以从模式中计算出文件名

如果您已经有了一整个文件夹的目标和先决条件呢？你不想为每一个都写一个目标。我的意思是，这听起来很无聊，而且每当你添加或删除一个文件时，你都必须记得更新它。

因此，如果你的文件路径是可预测的，例如，如果你想表达类似“这是如何从*文件夹*中的同名文件创建*文件夹*中的任何文件”的意思，你可以使用*文件模式*。在目标和前提条件中，将需要填充的洞的位写为`%`，例如`target-files/%.txt: source-files/%.txt`。

如果我们只知道模式，那么如何引用实际的文件名呢？`make`定义了一些用于模式规则的*自动变量*，它根据模式和匹配的文件名进行设置。您需要知道的两个是`$@`，它是匹配的目标文件名，和`$<`，它是从目标模式、目标文件名和先决条件模式中推断出来的先决条件文件名，通过计算文件名中的什么替换了模式中的`%`(*词干*)。

给定我们上面的例子，当你运行`make target-files/foo.txt`，`make`将它与`target-files/%.txt`匹配并运行那个目标。它将`$@`设置为`target-files/foo.txt`，并且因为词干是`foo`(那是在模式中替换`%`的)，它将`$<`设置为`source-files/foo.txt`。

全部加在一起:

```
target-files/%.txt: source-files/%.txt
    cp $< $@ 
```

Enter fullscreen mode Exit fullscreen mode

```
⟩ make target-files/foo.txt
cp source-files/foo.txt target-files/foo.txt 
```

Enter fullscreen mode Exit fullscreen mode

## 不要告诉 Make 该做什么，让它自己找到路

好吧，如果你想什么都做呢？所有的文件？`make`实际上是一种成熟的(虽然有点神秘，但嘿，它已经有 40 年的历史了)编程语言，有一些内置功能可以用来生成你希望它构建的文件列表。

其中之一是`wildcard`函数。它接受一个类似 shell 的通配符表达式，并将其扩展为匹配的文件列表。假设你的`source-files`文件夹包含`foo.txt`、`bar.txt`和`baz.txt`，那么`$(wildcard source-files/*.txt)`展开为`source-files/foo.txt source-files/bar.txt source-files/baz.txt`。为什么模式用`%`的时候这个用`*`？因为原因。

然后就是`patsubst`。给它两个模式和一个文件列表，它匹配并**替换**替换**pat**terns。所以`$(patsubst source-files/%.txt, target-files/%.txt, source-files/foo.txt source-files/bar.txt)`评估为`target-files/foo.txt target-files/bar.txt`。

我在上面提到了自动模式变量。你可以定义自己的变量，语法看起来会很熟悉:`source-files = $(wildcard source-files/*.txt)`。使用`$(variable-name)`引用变量，例如`$(source-files)`，如果名字是单个字符，可以去掉`()`，像`$<`和`$@`(这没什么特别的；`make`有*非常宽松的变量语法，几乎只有*不能*使用的字符是`=`、`#`、`:`或空格。*

变量可以在先决条件和命令中使用。您将看到它们在命令中被用来向命令行程序传递长选项。当在先决条件中使用时，它们可以用来一次生成一堆文件。一个常见的用例是将每个文件放在一个文件夹中:

```
source-files = $(wildcard source-files/*.txt)
target-files = $(patsubst source-files/%.txt, target-files/%.txt, $(source-files))

all: $(target-files)

target-files/%.txt: source-files/%.txt
    cp $< $@ 
```

Enter fullscreen mode Exit fullscreen mode

```
⟩ make all
cp source-files/foo.txt target-files/foo.txt
cp source-files/bar.txt target-files/bar.txt
cp source-files/baz.txt target-files/baz.txt 
```

Enter fullscreen mode Exit fullscreen mode

从最后开始分解，这是我们之前看到的同一个目标，这使得任何`.txt`文件都在`target-files/`中。`all`目标没有命令，变量`$(target-files)`是先决条件。当目标没有命令时，它只是作为先决条件存在。`make`制造那些是为了制造`all`，然后不劳而获制造`all`。最后，通过使用`wildcard`获取`source-files`中的文件列表，并使用`patsubst`将该列表转换为`target-files`中的文件列表，从而生成`$(target-files)`。我一会儿会再谈这个。

好了，现在看看这个。我已经讲过了`make`如何只构建它需要的东西？当一次构建一堆文件时，*效果很好*:

```
⟩ make all
make: Nothing to be done for ‘all’
⟩ echo hello > source-files/baz.txt
⟩ make all
cp source-files/baz.txt target-files/baz.txt 
```

Enter fullscreen mode Exit fullscreen mode

更新`baz.txt`，不去管其他两个，那是`make`决定要建的唯一一个。在内部，它查看文件修改时间。当它发现一个目标文件依赖于一个先决条件文件，并且两者都存在时，它只在先决条件较新时才创建目标文件。

哦，如果你只是单独运行`make`，它会运行文件中的第一个(非模式)目标。按照惯例，人们称此为`all`。所以你可以用`make`而不是`make all`运行最后两个例子。

## 我实际上谈论文章标题的部分

`make`的意义是什么？到目前为止，我希望您会看到它不仅仅是一个简单的任务运行程序。如果您有一堆输入文件，并且想要将它们转换成一堆输出文件，那么您应该使用的工具是`make`。如果你的输入文件名看起来像你的输出文件名，看看你是否能写一个模式目标。

这些是我在编写 makefile 时经历的步骤:

1.  用具体的文件名写一个简单的规则
2.  用模式使规则通用化
3.  研究如何从输入文件名生成输出文件列表
4.  一次完成所有事情
5.  获得朋友和同事的赞赏

我还在第四步。

我将通过一个具体的例子:使用 Babel 构建 Javascript 源代码。首先，它是一个单独的文件，`src/index.js`，我想把它编译成`lib/index.js`。为了转换文件，我们运行`babel src/index.js -o lib/index.js`，因此整个规则看起来像:

```
lib/index.js: src/index.js
    babel src/index.js -o lib/index.js 
```

Enter fullscreen mode Exit fullscreen mode

现在我有了更多的`.js`文件，我想把它变成一个模式规则。输出和输入文件有一个非常清晰的`lib/%.js: src/%.js`模式，所以使用自动模式变量，我们得到:

```
lib/%.js: src/%.js
    babel $< -o $@ 
```

Enter fullscreen mode Exit fullscreen mode

为了一次构建所有的`.js`文件，我们需要一个由源文件生成的目标文件列表。所以得到源文件列表:

```
source-files = $(wildcard src/*js) 
```

Enter fullscreen mode Exit fullscreen mode

并转换输出文件名:

```
output-files = $(patsubst src/%.js, lib/%.js, $(source-files)) 
```

Enter fullscreen mode Exit fullscreen mode

那么我们可以把列表作为先决条件:

```
all: $(output-files) 
```

Enter fullscreen mode Exit fullscreen mode

所以我们最终的 makefile 看起来像:

```
source-files = $(wildcard src/*js)
output-files = $(patsubst src/%.js, lib/%.js, $(source-files))

all: $(output-files)

lib/%.js: src/%.js
    babel $< -o $@ 
```

Enter fullscreen mode Exit fullscreen mode

## 额外积分:自动重建文件

因为`make`不做任何它不需要做的事情，所以*真的真的真的*当你改变输入文件时，改造事情的基本方法是在 Bash 循环中运行它:

```
while true; do make
    sleep 1
done 
```

Enter fullscreen mode Exit fullscreen mode

当然，如果你不更新任何东西，你的终端将会永远被`make: Nothing to be done for ‘all’`包围。另一个选择是一个名为 [`watch-make`](https://github.com/quarterto/watch-make) 的套餐，由(*咳嗽着，一副英国式的假惺惺样子的害羞的*我。它包装`make`，从中获取先决条件文件，并在它们改变时重新运行`make`:

```
⟩ wmake

  ⛭ running make
  │ babel src/index.js -o lib/index.js
  ✔︎ make
  ⚲ watching 2 files

  ✏︎ changed src/index.js
  ⛭ running make
  │ babel src/index.js -o lib/index.js
  ✔︎ make
  ⚲ watching 2 files 
```

Enter fullscreen mode Exit fullscreen mode

## Make 可以做更多的事情

我在这里只是触及了表面，但是这比大多数 makefiles 在实践中使用的要多得多。如果你很想知道更多，那么 [GNU Make 文档](https://www.gnu.org/software/make/manual/html_node/index.html)是详尽的，写得非常好，但是当你忙于静态模式规则和`SECONDEXPANSION`时，不要怪我。

在你知道之前，你将会写 makefile 文件，然后没人能救你。

-好吧，好吧，好吧-好吧，好吧，好吧，好吧，好吧，好吧，好吧，好吧，好吧，好吧，好吧，好吧，好吧，好吧，好吧，好吧，好吧。

## auto tools/automake 怎么样？

号码*