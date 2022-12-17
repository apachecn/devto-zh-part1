# 用 Kotlin 映射 JPA 实体

> 原文：<https://dev.to/livioribeiro/mapping-jpa-entities-with-kotlin-36d>

Kotlin 是一种非常有趣的编程语言，由运行在 JVM 上的 T2 JetBrains 创建。它的一个特性是[数据类](https://kotlinlang.org/docs/reference/data-classes.html#data-classes)，这些类的主要目的是保存数据。有人可能认为数据类对于映射数据库实体来说是完美的，它们实际上非常好，但是为了在 JPA 中正确地使用它们，您还需要多考虑一些。

数据类根据主构造函数中定义的属性自动实现`toString()`、`equals()`和`hashCode()`。正因为如此，你需要思考应该用什么属性来判断两个对象是否相等。

在 JPA 中使用数据类时有一个警告:数据类需要一个至少有一个参数的主构造函数，而 JPA 需要一个没有参数的构造函数。为了帮助解决这个问题，Kotlin 有一个[无参数编译器插件](https://kotlinlang.org/docs/reference/compiler-plugins.html#no-arg-compiler-plugin)，它可以生成一个只能使用反射调用的零参数构造函数。Kotlin 还提供了 [jpa 插件](https://kotlinlang.org/docs/reference/compiler-plugins.html#jpa-support)，它包装了为 jpa 配置的`no-arg`插件。

让我们以任务列表应用程序的以下模式为例:

```
CREATE TABLE IF NOT EXISTS "task_list" (
    "id" SERIAL PRIMARY KEY,
    "name" VARCHAR(200) NOT NULL
);

CREATE TABLE IF NOT EXISTS "task" (
    "id" SERIAL PRIMARY KEY,
    "task_list_id" INTEGER NOT NULL,
    "name" VARCHAR(200) NOT NULL,
    "description" TEXT,
    "due_date" DATE,
    "done" BOOLEAN DEFAULT FALSE,

    FOREIGN KEY ("task_list_id") REFERENCES "task_list" ("id")
); 
```

对于这个模式，我们可以看到我们将需要两个实体:`TaskList`和`Task`。首先让我们来绘制`TaskList`的地图:

```
package com.example.domain

import javax.persistence.*

@Entity
data class TaskList(
    @Id @GeneratedValue
    val id: Int = 0,
    val name: String
) {
    @OneToMany(mappedBy = "taskList")
    val tasks: MutableSet<Task> = HashSet()
} 
```

`id`和`name`属性在主构造函数中定义，但是`tasks`在类体中定义。这样`equals()`、`hashCode()`、`toString()`只用`id`和`name`。由于`tasks`映射了一个关系，最好不要用主构造函数。

现在到了`Task`:

```
package com.example.demo.domain

import java.time.LocalDate
import javax.persistence.*

@Entity
data class Task(
    @Id @GeneratedValue
    val id: Int = 0,
    val name: String,
    val description: String? = null,
    val dueDate: LocalDate? = null,
    val done: Boolean = false
) {
    @ManyToOne
    lateinit var taskList: TaskList

    constructor(name: String, taskList: TaskList) : this(name = name) {
        this.taskList = taskList
    }
} 
```

`TaskList`也是如此，但现在我们和`Task`的关系是相反的。我们将属性`taskList`定义为`lateinit`有两个原因:一是因为它会被懒加载；第二，我们不需要马上初始化它，而是在二级构造函数上赋值。

如果我们将所有属性放在主构造函数中，简单地调用`println(task)`会导致我们的应用程序因无限递归而崩溃，因为`Task::toString()`会调用`TaskList::toString()`，后者会调用`Task::toString()`等等。