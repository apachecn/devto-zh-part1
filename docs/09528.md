# 我如何将我的 vocabs 从 Kindle Paperwhite 转移到 Anki

> 原文：<https://dev.to/psamim/how-i-transfer-my-vocabs-from-my-kindle-paperwhite-to-anki-1f4d>

我在 Kindle Paperwhite 上读了很多书，智人能发明多少单词真的让我很惊讶。当我在字典中查找一个新单词，并在我的母语中看到对应的单词时，我想知道我在哪里学到了这个罕见的单词，甚至想知道我上次听到它是什么时候。尽管如此，我完全理解这个词及其背后的概念。智人很神奇。

我想不受干扰地阅读，把新单词留到以后用，并把它们添加到我的笔记本中，这样我就可以在空闲时间用手机或者在健身房复习它们。

这是我的做法。

正如你所知，当你在 Kindle 中查找一个单词时，它会自动保存在一个叫做“词汇生成器”的东西中，可以从设备右上角的菜单中访问。这些单词实际上和它周围的句子一起保存在设备的 SQLite 数据库中。如果通过 USB 连接设备，数据库文件位于`system/vocabulary/vocab.db`。

然后[我有一个 Python 脚本](https://github.com/psamim/kindle2anki)来读取这个数据库，交互地询问单词的意思，并将它们添加到 Anki deck 中。我偶尔会将数据库文件复制到我的 PC 上，并在终端中运行下面的脚本。

```
#!/bin/bash

KINDLE=~/Backup/vocab.db
COLLECTION=~/Documents/Anki/User\ 1/collection.anki2
DECK=Words

kindle2anki --kindle $KINDLE --collection $COLLECTION --deck $DECK --clipboard 
```

如果你属于那个部落，我已经把剧本放在 AUR 了。