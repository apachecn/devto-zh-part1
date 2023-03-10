# 关于 Maven 版本范围

> 原文：<https://dev.to/ebud7/maven-version-ranges>

嘿，伙计们

本周，我偶然发现了 maven 依赖版本范围的问题。

这是一篇关于 maven 依赖版本范围和基于我的经验的优点/缺点的短文。

# 什么是版本范围？

Maven 允许您定义一个版本号范围(以特定的格式),以便在下一次执行构建时自动解析已定义依赖项的最新版本。

# 我可以指定什么版本范围？

| 范围 | 意义 |
| --- | --- |
| One | x > = 1.0 * 1.0 的默认 Maven 含义是一切(，)，但建议使用 1.0。显然这对于这里的强制版本不起作用，所以它被重新定义为最低版本。 |
| (,1.0] | x <= 1.0 |
| (,1.0) | x < 1.0 |
| [1.0] | x == 1.0 |
| [1.0,) | x >= 1.0 |
| (1.0,) | x > 1.0 |
| (1.0,2.0) | 1.0 < x < 2.0 |
| [1.0,2.0] | 1.0 <= x <= 2.0 |
| (,1.0],[1.2,) | x <= 1.0 or x > = 1.2。多个集合以逗号分隔 |
| (,1.1),(1.1,) | x！= 1.1 |

例如:

```
<dependency>
    <groupId>com.example</groupId>
    <artifactId>healthcheck</artifactId>
    <version>[0.0.5,0.3.0)</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

# 当前版本是什么？

您可以通过运行`mvn dependency:list |grep healthcheck`获得实际版本

结果将提供实际的版本号。它可能是这样的:`[INFO] com.example:healthcheck:jar:0.0.7:compile`

# 为什么要用？

这是魔法。在不敲击键盘的情况下，更新定义范围内的较新的依赖关系。因此，关注任何更新和可能的新特性/依赖项修复的“神奇”收获的工作更少了。

# 有何不可？

您永远无法确定新版本是否与您的代码兼容，是否完整。所以有可能你的构建/编译会神奇地被新的依赖项破坏。

# 结论

在我看来，版本范围的使用取决于个人喜好，也取决于所使用的技术。例如，JavaScriptðÿœž版本范围更受推荐和欢迎。

特别是对于 Maven，我喜欢避免版本范围，以保持我的依赖列表干净和简短，以便更好地了解使用的版本，某些子依赖项，并避免可能的站点魔法更新的影响。

# 提示

您可以使用`mvn versions:display-dependency-updates`解析更新的依赖版本

通过运行`mvn versions:resolve-ranges`或基于特定的 groupId 的`mvn versions:resolve-ranges -Dincludes=com.example:*`自动替换版本范围(可能会出现'，'分隔)

小心地按版本范围解析快照依赖关系。他们可能正在建设中，并操纵你的预期行为。

#### 相关:

[Apache maven 官方文档](http://maven.apache.org/enforcer/enforcer-rules/versionRanges.html)