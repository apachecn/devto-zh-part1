# 为了管理中等规模的 Swagger 文件，制作了一个很薄的框架- swglow

> 原文：<https://dev.to/chuross/swagger---swglow-c47>

## 这次的成果

### Swaglow

[https://github.com/chuross/swaglow](https://github.com/chuross/swaglow)

## specialty

从基于特定规则的目录层次结构合并并输出 swagger YAML 文件。

*   给 Swagger 的 Yaml 运用带来**最低限度**的秩序
    *   因为沿着框架生成的目录追加文件，所以容易将文件管理规则化
*   根据目录层次和名称自动生成`paths`和`definitions`的定义
    *   没有必要意识到最终的 Yaml 是如何生成的
    *   可以集中精力分别管理 Yaml 文件

## Weft

在 Swagger 上使用的 Yaml(or Json )最终必须作为一个文件来处理，但是如果什么都不想就坐上去的话，文件就会变得庞大而难以管理。

因此，分割 Yaml 并结合比较好，npm 中也已经有丰富的实现它的程序库。 但是，都是使用`$ref`的方式，这样的话就没有以哪个单位分割文件的规则，所以想从结构上再束缚一下。

## import

1.  `$ npm install -g swaglow`

## 使用方法

### Initial

首先在项目的根目录中执行 init 命令

`$ swaglow init -o [output path]`

这样，这样的目录就会在输出处生成

```
swaglow root // -oで指定したパスのルート
- swaglow.json // 専用のコンフィグファイル
- paths // APIのエンドポイントを管理するディレクトリ
- definitions // モデルを管理するディレクトリ
- parameters // APIリクエストで使う共通のパラメータを管理するディレクトリ 
```

Enter fullscreen mode Exit fullscreen mode

### Opening

作为简单的例子，链接粘贴 README.md 中的内容
[githu B- Chu Ross/swag low:simple swagger framework](https://github.com/chuross/swaglow#example)

作为更实践的例子，我准备了使用这个库为 Qiita API 制作 Swagger 的资源库，希望您能确认一下

加入将使用 swagger-tools 等输出的 yaml 进行 validate 或构建到 IOs 客户端的结构，用 CI 进行旋转

[GitHub-Chu Ross/Qiita-swagger-YAML:Qiita 的 swagger yaml 文件](https://github.com/chuross/qiita-swagger-yaml)

### 构建

`$ swaglow build -o [output path]`

生成与输出目标绑定的 Swagger Yaml 文件

## 许可证

用它

## 后记

像这次介绍的那样，基本上不推荐通过手动操作生成 Swagger 的 Yaml。

*   如果不跟随，有可能与服务器端的安装背离(维护成本增加)
*   有可能出现 Swagger Yaml 工匠(属人化)
    *   需要建立由团队整体管理的机制等

在这种意义上，如果是对自己公司开发的 API 提出请求的情况下，为了提高维护的效率，从在服务器端编写的代码中自动生成比较好吧。

那么，要说手动运用真的没有意义吗，根据情况也不是那样的。

例如，被认为可以用于这种情况

*   项目刚启动，服务器端没有准备好
    *   作为模型服务器活用
    *   正式的运用如果服务器方面做好了的话，在那里做好像比较好。
*   其他公司团队的产品等原本就很难与 Swagger 合作的情况

在这种情况下，我认为是个案，所以如果因为某些原因需要进行手动运用管理的话，也许可以使用这个框架。