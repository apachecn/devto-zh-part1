# 大锈词

> 原文：<https://dev.to/cad97/great-rust-ci-1fk6>

伟大的 CI 意味着自信地编码。而且针对 Rust 的 CI 兼容工具的情况非常好，所以您应该利用它。

但是，如果您不知道所有可用工具的全部细节，那么建立完美的 CI 会很困难。在这里，我将向您介绍我正在使用的设置。

* * *

## 安装本地工具。

以下是我每天使用的:

1.  比起 rustc 给你的，为林挺写更多自以为是的东西。

    目前，clippy 将只与最新的夜间工作。用 rustup 安装并更新每夜 rust，然后运行`cargo +nightly install clippy --force`强制安装一个针对你当前每夜的 clippy。配合`cargo +nightly clippy`使用。

2.  [Rustfmt](https://github.com/rust-lang-nursery/rustfmt) :自动代码格式化。

    对于许多(虽然不是全部，很遗憾)的夜生活，rustfmt 可以使用 rustup 来管理，就像你可以管理 rls 一样。如果你所在的夜晚包含 rustfmt，你可以做`rustup component add rustfmt-preview`。如果你的 nightly 不包含 rustfmt-preview，你可以从 crates.io: `cargo +nightly install rustfmt-nightly --force`安装。拥有 rustup 托管版本和 cargo 托管版本可能会导致冲突，所以使用其中一个。配合`cargo +nightly fmt`使用。

3.  [Cargo-Update](https://github.com/nabijaczleweli/cargo-update) :检查并更新 Cargo-installed 二进制文件。

    对 clippy/rustfmt 没有太大帮助，因为每晚都需要强制重新安装(因为它们链接到它)，但对无缝更新其他工具(包括它本身)来说是无价的。(需要 CMake)

4.  可选: [Cargo-Edit](https://github.com/killercup/cargo-edit) :从命令行用简单的命令修改构建清单(`Cargo.toml`)，而不是手工编辑文件。

5.  可选项:IDE 支持。我使用 IntelliJ IDEA 和 IntelliJ Rust 插件。还有用于 VSCode 的 [RLS，或者你可以使用](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust) [Vim](https://github.com/rust-lang/rust.vim) 或 [Emacs](https://github.com/rust-lang/rust-mode) ，如果你喜欢那种东西的话(没有评判！).

## 创建您的项目！

```
$ cargo new great-ci
     Created library `great-ci` project 
```

Enter fullscreen mode Exit fullscreen mode

设置`.git/info/exclude`来告诉 git 忽略你的 IDE 文件是一个很好的实践。对我来说，这意味着 git exclude 的内容是:

```
.idea/
*.iml 
```

Enter fullscreen mode Exit fullscreen mode

出于这个例子的目的，让我们制作一个微型的“Hello World”样式库:

```
//! Example library for great CI integration!

use std::fmt;

/// Greetings to some target
#[derive(Copy, Clone, Debug, Eq, PartialEq, Hash)]
pub struct Greeting<'a>(&'a str);

impl<'a> Greeting<'a> {
    /// Construct a Greeter to greet a target
    pub fn greet(target: &str) -> Greeting {
        Greeting(target)
    }
}

impl<'a> fmt::Display for Greeting<'a> {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "Greetings, {}!", self.0)
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn it_works() {
        assert_eq!(2 + 2, 4);
    }

    #[test]
    fn greet_author() {
        let greeter = Greeting::greet("CAD97");
        assert_eq!(greeter.to_string(), "Greetings, CAD97!");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

添加许可证和自述文件，然后是提交的时候了，然后[在线获取代码](https://github.com/CAD97/great-ci/tree/91a0947f638fb7beda5356e7dc520296fcffae93)。

```
$ git add .
$ git commit -m "Initial commit"
$ git remote add CAD97 git@github.com:CAD97/great-ci.git
$ git push -u CAD97 master 
```

Enter fullscreen mode Exit fullscreen mode

## 设置 CI

我们关心的部分！我使用 [Travis CI](https://travis-ci.org/) 、 [Cargo-Travis](https://github.com/roblabla/cargo-travis) 、 [Codecov](https://codecov.io/) 和 [Bors](https://bors.tech/) 作为我的 CI 堆栈。

那么让我们切换到一个特性分支，设置一个基本的`bors.toml`

```
status = [
    "continuous-integration/travis-ci/push"
] 
```

Enter fullscreen mode Exit fullscreen mode

和`.travis.yml` :

```
language: rust

rust:
  - stable
  - beta
  - nightly

script: |
  cargo build --verbose &&
  cargo test  --verbose &&
  cargo doc   --verbose

branches:
  only:
    - staging # bors r+
    - trying  # bors try
    - master 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记为存储库启用 Travis 和 Bors(我已经启用了)，然后[发送一个 PR](https://github.com/CAD97/great-ci/pull/2) 。如果一切顺利，你的 CI 是绿色的，可以和`bors r+`合并。

## 测试 rustfmt 和 clippy

当你在处理一个你期望变大的项目时，它可以帮助执行标准格式，clippy 在防止简单错误和让人们远离有问题的 API 决策方面有着惊人的表现。

因为 rustfmt 和 clippy 有些不稳定，需要某些小范围的夜间编译器，有时会中断，并且更改默认格式或添加 lints 可能会中断您的构建，我们依赖于工具的特定版本，您可以手动更新版本。

我们使用一个包含 Travis 上的的[矩阵来添加支票:](https://docs.travis-ci.com/user/customizing-the-build/#Explicitly-Including-Jobs) 

```
cache: cargo
matrix:
  include:
    - rust: nightly-2018-01-12
      env: # use env so updating versions causes cache invalidation
        - CLIPPY_VERSION=0.0.179
      before_script:
        - rustup component add rustfmt-preview
        - cargo install clippy --version $CLIPPY_VERSION || echo "clippy already installed"
      script:
        - cargo fmt -- --write-mode=diff
        - cargo clippy -- -D clippy 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们使用`cargo install`安装 clippy，如果已经安装，则成功安装。我们使用 rustup 安装 rustfmt，因为在 Travis 的 Rust 设置中，rustfmt 已经被 rustup 跟踪，所以`cargo-fmt`可执行文件存在，没有`--force`的安装会失败。如果我们不选择一个真正包含`cargo-fmt`的夜间工具链，使用它将会失败。这里我们有`rust: nightly-2018-01-12 CLIPPY_VERSION=0.0.179`,因为这是 rustfmt-preview 所在的最新工具链，也是每晚构建的 clippy 的最新版本。

安装像 rustfmt 和 clippy 这样的工具需要时间，而 CI 在它*快*的时候变得很棒，所以我们启用 [Travis 的 Rust Cargo cache](https://docs.travis-ci.com/user/caching/#Rust-Cargo-cache) 来缓存货物的符号。这加快了构建的速度，因为 cargo 不需要重新编译依赖项，不管它们是构建依赖项还是工具。

缓存由语言版本(这里是`rust: nightly-2018-01-18`)和 env(这里是`CLIPPY_VERSION=0.0.180`)决定，并且在具有相同 rust 版本和 env 的构建作业之间共享。通过将 clippy 的版本放在 env 中，我们将工具构建的缓存与其他缓存分开，并确保版本更新会导致重新构建。

您可能想将这些环境变量放在 CI 配置中，这样您就可以更新它们，而无需额外提交到存储库。这是不明智的，有两个主要原因。首先，版本碰撞这些工具很有可能改变您的构建需求，并且会突然出现问题。这种变化应该在您的存储库中进行标记。其次，在这里使用 env 也是为了将这些工具的缓存与其他缓存分开。如果指定的 rust 版本是每夜最新的，那么这个缓存和每夜缓存会互相碰撞。你也不想在不需要的 CI 构建上强制安装这些工具，因为我们想要那些*灵活*的 CI 绿色。

所以做出这些改变，[提交一份 PR](https://github.com/CAD97/great-ci/pull/3) ，我们开始最后一步！

## 利用货物-特拉维斯

Cargo-travis 使您能够轻松使用 [kcov](https://github.com/SimonKagstrom/kcov) 并将覆盖信息上传到 codecov 或工作服。此外，我最近添加的，它允许您将您的板条箱的文档上传到 GitHub 页面，并维护一个可理解的 git 历史和目录结构，允许您记录多个分支(`master` / `beta` / `release`分支，以模仿 Rust 的`nightly` / `beta` / `stable`列车)，如果您愿意的话。

因为这些任务可能需要一段时间，并且更多的是为了通知决策而不是硬关 PRs，所以我将这些放入我的构建的允许失败矩阵行中。这意味着 Travis 在上述四个作业通过后立即通过(`rust: [stable, beta, nightly]`、rustfmt & clippy)，并允许 cargo-travis 作业继续在后台运行。

我们的`.travis.yml`新增:

```
env: # required for allow_failures
matrix:
  fast_finish: true
  allow_failures:
    - env: NAME='cargo-travis'
  include:
    - env: NAME='cargo-travis'
      sudo: required # travis-ci/travis-ci#9061
      before_script:
        - cargo install cargo-update || echo "cargo-update already installed"
        - cargo install cargo-travis || echo "cargo-travis already installed"
        - cargo install-update -a
      script:
        - |
          cargo build    --verbose &&
          cargo coverage --verbose &&
          bash <(curl -s https://codecov.io/bash) -s target/kcov
        - |
          cargo doc --verbose &&
          cargo doc-upload
      addons: # required for kcov
        apt:
          packages:
            - libcurl4-openssl-dev
            - libelf-dev
            - libdw-dev
            - binutils-dev
            - cmake 
```

Enter fullscreen mode Exit fullscreen mode

如果要用工作服，用`cargo coveralls`代替`cargo coverage`就行了。如果不想使用 Codecov，只需删除 Codecov bash 脚本行。

然而，这对于`doc-upload`任务来说还不够(尽管这个*不会*让你的 CI 变红，因为我们允许这个任务失败)。为了上传文档，`doc-upload`脚本需要获得权限才能推送到您的 GitHub 存储库。最简单的方法，我们将在这里使用，是一个 [GitHub 令牌](https://github.com/blog/1509-personal-api-tokens)，但是你可以在 [cargo-travis README](https://github.com/roblabla/cargo-travis#doc-upload) 上阅读更多关于这个选项的信息。

为了生成 GitHub 令牌，请导航到[您的令牌设置页面](https://github.com/settings/tokens)。当你在那里时，检查你当前发行的令牌，并撤销那些你不再使用的令牌。生成一个新的令牌，给它一个描述性的名字(这个可以稍后编辑)(我建议用`repo-name-doc-upload`)和`public_repo`作用域(这个可以稍后编辑)。请确保在生成令牌后复制它，因为您将再也看不到它了(尽管您可以重新生成它)！这个令牌允许任何拥有令牌的人在读/写您有权限读/写的任何公共存储库时充当您的角色，所以要保证它的安全。将其添加到 Travis 上的`GH_TOKEN`环境变量中，并*确保“在构建日志中显示值”设置为 off* 。

[![Environment Variables](img/b839bbc2421c5c53a10de1d084a24ffa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T6pIP9Lr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/kfu4hgY.png)

一旦你发出你的简历，新的工作就会开始。

关于此设置，需要注意以下几点:

*   CI 将在覆盖完成之前通过。这是有意的，因为我们希望看到测试通过/失败的快速 CI 反馈，并且覆盖率不需要减缓 PRs 的反馈循环(或者花费在`bors r+`和合并上的时间)。尽管如此，我们仍然希望看到这些统计数据，Codecov/workalls 仍然会进行分析，并在覆盖信息上传后发表评论。
*   第一次会花很长时间；你要构建大量的工具，包括 kcov 本身。然而，希望所有这些都被缓存，这样后续的构建会快得多。
*   当 CI 仅构建主分支时，将构建文档。您可以通过向`cargo doc-upload`传递(可选多个)`--branch NAME`参数来指定要构建的分支。
*   `doc-upload` *不会*为你建立文档，所以你需要自己调用`cargo doc`。这样，如果你的库提供了特性标志或其他条件编译，你就可以在`target/doc` (rustdoc 的输出目录)中建立一个目录，然后`doc-upload`将在上传到 GitHub 页面时使用这个目录。
*   文档在`https://<user>.github.io/<repo>/<branch>/<crate>/`结束，在这个例子中是[https://cad97.github.io/great-ci/master/great_ci/](https://cad97.github.io/great-ci/master/great_ci/)。
*   [覆盖率为 100%](https://codecov.io/gh/CAD97/great-ci) 🎉

## 进一步扩展

如果你想(或者需要)在 Windows 平台上测试，经常使用 [AppVeyor](https://www.appveyor.com/) 。这个配置项可以与这个 Travis 设置并行使用，没有任何问题。但是，一定要告诉 Bors 等待 AppVeyor 构建！

不要忘记，如果您不希望 rustfmt 和 clippy 警告应用于某些代码块，您可以取消它们。如果是源库中的问题，提交一个问题，或者如果只是不一致，检查配置，看看是否可以在那里更改。

对于注重安全的人来说，GitHub 令牌的`public_repo`范围可能太广了。这是一个已经解决的问题；解决方案是特定于回购的部署密钥。如果您为您的存储库提供了一个部署密钥，并将其加载到 Travis 中(并且没有提供令牌)，`doc-upload`将会使用它。

您已经建立了 CI，在您的自述文件中使用徽章和嵌入覆盖率图表来吹嘘它吧！ [Shields|IO](https://shields.io/) 为几乎所有服务提供风格一致的徽章，而 [Codecov graphs](https://codecov.io/gh/CAD97/great-ci/branch/master/graphs) 则是🔥🔥🔥。

你的 GitHub 页面的基础页面(`<user>.github.io/<repo>/`)将会转到 404，因为在你的`gh-pages`分支的根中不存在`index.html`。对于分支根(`<user>.gihub.io/<repo>/<branch>`)也是如此。`doc-upload`故意不弄乱你的根目录或分支根处的`index.html`，这样你就可以在这些地方包含一个 HTML 重定向，或者如果你愿意的话包含一个更有信息的索引。对于 HTML 重定向，使用以下代码:

```
<meta http-equiv="refresh" content="0; url=<crate>">
<a href="<crate>">Redirect</a> 
```

Enter fullscreen mode Exit fullscreen mode

[在 Reddit](https://redd.it/7rnyg8) 上讨论这个问题，告诉我我是否遗漏了什么或者什么东西过时了！

[参见 GitHub 上的公共知识库！](https://github.com/CAD97/great-ci)