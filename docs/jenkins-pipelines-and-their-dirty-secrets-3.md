# 詹金斯管道和他们的肮脏秘密 3。

> 原文：<https://dev.to/pencillr/jenkins-pipelines-and-their-dirty-secrets-3>

#### 管道的窍门和窍门

这一章是怎么来的？我基本上只是在工作的时候在这里贴东西。管道有许多微妙之处，我建议你们所有人创建一个自己的詹金斯作为学习的游乐场，并进行实验。

* * *

## 处理构建依赖关系

现在，在我们开始之前，让我们记住，管道脚本可以用*声明式*或*脚本式*语法来制定。本节中的第一个示例代码是用声明性管道语法编写的。如果你不知道区别在哪里，就把[第一章](https://dev.to/pencillr/jenkins-pipelines-and-their-dirty-secrets-1)读完。我们来看以下几个问题:

### 1。你有两个相互衔接的工作:

```
#!/usr/bin/env groovy

// HEADS UP: This is Declarative Pipeline syntax

pipeline {
agent any
    stages {
        stage("build") {
            steps {
                build('job-1')
                build('job-2')
            }
        }
    }
} 
```

在这种情况下，将首先执行第一个构建步骤，但是如果第一个作业失败，整个作业将停止并失败。但是假设你不想这样。**相反，您需要下面的**:

*   您希望两个作业都运行，即使第一个作业失败，并且只有在第二个作业失败时才会失败
*   您希望作业一个接一个地运行，而不是并行运行。

下面是你的解决方案:使用 **`propagate`** 属性，设置为 **`false`** 。如果禁用，则即使下游构建不稳定或失败，此步骤也会成功。

```
build(job: 'job-1', propagate: false)
// This step will run even if job-1 failed
build(job: 'job-2') 
```

在这种情况下，即使第一步失败，作业也会开始第二步构建。*但是有一个陷阱*。如果第一个失败了，您不会从管道的结果中知道这一点。即使第一个作业失败，管道作业的结果也将被评估为`SUCCESS`。这不一定是个问题，但是第三个例子将为这个问题提供一个解决方案。

### 2。您希望运行并行构建:

```
#!/usr/bin/env groovy

pipeline {
    agent any
    stages {
        stage("test") {
            steps {
                parallel (
                    "Unit Test" : {
                        build("unit-test-job")
                    },
                    "Component Test" : {
                        build("component-test-job")
                    },
                    "Build" : {
                        build("build-job")
                    }
                )
            }
        }
    }
} 
```

是的，但是有什么问题吗？是的，有 :
其中之一，你不能有别的，但在那个阶段的平行块。如果您想添加一个单独的构建步骤，就像这样:

```
//    !!!!!!!!!!!!!!!!!!!!!!!!!!
//    DONT USE! BAD CODE

            steps {
                parallel (
                    "Unit Test" : {
                        build("unit-test-job")
                    },
                    "Component Test" : {
                        build("component-test-job")
                    },
                    "Build" : {
                        build("build-job")
                    }
                )
                // extra build step together with parallel block
                build("extra-job")
            }
//    !!!!!!!!!!!!!!!!!!!!!!!!!! 
```

...您将得到以下错误:
`WorkflowScript: 6: Invalid step "parallel" used - not allowed in this context - The parallel step can only be used as the only top-level step in a stages step block.`

**一个快速的解决方案是用脚本管道语法重新编写你的脚本:**

```
node {
// HEADS UP: this is Scripted Pipeline syntax
    stage("Build") {
        parallel (
            "First Build" : {
                build("first-build-job")
            },
            "Second Build" : {
                build("second-build-job")
            }
        )
        build("test-job")
        parallel (
            "Third Build" : {
                build("third -build-job")
            },
            "Last Build" : {
                build("last-build-job")
            }
        )
    }
} 
```

### 3。您想要检查构建

构建步骤有一个隐藏的属性: **`wait`** (就像`propagate`，你在本章之前已经看到过)。

```
build(job: 'example-job', wait: true) 
```

**警告**:我在网上看到很多人把`wait`和`propagate`搞混了，他们想忽略流水线上某个作业的失败。让我们来看看不同之处:

> `build(job: 'example-job', wait: false)`:这意味着流水线**不会等待这个构建步骤的结果，只是启动它并跳转到下一个步骤**。如果你把这样的积木一个接一个地放在一起，它们会一个接一个地开始，而不需要等待对方完成(实际上几乎就像一个平行的积木)。
> `build(job: 'example-job', propagate: false)`:这意味着流水线会**先执行这一步**，即使这一步失败**也会继续下一步。**

`wait`是否默认**为真**，所以通常你不必把它添加到代码中。如果不将其设为 false，构建步骤的返回值将是一个对象，您可以在其上获取只读属性:这样您就可以检查它的。结果等等。

```
node {
    stage("example") {
        echo build("example-job").result
    } 
```

有了这个，即使你用设置`propagate: false` 忽略了失败的构建，你实际上也可以实现**设置构建的结果:基本上你要做的就是在用`if`块检查给定的条件后，手工设置作业结果。
这样，即使第一个作业失败，管道也会继续运行，但是您可以从管道的结果中看到是否有问题。** 

```
node {
    stage("example") {
        b = build(job: "example-job", propagate: false).result
        if(b == 'FAILURE') {
            echo "First job failed"
            currentBuild.result = 'UNSTABLE' // of FAILURE
        }
    }
    stage("test") {
        build("test-job")
    }
} 
```