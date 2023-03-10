# 为 Rust Diesel 项目设置 Travis CI

> 原文：<https://dev.to/huytd/setup-travis-ci-for-rust-diesel-project-amj>

Rust 在 Travis CI 上支持[，Rust 有三个主要通道可以使用:*稳定*、 *beta* 和*夜间*。你可以按照他们的指导让你的 Rust 项目顺利构建。](https://docs.travis-ci.com/user/languages/rust/)

但是到了 [diesel.rs](https://diesel.rs) 就有点棘手了。

原因是，为了构建一个基于柴油的项目，您需要具备:

*   构建计算机上的数据库
*   一个带有连接到数据库配置的`.env`文件
*   安装 diesel-cli 进行迁移

所以，它需要更多的配置。

让我们在项目的根文件夹中创建一个`.travis.yml`文件。

## 第一步:锈版

您可以在 Travis CI 中选择 Rust 的任何版本，将其放入您的配置文件:

```
language: rust
rust:
  - nightly
  // or
  - 1.22.0
  // or
  - stable 
```

Enter fullscreen mode Exit fullscreen mode

## 第二步:PostgreSQL(或者你用过的任何数据库)

接下来，我们需要添加 PostgreSQL 作为额外的服务:

```
services:
  - postgresql 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用不同的数据库，只需更改它。有关支持的数据库的完整列表，请查看本文档。

## 步骤 3:初始化您的数据库并迁移

现在我们需要做很多事情，创建一个数据库，安装 **diesel-cli** ，创建一个数据库并运行迁移，让我们创建一个`before_script`部分:

```
before_script:
  - psql -c 'create database build_db;' -U postgres
  - echo "DATABASE_URL=postgres://postgres@localhost/build_db" > .env
  - cargo install diesel_cli --no-default-features --features=postgres
  - diesel migration run 
```

Enter fullscreen mode Exit fullscreen mode

## 第四步:构建和测试

最后，用`cargo` :
构建并测试您的项目

```
script:
  - cargo build --verbose --all
  - cargo test --verbose --all 
```

Enter fullscreen mode Exit fullscreen mode

这就是你所需要的！

如果你懒得遵循这四个步骤，[这是给你的要点](https://gist.github.com/huytd/8438a0d3fe1510a483a0499e63a07925)。

**P/S:** 值得一提的是，由于大量的`cargo`运行，构建时间非常慢，根据上面的要点，我的项目每次构建花费了大约 8 到 9 分钟。

您可能想在您的`.travis.yml` :
中为`cargo`添加一些缓存

```
cache: cargo 
```

Enter fullscreen mode Exit fullscreen mode

点击查看更多关于[依赖项缓存的信息。](https://docs.travis-ci.com/user/caching/)