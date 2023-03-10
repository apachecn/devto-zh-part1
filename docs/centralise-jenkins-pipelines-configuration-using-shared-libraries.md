# 使用共享库集中配置 Jenkins 管道

> 原文：<https://dev.to/jalogut/centralise-jenkins-pipelines-configuration-using-shared-libraries>

这篇文章是教程[的续篇，用 Jenkins Pipelines 和 Blue Ocean](https://dev.to/jalogut/setup-continuos-integrationdelivery-system-in-just-4-steps-with-jenkins-pipelines-and-blue-ocean) 的 4 个步骤设置 Continuos 集成/交付系统。

既然您已经知道了如何设置您的管道，那么您可能想要更进一步，为您的所有项目只保留一个管道配置。使用[共享库](https://jenkins.io/doc/book/pipeline/shared-libraries/#defining-a-more-structured-dsl)是可能的。其思想是，您的标准管道配置驻留在一个共享存储库中，每个项目都可以访问该存储库。每个项目将只在自己的`Jenkinsfile`中设置特定的属性。

让我们来看看怎么做:

### 共享库仓库

1-创建一个新的`git`库，您的`shared library`将驻留在这里。
2-在里面创建一个`vars`目录和一个`.groovy`文件。比如把这个文件叫做`standardPipeline.groovy`。
3-将以下代码示例添加到该文件中。

```
 def call(body) {

        def config = [:]
        body.resolveStrategy = Closure.DELEGATE_FIRST
        body.delegate = config
        body()

        node {
            // Clean workspace before doing anything
            deleteDir()

            try {
                stage ('Clone') {
                    checkout scm
                }
                stage ('Build') {
                    sh "echo 'building ${config.projectName} ...'"
                }
                stage ('Tests') {
                    parallel 'static': {
                        sh "echo 'shell scripts to run static tests...'"
                    },
                    'unit': {
                        sh "echo 'shell scripts to run unit tests...'"
                    },
                    'integration': {
                        sh "echo 'shell scripts to run integration tests...'"
                    }
                }
                stage ('Deploy') {
                    sh "echo 'deploying to server ${config.serverDomain}...'"
                }
            } catch (err) {
                currentBuild.result = 'FAILED'
                throw err
            }
        }
    } 
```

4-提交和推送

### 詹金斯配置

1-转到`Manage Jenkins > Configure System > Global Pipeline Libraries`
2-给你的库命名并设置 git url
[![Global Pipeline Configuration](img/6b20be0445826955c4e86a23fabc16af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_ot9aIK4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/svog5t4qkym153doewl3.png)

### 在你的项目中使用库

1-转到你的项目库根目录，用下面的代码编辑你的`Jenkinsfile`的内容

```
 @Library("your-library-name") _
    standardPipeline {
        projectName = "Project1"
        serverDomain = "Project1 Server Domain"
    } 
```

2-注意，库的名称必须与您之前在 Jenkins 中设置的名称相同。

就是这样！您现在可以在 Blue Ocean 中查看您的项目如何使用共享管道配置
[![shared-library pipeline](img/68870dc6da10f713c8ba0a71a32bc016.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E8ez1ux_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ledioxgmvxicggym8zu3.png)

### 回顾

在结束之前，让我们更详细地看看我们使用的代码:

```
@library("your-library-name") _ 
```

您可以在这里定义要导入的库。您可以在同一个`Jenkinsfile`中使用任意多个库。不要忘记结尾的`_`，它是必需的！

```
standardPipeline {
    //...
} 
```

该程序块的名称必须与您的`.groovy`库名称相匹配。在这个例子中，那将是`standardBuild`，因为我们的文件被称为`standardPipeline.groovy`

```
standardPipeline {
    projectName = "Project1"
    serverDomain = "Project1 Server Domain"
} 
```

`projectName`和`serverDomain`是如何设置可以在共享库上访问的属性的示例:

```
def call(body) {
    def config = [:]
    body.resolveStrategy = Closure.DELEGATE_FIRST
    body.delegate = config
    body()

    //... 
```

这段代码负责加载前面提到的属性。因此，可以使用`config.propertyName`访问属性

您可以使用属性来标识项目设置或执行不同的操作。例如:

```
if (config.propertyName == true) {
    // Do something here 
} 
```

仅此而已。现在，您可以尝试和配置您的共享管道。我们使用两种管道配置:

*   对于我们的项目
*   构建可以作为项目依赖项安装的公共模块。

`commonModuleBuild.groovy`有点不同，因为它不需要`Deploy`步骤。

您可以在此处找到用于本教程的资源:

*   [共享库文档](https://jenkins.io/doc/book/pipeline/shared-libraries/)
*   [jalo gut/Jenkins-基本-共享-库-样本](https://github.com/jalogut/jenkins-basic-shared-library-sample)
*   [jalogut/Jenkins file-shared-library-sample](https://github.com/jalogut/jenkinsfile-shared-library-sample)