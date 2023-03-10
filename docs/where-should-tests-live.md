# 测试应该放在哪里？

> 原文：<https://dev.to/rtfeldman/where-should-tests-live>

我最近在做 [elm-test](https://github.com/elm-community/elm-test/) 时遇到了一个设计问题:

如果你能在他们测试的代码所在的同一个文件中编写测试，会怎么样？

有几个测试系统支持这一点:

*   [`clojure.spec`](https://clojure.org/about/spec#_for_testing) (极度简化)[让您在与您的业务逻辑相同的文件中定义内联测试](http://stackoverflow.com/questions/37942495/where-to-put-specs-for-clojure-spec)。
*   Rust 允许将测试放在与被测试代码相同的文件中，尽管文档建议将它们放在一个单独的文件中(没有详细说明为什么)。
*   Doctests(比如在 [Python](https://docs.python.org/2/library/doctest.html) 、 [Elixir](http://elixir-lang.org/getting-started/mix-otp/docs-tests-and-with.html#doctests) 、 [Go](https://blog.golang.org/examples) 和 [Haskell](https://hackage.haskell.org/package/doctest) 中)允许在同一个文件中编写测试——理论上是为了文档的目的，尽管也可能是为了测试的目的。
*   Erlang 中的 [`EUnit`](http://erlang.org/doc/apps/eunit/chapter.html) 运行给定模块中所有以`_test`结尾的函数作为测试，包括业务逻辑模块。
*   [球拍](https://groups.google.com/forum/#!topic/racket-users/awkxA9_Ye5I)支持线上测试。
*   Pyret 支持“测试块”,它可以存在于任何源文件中。
*   [`Test::Inline`](http://search.cpan.org/~adamk/Test-Inline-2.213/lib/Test/Inline.pm) 给 Perl 增加了这方面的支持。

我从来没有使用过这些，但是我想知道在实践中会有怎样的体验。是不是很神奇？无趣？一个彻头彻尾的坏主意？与将测试放在单独的文件中相比，有什么优点和缺点？

如果您以前尝试过编写这样的测试，您会怎么想？