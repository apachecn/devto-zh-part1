# 全新的 Cdecl 3.0

> 原文：<https://dev.to/pauljlucas/the-all-new-cdecl-30>

我仍然不确定语言声明的语法，在声明中，语法被用来模仿被声明变量的用法。这是招致强烈批评的事情之一，但它有一定的逻辑。
——丹尼斯·m·里奇

我认为 C 声明符语法是一个失败的实验。
——比雅尼·斯特劳斯特鲁普

* * *

cdecl(*see-**DEH**-kull*)是一个用于编写和解密 C(或 C++)类型声明或类型转换的程序，也称为“乱码”当然，你不经常需要这样的程序*，但是，当你需要的时候，它非常方便。它可以在终端上交互使用，或者接受来自命令行或标准输入的输入。一些例子(其中`cdecl>`是它的交互提示):*

```
cdecl> declare p as pointer to const pointer to const int
const int *const *p;

cdecl> explain int *const (*p)[4]
declare p as pointer to array 4 of const pointer to int 
```

我不记得我第一次使用 cdecl 是什么时候，但那可能是在我职业生涯的早期(那时我和 cdecl 都还年轻),大约 30 多年前。从那以后我偶尔会用它。

我最近一次使用是在 2017 年初。就在那时，我意识到 cdecl 真的显示出了它的年龄。(它最近一次重大更新是在 1996 年。)与此同时，C 和 C++语言已经发展了很多，cdecl 不支持任何新的关键字或语法。除此之外，cdecl 也有最少的错误报告(`syntax error`是*所有*你得到的，没有进一步解释*错误在哪里*或者为什么)。

就在那时，我决定获取一份源代码，并给它一些 TLC，使它保持最新，以支持 C 到 C11 和 C++到 C++14，并改进它的错误报告。结果是全新的 [cdecl 3.0](https://github.com/paul-j-lucas/cdecl) 。

# Cdecl 的原始源代码

cdecl 的源代码不多，只有三个文件:一个 lex 文件、一个 yacc 语法和一个单独的`.c`文件:

```
$ wc -l cdlex.l cdgram.y cdecl.c
      76 cdlex.l
     946 cdgram.y
    1321 cdecl.c
    2343 total 
```

最初的 cdecl 的工作方式是解析它的输入并合成它的输出(要么是 C 声明的 *declare* 要么是伪英语的 *explain* )，以正确的方式*将字符串片段连接在一起，同时*减少语法中的规则。这是一个最小且聪明的解决方案，值得称赞，但是很难遵循和修改。

# Cdecl 的新源代码

### 使用抽象语法树

新的 cdecl 现在将其输入解析为一个适当的数据结构，即一个抽象语法树 (AST)。从那里，其他代码不仅可以遍历树来生成 C 声明或伪英语，还可以执行语义检查。(最初的 cdecl 执行了一些*语义检查，但是代码被卷入了语法规则中。)*

AST 是伪英语形式，因为这样想最容易。因此，虽然从/到 AST 解析和生成伪英语很容易，但是解析和生成乱码要棘手得多，因为 AST 不能按一个顺序(前对后)或一个方向(下对上)遍历，特别是对于总是必须将大小或参数*放在最后*的数组和函数。经过*的大量*的黑客攻击，在`lldb`中花费时间，使用 cdecl 的`--debug`选项(以类似 JSON 的格式转储 AST)，以及 500 多个单元测试，它完成了。

### 好得多的错误信息

另一个主要改进是*更好的错误消息，例如:*

```
cdecl> declare f as pointer function (char) returning pointer to void
                            ^
21: syntax error: "function": "to" expected 
```

包括指向错误位置的`^`、列号(21)、什么标记导致了错误、什么标记是预期的，所有这些都用*颜色*(当然，可以通过`CDECL_COLORS`环境变量定制，类似于`gcc`的方式)。(注意，上面的颜色是代表 cdecl 使用的实际默认颜色的*而不是*。它们就是 Markdown 代码块解析器选择用于这个网站的任何东西。)

跟踪代币位置变得相当容易，因为[野牛](https://www.gnu.org/software/bison/)通过[位置](https://www.gnu.org/software/bison/manual/html_node/Tracking-Locations.html)直接支持它。AST 中的每个节点都有一个`YYLTYPE`数据成员(bison 的位置`struct`)。

### 更好的 C 和 C++语言支持

cdecl 年轻的时候，第一个 ANSI C (C89)和 C++都是新生事物。1988 年，[托尼·汉森](https://www.linkedin.com/in/tonyhansen/)添加了 C++支持(的预标准版本)。Cdecl 的语言支持从那以后就没怎么变过。Cdecl 3.0 区分了 K&R(ANSI 之前)C、C89、C95、C99、C11、C++98、C++03、C++11 和 C++14。

对哪些语言支持哪些标记的大部分语义检查都是表驱动的(这是 Tony 的贡献之一，我刚刚对其进行了扩展)。例如:

```
static c_type_info_t const C_QUALIFIER_INFO[] = {
  { T_ATOMIC,   L__ATOMIC,  LANG_MIN(C_11)                 },
  { T_CONST,    L_CONST,    LANG_MIN(C_89)                 },
  { T_RESTRICT, L_RESTRICT, LANG_MIN(C_89) & ~LANG_CPP_ALL },
  { T_VOLATILE, L_VOLATILE, LANG_MIN(C_89)                 },
}; 
```

指定具有给定标记文字(`L_`)的哪些限定符类型(`T_`)在给定语言中有效；并且:

```
static c_lang_t const OK_STORAGE_LANGS[][ ARRAY_SIZE( C_STORAGE_INFO ) ] = {
/*                   a  b  e  r  s  tl td   ce fi fr in mu nr o  v  pv */
/* auto         */ { __,__,__,__,__,__,__,  __,__,__,__,__,__,__,__,__ },
/* block        */ { __,__,__,__,__,__,__,  __,__,__,__,__,__,__,__,__ },
/* extern       */ { XX,__,__,__,__,__,__,  __,__,__,__,__,__,__,__,__ },
/* register     */ { XX,__,XX,__,__,__,__,  __,__,__,__,__,__,__,__,__ },
/* static       */ { XX,XX,XX,XX,__,__,__,  __,__,__,__,__,__,__,__,__ },
/* thread_local */ { XX,E1,E1,XX,E1,E1,__,  __,__,__,__,__,__,__,__,__ },
/* typedef      */ { XX,__,XX,XX,XX,XX,__,  __,__,__,__,__,__,__,__,__ },

/* constexpr    */ { P1,P1,P1,XX,P1,XX,XX,  P1,__,__,__,__,__,__,__,__ },
/* final        */ { XX,XX,XX,XX,XX,XX,XX,  XX,P1,__,__,__,__,__,__,__ },
/* friend       */ { XX,XX,XX,XX,XX,XX,XX,  P1,XX,PP,__,__,__,__,__,__ },
/* inline       */ { XX,XX,C9,XX,C9,XX,XX,  P1,P1,PP,C9,__,__,__,__,__ },
/* mutable      */ { XX,XX,XX,XX,XX,XX,XX,  XX,XX,XX,XX,P3,__,__,__,__ },
/* noreturn     */ { XX,XX,C1,XX,C1,XX,XX,  XX,XX,XX,C1,XX,C1,__,__,__ },
/* override     */ { XX,XX,XX,XX,XX,XX,XX,  XX,P1,XX,C1,XX,XX,P1,__,__ },
/* virtual      */ { XX,XX,XX,XX,XX,XX,XX,  XX,P1,XX,PP,XX,XX,P1,PP,__ },
/* pure virtual */ { XX,XX,XX,XX,XX,XX,XX,  XX,XX,XX,PP,XX,XX,P1,PP,PP },
}; 
```

指定哪些存储类在与哪种语言的其他存储类组合时有效(其中`__`是 all，`XX`是 none，`C1`是 C11，`C9`是 C99，`PP`是任何 C++，`P1`是 C++11 或更高版本，`P3`是 C++03 或更高版本，`E1`是 C11 或 C++11 或更高版本)。两个字符的缩写是宏(未显示),使表格适合 80 列。

其他一些语义检查是通过遍历 AST 来完成的，例如:当给函数参数列表时，检查`void`是否是唯一给定的*；或者检查`...`(varargs)*不能*是*唯一的*自变量并且*必须*在最后。(最初的 cdecl 没有捕捉到这些错误。)*

 *# 最后的想法

Cdecl 3.0 是一个有趣的黑客项目。我可能会时不时地继续黑它。新的源代码被大量评论——看看[吧](https://github.com/paul-j-lucas/cdecl)！

我想让 cdecl 3.0 进入软件(Linux，BSD 等)。)发行版，特别是为了替换旧版本。如果你想帮忙，请便。*