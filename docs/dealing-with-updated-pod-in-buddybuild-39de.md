# 在 BuddyBuild 中处理更新的 pod

> 原文：<https://dev.to/onmyway133/dealing-with-updated-pod-in-buddybuild-39de>

原帖[https://github.com/onmyway133/blog/issues/149](https://github.com/onmyway133/blog/issues/149)

我们用 BuddyBuild 作为我们的 CI。今天我们的一个依赖者得到了一个更新[https://github . com/hyperoslo/BarcodeScanner/releases/tag/4 . 1 . 1](https://github.com/hyperoslo/BarcodeScanner/releases/tag/4.1.1)。所以我们选择了一个依赖它的项目。在本地运行时一切正常。所以我发出一个 Pull 请求，并等待 BuddyBuild 中的构建开始。

出于某种原因，BuddyBuild 无法获得 Cocoapods 的正确版本，因此无法更新新的 pods。

```
[!] The version of CocoaPods used to generate the lockfile (1.3.1) is higher than the version of the current executable (1.1.1). Incompatibility issues may arise.

=== CocoaPods ===
104
    Switching CocoaPods version to 1.1.1
105
    Using Command Line: gem cleanup "cocoapods"
106
    Using Command Line: gem uninstall "cocoapods" --all --executables --force
107
    Using Command Line: gem install "cocoapods" --no-rdoc --no-ri --version "1.1.1"
108
    Unpacking caches - cocoapods pod specs
109
    Using Command Line: pod install --no-repo-update
110
    Analyzing dependencies
111
    Pre-downloading: `Tailor` from `https://github.com/zenangst/Tailor`, tag `3.0.0`
112
    [!] Unable to satisfy the following requirements:
113
    - `BarcodeScanner (~> 4.0)` required by `Podfile`
114
    - `BarcodeScanner (~> 4.0)` required by `Podfile`
115
    - `BarcodeScanner (~> 4.0)` required by `Podfile`
116
    - `BarcodeScanner (~> 4.0)` required by `Podfile`
117
    - `BarcodeScanner (~> 4.0)` required by `Podfile`
118
    - `BarcodeScanner (~> 4.0)` required by `Podfile`
119
    - `BarcodeScanner (= 4.1.1)` required by `Podfile.lock`
120
    None of your spec sources contain a spec satisfying the dependencies: `BarcodeScanner (~> 4.0), BarcodeScanner (= 4.1.1)`.
121
    You have either:
122
     * out-of-date source repos which you can update with `pod repo update`.
123
     * mistyped the name or version.
124
     * not added the source repo that hosts the Podspec to your Podfile.
125
    Note: as of CocoaPods 1.0, `pod repo update` does not happen on `pod install` by default. 
```

Enter fullscreen mode Exit fullscreen mode

### 尝试脚本

所以我想我可以运行一些自定义脚本来强制 BuddyBuild 更新 pods。从[https://docs.buddybuild.com/builds/custom_build_steps.html](https://docs.buddybuild.com/builds/custom_build_steps.html)开始

我用
提交文件`buddybuild_postclone.sh`

```
#!/usr/bin/env bash

pod repo update
pod update BarcodeScanner 
```

Enter fullscreen mode Exit fullscreen mode

没用。然后我用
更新脚本

```
#!/usr/bin/env bash

pod repo remove master
pod setup
pod install 
```

Enter fullscreen mode Exit fullscreen mode

没用。仔细查看日志。我明白了

```
Switching CocoaPods version to 1.1.1 
```

Enter fullscreen mode Exit fullscreen mode

好像 BuddyBuild 用的是 cocoapods 1.1.1。我的是 1.4.0。

### 指定 cocoapods 版本

所以我需要指定正确的 cocoapods 版本，以确保我和 BuddyBuild 在同一个页面上

```
gem install bundler
bundler init 
```

Enter fullscreen mode Exit fullscreen mode

而在我的`Gemfile`

```
# frozen_string_literal: true

source "https://rubygems.org"

git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

gem "cocoapods", '~> 1.0' 
```

Enter fullscreen mode Exit fullscreen mode

然后运行

```
bundler install 
```

Enter fullscreen mode Exit fullscreen mode

并检查`Gemfile.lock`以确保一切正常

### 终于

提交更改，现在 BuddyBuild 获得了正确的 cocoapods 版本，因此使用了新的 pods

```
 === CocoaPods ===
45
    Switching CocoaPods version to 1.4.0
46
    Using Command Line: gem cleanup "cocoapods"
47
    Using Command Line: gem uninstall "cocoapods" --all --executables --force
48
    Using Command Line: gem install "cocoapods" --no-rdoc --no-ri --version "1.4.0"
49
    Unpacking caches - cocoapods pod specs
50
    Using Command Line: pod install --no-repo-update 
```

Enter fullscreen mode Exit fullscreen mode