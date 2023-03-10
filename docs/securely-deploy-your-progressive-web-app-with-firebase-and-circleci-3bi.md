# 使用 Firebase 和 CircleCI 安全部署您的渐进式 web 应用程序

> 原文：<https://dev.to/raybesiga/securely-deploy-your-progressive-web-app-with-firebase-and-circleci-3bi>

所以你已经花了相当多的时间来构建你的渐进式网络应用程序(PWA ),现在是时候部署它了。从哪里开始？！我将分享我遇到的最简单的方法来完成这项工作。我们将在这个特定过程中使用的关键平台是 Firebase 和 CircleCI。好了，我们开始吧。

### 设置 Firebase

首先，你需要注册 [Firebase](https://firebase.google.com) 。完成后，在`Hosting`选项卡下创建一个项目。我创建了一个叫做`ray-besiga`的。然后会弹出一个模式，指示在命令行上安装 Firebase 工具。

```
$ npm install -g firebase-tools 
```

接下来，导航到 PWA 所在的目录并运行以下命令:

```
$ firebase login
$ firebase init 
```

第一个命令让您登录 Google Firebase 控制台，第二个命令启动您的项目。在您的终端上，您应该有以下输出(附带许多火表情符号！！)

```
You're about to initialize a Firebase project in this directory:

  /Users/username/Projects/fancy_new_project

Which Firebase CLI features do you want to setup for this folder?
Press Space to select features, then Enter to confirm your choices. (Press <space> to select)
❯◯ Database: Deploy Firebase Realtime Database Rules
 ◯ Firestore: Deploy rules and create indexes for Firestore
 ◯ Functions: Configure and deploy Cloud Functions
 ◯ Hosting: Configure and deploy Firebase Hosting sites
 ◯ Storage: Deploy Cloud Storage security rules 
```

向下滚动到主机，按下`space`栏选择它，然后回车确认您的选择。然后，它将带您进入项目设置。

```
 === Project Setup

First, let's associate this project directory with a Firebase project.
You can create multiple project aliases by running firebase use --add,
but for now we'll just set up a default project.

Select a default Firebase project for this directory: (Use arrow keys)
  [don't setup a default project]
  Tribe Kampala (tribe-kampala)
❯  Ray Besiga (ray-besiga)
  [create a new project] 
```

接下来是`Hosting Setup`页。我使用了下面的默认设置，但是由于一些原因我们不得不做一些改变。

```
=== Hosting Setup

Your public directory is the folder (relative to your project directory) that
will contain Hosting assets to be uploaded with firebase deploy. If you
have a build process for your assets, use your build's output directory.

What do you want to use as your public directory? public
Configure as a single-page app (rewrite all urls to /index.html)? No
✔  Wrote public/404.html
✔  Wrote public/index.html

i  Writing configuration info to firebase.json...
i  Writing project information to .firebaserc...

✔  Firebase initialization complete! 
```

{:.我正在使用 Jekyll 作为我的静态站点生成器来构建我的 PWA。默认情况下，Jekyll 构建到`_site`，而`Firebase`期望部署到`public`文件夹。我们将在`firebase.json`文件中对此进行修改，当我们运行`firebase init`命令时，该文件与`.firebaserc`文件一起创建。编辑`firebase.json`文件，如下所示:

```
{
  "hosting": {
    "public": "_site",
    "ignore": [
      "firebase.json",
      "**/.*",
      "**/node_modules/**"
    ]
  }
} 
```

这样，我们就可以部署我们的站点了。Firebase 为我们提供了以下命令来实现这一点。

```
$ firebase deploy 
```

现在，您应该能够在浏览器中导航到终端中显示的 URL，以查看新部署的网站。谢谢 Firebase！但是，我相信您也意识到部署是手动的。一个更酷的方法是用 Github 做的`commit-to-deploy`。我们要和 CircleCI 一起做这件事。

### 设置 CircleCI

前往 [CircleCI](https://circleci.com) ，使用您的 Bitbucket 或 Github 登录，添加您希望继续部署的项目。此时，您可以创建一个`circle.yml`文件，或者让别人为您创建一个。如果你决定不做推论而去做，我更喜欢像 CircleCI 那样做。我的很简单，看起来像这样:

```
# The Firebase tools want a v6.x version of Node but Circle CI
# defaults to v4.x. Set the latest LTS version.
machine:
  node:
    version: 6.10.3

# Need to install firebase-tools so that the deploy works
dependencies:
  pre:
    - npm install -g firebase-tools

# Need to tell Circle CI how to build the site
compile:
  override:
    - bundle exec jekyll build --verbose

# Circle CI expects some kind of testing to pass in order for the
# build to be successful and deploy. Since you don't have tests
# you can fake it by just returning true. http://gph.is/1MLPDWK
test:
  override:
    - "true"

# How you tell Circle to deploy to Firebase. The important thing to
# note here is the FIREBASE_TOKEN env variable. See below.
deployment:
  production:
    branch: master
    commands:
      - firebase use default
      - firebase deploy --token=$FIREBASE_TOKEN --non-interactive 
```

提交这个文件，向上游推进到你的遥控器，点击 build，瞧！

```
$ firebase use default

Error: Command requires authentication, please run firebase login

firebase use default returned exit code 1

Action failed: firebase use default 
```

哦，等等，我们还没到那一步。看起来我们缺少了在`circle.yml`文件最后一行中指定的令牌。这个令牌是必不可少的，因为它授权访问 CI 服务器上的`firebase`工具。要解决此问题，我们将运行以下命令:

```
$ firebase login:ci 
```

一旦被授权访问，您应该在您的终端中有一个类似这样的令牌

```
Waiting for authentication...

✔  Success! Use this token to login on a CI server:

1/1QDE1Do0PhmhPG60pQi1fnXoSOjnRhfMQLzBzlJSxiaWUmacndrIee-GEhxvs41B

Example: firebase deploy --token "$FIREBASE_TOKEN" 
```

复制令牌，在 CircleCI 的“项目设置”中的“构建设置”下，单击“环境变量”链接，然后单击“添加变量”按钮。确保变量的名称是`FIREBASE_TOKEN`，值是令牌。

现在提交您在回购中所做的任何更改，并观察构建。成功！快乐的舞蹈。希望你喜欢跟随。下一次，我们将使用 SemaphoreCI 和 Firebase 尝试类似的过程。