# 微观决策

> 原文：<https://dev.to/rpalo/microdecisions>

这篇文章的背景实际上主要来自我的机械工程经验，但是我编程越多，我就越开始发现设计零件和设计软件之间有很多相似之处。我今天想写的是*微决策*的重要性。

场景:要求您编写一个函数，该函数接受一个字符串并用“x”替换所有元音字母。为什么？因为经理让你这么做。以后会派上用场的。别问问题了。这是第一次切割。

```
def vowels_to_x(phrase):
    """Replaces all of the vowels with 'x' and returns the new string"""
    result = ""
    for letter in phrase:
        if letter in (a, e, i, o, u, A, E, I, O, U):
            result += "x"
        else:
            result += letter
    return result 
```

这是一个非常简单的函数，有一个非常简单的解决方案——事实上，如此简单，以至于你必须做出的大多数决定都显得微不足道。但这是另一个切口，做得不同。

```
VOWELS = "aeiouAEIOU"

def vowels_to_x(phrase):
    """Replaces all of the vowels with 'x' and returns the new string"""
    result_list = []
    for character in phrase:
        if character in VOWELS:
            result_list.append("x")
        else:
            result_list.append(character)
    return "".join(result_list) 
```

以下是一些决策变化:

1)将元音提取到外部常量中以提高可读性。
2)把元音元组收缩成一个字符串，因为这样更容易阅读，不那么分散。
3)将结果构建为列表，而不是字符串。这样你就不必用每一个新字母来重现结果。如果短语最终有几千个字符，这可能会有所帮助。
4)将变量`letter`改为`character`，考虑到它可能是一个空格或标点符号，这样更准确。

这里还有一个选择:

```
VOWELS = "aeiouAEIOU"

def vowels_to_x(phrase):
    """Replaces all of the vowels with 'x' and returns the new string"""
    result_list = ["x" if character in VOWELS else character
                for character in phrase]
    return "".join(result_list) 
```

这个基本上和上面的一样，但是它被浓缩成一个列表理解。可以说，这更“pythonic 化”，也可能更容易阅读。我不能 1000%确定我更喜欢这个，但是它是一个选择。

那么，你选哪个？“有关系吗？”你问。“这是一个愚蠢的功能。随便挑一个。”我是这么想的。

我认为那些无关紧要的微小决定实际上是一些最重要的决定。如果没有理由选择一个版本而不是另一个，那就花时间找个理由选择一个。这有几个好处。首先，如果有人来问你的代码，你会准备好答案，并能够为你的决定辩护。第二，对你的每个决定进行推理的过程会帮助你发现错误。最后，如果你对你选择的每件事都有一个理由，它将会最小化下一个人看你的代码的 WTF 时刻。他们不太可能大喊“为什么”!$%他们会那样做吗！?"更可能平静地说，“我明白他们为什么这么做了。我可能不同意，但这是有道理的。”

当有疑问时，试着从下一个人的角度来看。试着走最不令人惊讶的路。你可以用变量和函数的名字做很多这样的事情。好的名字会帮助其他人凭直觉理解你的设计意图，如果他们能做到这一点，他们会更有可能同情你和你的选择，而不会马上认为你不称职。其他想法，看一下[《Python 之禅》](https://www.python.org/dev/peps/pep-0020/)。当有很多相似的选项时，有很多东西会帮助你选择一个东西而不是另一个。

让我知道你是否能想到任何其他简单的方法来使微决策更容易，更清楚地传达设计意图！

> “小细节至关重要。小事成就大事。”
> 约翰·伍登