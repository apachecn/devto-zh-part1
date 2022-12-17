# 詹金斯管道和他们的肮脏秘密 1。

> 原文：<https://dev.to/pencillr/jenkins-pipelines-and-their-dirty-secrets-1>

# 简介:

我刚刚开始我作为软件开发人员的第一份工作，这时关于构建流插件被弃用的第一条新闻出现了。当时公司已经有了一个庞大、复杂的 CI，有很多工作流组织起来的詹金斯乔布斯。很明显，一个快速的解决方案将是简单地用管道*代替构建流程工作，并保持聚会的原样*。当然，谁是解决这个小问题的最佳人选，如果不是新来的实习生！舞台已经搭好，我被派去结束这项工作。
***...但是有一个问题，因为我意识到还没有人知道这个深奥的管道脚本的语法，所以我必须弄清楚如何使用它们来准确地替换构建流脚本。出现了许多关于管道的有趣信息，这让我重新思考如何用它来替换构建流程中的某些元素。
在我的研究中，我没有在网上找到任何关于特定管道步骤的具体用例的综合资料。在这里，我打算收集和分享我所有的发现。我将特别关注如何用构建流来替换以前的某些解决方案，或者像 Active Choice 这样的其他过时的插件。
***反正我知道你不是来看故事的，所以让我们看看代码吧！*T15】*****

* * *

## 管道基础知识

如果您开始使用管道，您应该做的第一件事是阅读官方管道文档。
在这里你可以了解什么是管道，什么是 Jenkinsfile，以及所有的基础知识，我不会详细解释。
然而，这个文档有一个肮脏的小秘密，一个你可以轻易忽略的信息。 ***管道脚本有两种编写方式:声明式和脚本式。***

### 声明性管道脚本示例

幸运的是，声明性管道有很好的文档记录，你可以在[这个页面](https://jenkins.io/doc/book/pipeline/syntax/)上找到很多例子和文档。无论如何，这是我举的一个例子。注意，这里**我专门创建了一个实现作业调度解决方案的脚本，就像之前的构建流脚本一样。**
还要注意，声明性管道总是有一个`pipeline {}`块，其中有一个`agent any`部分，并且被分成几个阶段。

```
#!/usr/bin/env groovy

pipeline {
    agent any
    stages {
        stage("build") {
            steps {
                retry(3) { build("build-job") }
            }
        }
        stage("tests") {
            steps {
                parallel (
                    "unit test" : {
                        build("unit-test-job")
                    },
                    "component test" : {
                        build("component-test-job")
                    }
                )
            }
        }
    }
} 
```

### 脚本化管道脚本的示例

另一方面，脚本管道语法提供了一种通用的 DSL 语法。脚本化管道总是包含在`node {}`块中。请注意，我使用了一个`stage`，但这不是必需的。你可以在[文档页面](https://jenkins.io/doc/pipeline/steps/)阅读具体的脚本化管道步骤，但是你为什么要这么做呢？从任何一个 jenkins 你都可以找到一个管道[代码片段生成器](https://qa.nuxeo.org/jenkins/pipeline-syntax/)，它可以帮助你理解语法。这里有一个例子。

```
#!/usr/bin/env groovy

node {
    def workspace = pwd()
    echo "Building Job at ${workspace}"
    build 'builder-job'

    stage('Shell') {
        try {
            sh returnStdout: true, script: 'demo.sh'
        }
        catch (exc) {
            echo 'Something failed!'
        }
    }
} 
```

要知道，那个- ***是的！***——在一个 Jenkinsfile 中可以有两种语法。

### 声明性管道中的脚本化管道

如果有必要，您可以在声明性管道步骤中实际创建一个嵌入式脚本管道块！我们为此使用了`script{}`块。是这样的:

```
#!/usr/bin/env groovy

pipeline {
    agent any
    stages {
        stage("robot test") {
            steps {
                script {
                    MYLIST = []
                    MYLIST += "param-one"
                    MYLIST += "param-two"
                    MYLIST += "param-three"
                    MYLIST += "param-four"
                    MYLIST += "param-five"

                    for (def element = 0; element < MYLIST.size(); element++) {
                        build(
                            job: 'parameterized-job',
                            parameters: [
                                [
                                    $class: 'StringParameterValue',
                                    name: 'MYLIST',
                                    value: MYLIST[element]
                                ]
                            ]
                        )
                    }
                }
            }
        }
    }
} 
```

**注意，这里我使用了脚本块，以便能够用于 for 循环。**
在后面的章节中我们还会发现其他几个用例。