# 快速用 ClojureScript 编写了一个可以写一点指令的启动工具包 chochoi

> 原文：<https://dev.to/karad/clojurechochoi-3o2l>

有很多方法可以使用 Clojure 或 ClojureScript 编写一些命令行程序。
大多是使用了 leiningen 和 boot 的东西，虽然是高功能且很棒的东西，但是个人想要的东西有以下几种。

*   可以自动生成用于在 NodeJS 上工作的命令的项目
*   可以用 clojurescript 记述处理
*   启动项目生成的生成器几乎没有时间
*   从启动到执行的时间也很快
*   构建也尽量不花时间。 也不依赖于 leiningen 和 boot
*   可以通过 npm 安装
*   构建的时候，会帮我安装命令
*   看起来很开心(捕捉到了指令的执行结果)

## 你做了什么？

可以生成名为`chochoi`的基于 ClojureScript 的 CLI 命令的工具。 `chochoi`是日语中的“有点”，可以简单迅速地创建命令项目。

*   [chochoi 的存储库](https://github.com/karad/chochoi)
*   [chochoi 的 npm 软件包](https://www.npmjs.com/package/chochoi)

想创建`hello`的指令时，通过执行`chochoi new hello`，生成可构建`hello`指令的全套项目文件。

版本还是`0.0.5`，正在发展中。 [制作 descjop](https://github.com/karad/lein_template_descjop) 时，自动生成关系中觉得“嗯”的部分应该主要得到了改善。 ( descjop 也计划在这个基础上重新制作)

※还只在 Mac 上确认了动作。 Windows 似乎需要解决路径问题。 Linux 可能会动起来。

## 开始使用前的准备

在[https://github.com/clo jure/clojurescript/releases/latest](https://github.com/clojure/clojurescript/releases/latest)上下载`cljs.jar`，并将其放置在本地存储器的某个位置

然后，设定`CLJS_PATH`的环境变量。

是这样的。

```
export CLJS_PATH="/Users/XXXXX/.cljs/cljs.jar" 
```

Enter fullscreen mode Exit fullscreen mode

## 安装 chochoi

安装`chochoi`

```
$ npm install -g chochoi 
```

Enter fullscreen mode Exit fullscreen mode

## 试着做 hello 项目

作为例子，试着制作一个叫`hello`的命令吧。

执行`chochoi new hello`后，会这样生成文件。

```
$ chochoi new hello

⚡ init chochoi project...

------------------------------------
- create : ./hello
- create : ./hello/src/hello
- create : hello/src/hello/core.cljs
- create : hello/src/hello/command.cljs
- create : hello/.gitignore
- create : hello/build.clj
- create : hello/build.js
- create : hello/config.js
- create : hello/package.json
- create : hello/README.md
------------------------------------

👍 completed. 
```

Enter fullscreen mode Exit fullscreen mode

## 组建 hello 项目

由于已生成 hello 目录，因此移动并执行`chochoi build`命令。

```
$ cd hello
$ chochoi build

⚡ build chochoi project...

completed linked command.

completed clojurescript build.
up to date in 0.055s
/Users/XXXXX/.nvm/versions/node/v8.7.0/bin/hello -> /Users/XXXXX/.nvm/versions/node/v8.7.0/lib/node_modules/hello/out/main.js
/Users/XXXXX/.nvm/versions/node/v8.7.0/lib/node_modules/hello -> /Users/XXXXX/study/chochoi/hello

👍 completed. 
```

Enter fullscreen mode Exit fullscreen mode

然后，构建完成，并且注册为命令。

## 执行

剩下的就只需要执行了。

```
$ hello kaz
Hello world! kaz 
```

Enter fullscreen mode Exit fullscreen mode

马上就启动，好快啊！

## 要摆弄处理

因为用`src/hello/command.cljs`写了执行处理，所以只需要更改这里。

```
(ns hello.command
  (:require [cljs.nodejs :as nodejs]))

(defn command [args]
  (println "Hello world!" (str (first args)))) 
```

Enter fullscreen mode Exit fullscreen mode

期待各位的明星

*   [chochoi 的存储库](https://github.com/karad/chochoi)