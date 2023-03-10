# 为 Android 项目设置 GitLab CI

> 原文：<https://dev.to/s_anastasov/setting-up-gitlab-ci-for-android-projects-a6o>

我对持续集成的第一次体验是将 Bitbucket 与 [Jenkins](http://www.jenkins.io) 结合使用。我对我的设置很满意。詹金斯会在每次提交时运行以确保我的代码编译成功，运行 android lint 并运行我的单元测试。我还使用[结构](http://www.fabric.io)设置了连续部署。

[![GitLab CI](img/9035e8112df34b18741d0869d1a96eb8.png "CI/CD pipeline")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M_MmjEPL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.gitlab.com/ee/ci/img/cicd_pipeline_infograph.png)

现在，在工作中，我们使用 GitLab 作为代码库。GitLab 还提供[持续集成](https://about.gitlab.com/features/gitlab-ci-cd/)。当我们决定开始在工作中使用持续集成时，我们决定给 GitLab 一个机会。它已经与 GitLab 集成在一起，要使用它，我们只需要安装一个[转轮](https://docs.gitlab.com/runner/)。

在 GitLab 中使用 CI 很简单，在安装了 runner 之后，您需要在存储库的根目录下添加一个. gitlab-ci.yml 文件。GitLab 甚至提供模板。各种语言和框架的 gitlab-ci.yml 文件。android 模板基于[这篇【2016 年的博文。这是一个很好的指南，但不幸的是，今天它不起作用了。Google 在命令行工具中引入了一些变化。](https://about.gitlab.com/2016/11/30/setting-up-gitlab-ci-for-android-projects/)

# 正在安装 Android SDK

要在配置项上安装 Android SDK，我们需要安装[命令行工具](https://developer.android.com/studio/index.html#downloads)(滚动到底部以获得命令行工具)。命令行工具包括[SDK manager](https://developer.android.com/studio/command-line/sdkmanager.html)——一个命令行工具，允许你查看、安装、更新和卸载 Android SDK 的包。所以不要

```
- wget --quiet --output-document=android-sdk.tgz https://dl.google.com/android/android-sdk_r${ANDROID_SDK_TOOLS}-linux.tgz
  - tar --extract --gzip --file=android-sdk.tgz 
```

我们可以使用

```
- wget --quiet --output-document=android-sdk.zip https://dl.google.com/android/repository/sdk-tools-linux-3859397.zip
  - unzip -q android-sdk.zip -d android-sdk-linux 
```

下载并安装 Android SDK 工具。

在接受 Android SDK 的许可方面也有所改进。在您的开发机器上接受许可证后，工具将在 Android SDK 根目录下生成一个许可证文件夹。您可以将许可证从开发机器转移到 CI 服务器。要接受我们可以使用的许可证:

```
- mkdir android-sdk-linux/licenses
  - printf "8933bad161af4178b1185d1a37fbf41ea5269c55\nd56f5187479451eabf01fb78af6dfcb131a6481e" > android-sdk-linux/licenses/android-sdk-license
  - printf "84831b9409646a918e30573bab4c9c91346d8abd" > android-sdk-linux/licenses/android-sdk-preview-license 
```

这将创建一个包含两个文件的文件夹 licenses。文件中写入的值来自我的本地机器。根据您使用的组件，您的机器上可能还有其他文件，如谷歌电视或谷歌眼镜的许可证。一旦许可被接受，我们就可以安装软件包:

```
- android-sdk-linux/tools/bin/sdkmanager --update > update.log
  - android-sdk-linux/tools/bin/sdkmanager "platforms;android-${ANDROID_COMPILE_SDK}" "build-tools;${ANDROID_BUILD_TOOLS}" "extras;google;m2repository" "extras;android;m2repository" > installPlatform.log 
```

一个基于 Android 测试代码实验室的示例项目。gitlab-ci.yaml 文件可以在[这里](https://gitlab.com/stolea/android-gitlab-ci)找到。构建和单元测试脚本的其余部分与最初的帖子相同。最新的 x86 模拟器需要硬件加速才能运行，所以我现在跳过功能测试。