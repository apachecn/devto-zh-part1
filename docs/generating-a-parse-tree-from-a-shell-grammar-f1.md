# 从外壳语法生成解析树

> 原文：<https://dev.to/oyagci/generating-a-parse-tree-from-a-shell-grammar-f1>

作为一名 42 岁的学生，你必须自己完成的第一个项目是重新实现一个 shell。要拥有一个工作的 shell 还有很多事情要做，所以今天我只关注解析树。

解析树是表示我们命令的语法结构的东西，它将允许我们容易地执行用户输入的那些命令。

所以我们希望能够将类似这样的东西:
`ls | cat -e`
翻译成一个 shell 作业，这样我们就能够作为一个整体命令来执行。

第一步是将文本输入转换成*标记*。这样，我们将有 4 个标记，以更容易处理的方式表示我们的文本输入。

每个标记都有一个值(文本输入)和一个类型(如果是操作符或命令参数):

```
ls -> WORD
| -> PIPE
cat -> WORD
-e -> WORD 
```

Enter fullscreen mode Exit fullscreen mode

一旦被解析，我们期望得到这样的结果:

```
 pipe_sequence
              /          \
simple_command      simple_command    
      |              /          \
   cmd_name       cmd_name     cmd_suffix
      |              |             |
     'ls'          'cat'        cmd_word
                                   |
                                 '-e' 
```

Enter fullscreen mode Exit fullscreen mode

所以第二步是想出一个语法。语法是解析器生成解析树所遵循的一组规则。

这是`sh` :
语法的简单版本

```
pipe_sequence    : simple_command
                 | pipe_sequence '|' simple_command
                 ;
cmd_suffix       : cmd_word
                 | cmd_word cmd_word
                 ;
simple_command   : cmd_name
                 | cmd_name cmd_suffix
                 ;
cmd_name         : WORD                   
                 ;
cmd_word         : WORD
                 ; 
```

Enter fullscreen mode Exit fullscreen mode

这个语法告诉我们的是:

*   一个`pipe_sequence`是一个`simple_command`后跟 0 或更多的`simple_command`
*   一个`cmd_suffix`是一个或多个`cmd_word`
*   一个`simple_command`是不是一个`cmd_name`后面跟着一个`cmd_suffix`
*   一个`cmd_name`或`cmd_word`是一个单词令牌

每个规则都将作为一个函数来实现。

先说`cmd_word`

```
/*
** struct s_parser is a data type containing informations about the current parser
** (like the tokens list)
*/
struct s_cmd_word    *cmd_word(struct s_parser *p)
{
    struct s_cmd_word    *w = NULL;

    /*
    ** p->tokens is a linked list containing all the tokens
    ** p->tokens->type is an enum corresponding to the token's type
    */
    if (p->tokens->type == T_WORD)
    {
        w = malloc(sizeof(struct s_cmd_word));
        w->data = strdup(t->data);
        p->tokens = p->tokens->next
        return (w);        
    }
    else
        return (NULL);
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止很简单。如果当前令牌是一个`WORD`，那么我们返回一个包含实际数据的`struct s_cmd_word *`。但是如果当前的令牌不是一个单词(可能是一个操作符)，我们返回`NULL`。一旦使用了令牌，我们就不应该忘记前进到下一个令牌。

那`pipe_sequence`呢？

```
struct s_pipe_sequence    *pipe_sequence(struct s_parser *p)
{
    struct s_pipe_sequence    *ps;
    struct s_simple_command   *sc;

    ps = malloc(sizeof(struct s_pipe_sequence));
    while (sc = simple_command(p))
    {
        /* This function pushes a new element into a linked list */
        ft_lstpush(sc, &ps->simple_commands);
        if (p->tokens->type == T_PIPE)
            p->tokens = p->tokens->next;
    }
    if (ps->sc)
        return (ps);
    free(ps);
    return (NULL);
} 
```

Enter fullscreen mode Exit fullscreen mode

其他函数应该和上面的一样容易编写。

最后，你应该有一个基于上述语法的解析树。

当然，这是实际 shell 语法的一个非常简化的版本，但实际上这就是能够手工生成解析树所需要的全部。

如果你想实现一个 shell，你应该首先访问这个网站。这是理解像`sh`这样的 shell 如何工作的第一步。

感谢阅读:)