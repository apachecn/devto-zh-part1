# 测试 Bash 脚本

> 原文：<https://dev.to/sobolevn/testing-bash-scripts>

当你需要测试一个 bash 脚本时，你可以使用 bats 来完成。就是这么做的:

```
#!/usr/bin/env bats

@test "addition using bc" {
  result="$(echo 2+2 | bc)"
  [ "$result" -eq 4 ]
}

@test "addition using dc" {
  result="$(echo 2 2+p | dc)"
  [ "$result" -eq 4 ]
} 
```

Enter fullscreen mode Exit fullscreen mode

`bats`使用特殊的`@test`语法来定义测试。在测试主体中，每一行都应该返回`0`退出代码，否则测试将会失败。

在这里阅读全文，比较不同的工具:[https://medium . com/we make-services/testing-bash-applications-85512 e 7 Fe 2 de](https://medium.com/wemake-services/testing-bash-applications-85512e7fe2de)