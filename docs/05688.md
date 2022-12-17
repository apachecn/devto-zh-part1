# 使用 PHP-CS-Fixer 的 PHP 编码标准

> 原文：<https://dev.to/purplebooth/coding-standards-in-php-using-php-cs-fixer>

在项目早期，你能做的最好的事情之一就是定义一个编码标准。

这有许多好处:

*   标准内联文档
*   编辑其他代码时没有缩进冲突
*   减少了切换文件和代码块时的认知开销
*   减少讨论编码标准的时间
*   启用基于内联文档的代码静态分析

现在在 PHP 中，我们非常幸运地拥有一个为代码定义合理标准的组织，FIG(或框架互操作性组织)。这些以 [PSR-1](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md) 和 [PSR-2](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md) 的形式出现。这些标准相当松散，但是所有主要框架都使用它们，但是值得确保您选择的标准也是如此。

当选择一个编码标准时，如果你有一个经常使用的框架或库，那么你应该考虑有一个与之匹配的编码标准。通过这样做，你接触最多的“其他”代码。这有一个额外的好处，如果你有一个新的团队成员，他们会对他们以前见过的类似风格的代码感到很自在。

这也给了你一个直接的方法来平息关于 X 风格特征的争论，把责任推给你的风格库。

下一件要记住的重要事情是，如果你不能自动地将你的代码固定在你的标准上，没有人会遵循你的标准。如果你不同意代码的缩进或样式，这很容易被掩盖，如果它是在你提交之前自动修复的，但是如果你必须遍历每个实例并手动修复它们，人们会很生气。最终，不开心的开发人员不会做他们不想做的事情——尤其是像编码标准这样容易被忽略的事情。

为此，我推荐使用 php-cs-fixer 。这是一个 PHP 工具，通过自动修复代码库中的编码风格问题，帮助你修复 PHP 代码。

你可以用几种方式来运行它。首先只是独立使用别人的标准。

```
php-cs-fixer fix /path/to/project --rules=@Symfony 
```

会把你的项目固定在`Symfony` stanard。

你甚至可以添加额外的固定器

```
php-cs-fixer fix /path/to/project --rules=@Symfony,ordered_class_elements 
```

将您的项目修正为`Symfony`标准，并包含`ordered_class_elements`修正器，它对类中的元素进行排序。

让每个开发人员记住为一个项目传递什么标志可能会很棘手，所以您可以将它保存在一个带有您的标准的`.php_cs.dist`文件中。

这是我的标准的副本。

```
<?php

return PhpCsFixer\Config::create()
                        ->setRiskyAllowed(true)
                        ->setRules(
                            [
                                '@Symfony' => true,
                                '@Symfony:risky' => true,
                                '@PHP71Migration' => true,
                                'array_syntax' => ['syntax' => 'short'],
                                'dir_constant' => true,
                                'heredoc_to_nowdoc' => true,
                                'linebreak_after_opening_tag' => true,
                                'modernize_types_casting' => true,
                                'no_multiline_whitespace_before_semicolons' => true,
                                'no_unreachable_default_argument_value' => true,
                                'no_useless_else' => true,
                                'no_useless_return' => true,
                                'ordered_class_elements' => true,
                                'ordered_imports' => true,
                                'phpdoc_add_missing_param_annotation' => ['only_untyped' => false],
                                'phpdoc_order' => true,
                                'declare_strict_types' => true,
                                'doctrine_annotation_braces' => true,
                                'doctrine_annotation_indentation' => true,
                                'doctrine_annotation_spaces' => true,
                                'psr4' => true,
                                'no_php4_constructor' => true,
                                'no_short_echo_tag' => true,
                                'semicolon_after_instruction' => true,
                                'align_multiline_comment' => true,
                                'doctrine_annotation_array_assignment' => true,
                                'general_phpdoc_annotation_remove' => ['annotations' => ["author", "package"]],
                                'list_syntax' => ["syntax" => "short"],
                                'phpdoc_types_order' => ['null_adjustment'=> 'always_last'],
                                'single_line_comment_style' => true,
                            ]
                        )
                        ->setCacheFile(__DIR__.'/.php_cs.cache')
                        ->setFinder(
                            PhpCsFixer\Finder::create()
                                             ->in(__DIR__)
                        ); 
```

这份文件是以其他方式经手的。你也可以告诉`php-cs-fixer`跳过某些文件。

看到这条取景器线了吗

```
 ->setFinder(
                            PhpCsFixer\Finder::create()
                                             ->in(__DIR__)
                        ); 
```

您可以通过添加以下内容来忽略文件

```
 ->setFinder(
                            PhpCsFixer\Finder::create()
                                             ->in(__DIR__)
                                             ->notPath('/^app\/check.php/')
                                             ->notPath('/^web\/config.php/')

                        ); 
```

你可以在这里找到关于你能用 php-cs-fixer 做的所有事情的完整指南。但是希望这足以让你开始使用一个对所有 PHP 项目都非常有用的工具。