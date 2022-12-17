# Java 和 BCP 47 语言标签

> 原文：<https://dev.to/joconner/java-and-bcp-47-language-tags>

从 Java 7 开始，Java 的 Locale 对象已经被更新，扮演了一个语言标签的角色，正如在 [RFC 5646](https://medium.com/r/?url=https%3A%2F%2Ftools.ietf.org%2Fhtml%2Frfc5646) 和 BCP 47 规范中所确定的。新的语言标签支持使开发人员能够更精确地识别语言资源。新的语言环境，作为一个语言标签，可以用来识别这种通用形式的语言:

```
language[-script][-region]*(-variant)*(-extension)[-privateuse] 
```

Enter fullscreen mode Exit fullscreen mode

当然，您可以继续将 locale 视为一个`lang_region_variant`标识符，但是 Java 现在使用 RFC 5646 规范来增强 Locale 类，以支持语言、脚本、更广泛的区域，甚至在需要时支持更新的扩展。如果生成这个标识符字符串的规则看起来令人生畏，您可以使用`Locale.Builder`类来构建标签，而不用担心会创建一个格式错误的标签。

主要的*语言标识符*几乎和你一直知道的一样；这是一个由两个字母组成的代码`ISO 639`或三个字母组成的代码。规范建议尽可能使用最短的 id。

*脚本*是新的。现在，您可以添加一个适当的脚本标识符来指定该语言所使用的书写系统。人们可以使用多种书写系统来书写语言。例如，说日语的人/作家可以使用 3 种或更多种不同的日语脚本:日本汉字、平假名、片假名，甚至“罗马字”或拉丁脚本。塞尔维亚语是另一种经常用拉丁语或西里尔字符书写的语言。

*地区*标识符曾经仅限于 2 个字母的 ISO 3166 代码，但现在您也可以在语言标签的地区部分使用联合国 3 位宏地理区域代码。宏观地理区域标识包括一个以上国家的更大区域。例如，联合国目前将`Eastern Europe`定义为宏观区域`151`，其中包括 10 个国家。

最后，您可以使用*变体*、*扩展*和*私有*子标签为语言标签提供更多的上下文。请参见 [RFC 5646](https://medium.com/r/?url=https%3A%2F%2Ftools.ietf.org%2Fhtml%2Frfc5646) 了解更多详情。如果您需要使用这种级别的细节，我建议您也使用`Locale.Builder`类来辅助。

查看一下[语言环境文档](https://medium.com/r/?url=http%3A%2F%2Fdocs.oracle.com%2Fjavase%2F8%2Fdocs%2Fapi%2Fjava%2Futil%2FLocale.html)，了解使用这些新特性的所有细节。它们无疑让您对如何在国际化应用程序中识别和使用语言资源有了更多的控制。