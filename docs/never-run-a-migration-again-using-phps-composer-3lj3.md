# 永远不要使用 PHPs Composer 再次运行迁移

> 原文：<https://dev.to/david_j_eddy/never-run-a-migration-again-using-phps-composer-3lj3>

每个人都厌倦了在更新应用程序后运行数据库迁移吗？让作曲家为你做！在这个例子中，我们将使用 Yii2 的迁移命令作为例子:

```
{
  "name": "Application Name",
  "description": "Some desciption text",
  ...
  "scripts": {
    "post-install-cmd": [
      "php console/yii app/setup --interactive=0"
    ],
    "post-update-cmd": [
      "php console/yii app/setup --interactive=0"
    ]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您以原子的方式编写迁移(一旦更改，不要重写)，迁移可以反复运行而不会导致错误。您甚至可以通过命令挂钩在一个 composer 操作之后运行多个命令。