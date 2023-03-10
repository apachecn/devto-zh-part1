# 在 Makefile 中处理对生成的源文件的依赖

> 原文：<https://dev.to/brthanmathwoag/dealing-with-dependence-on-generated-source-files-in-a-makefile-1b9b>

我想我今天终于做对了。

我有一个静态的网站，网页从减价文件生成。其中一些是手工编写的，但大多数是用 python 脚本从 json 文件中的平面数据库生成的。构建过程是通过 Makefile 自动完成的。

我想只在必要时触发生成器，也就是说，当 json 或脚本被更改时。但是，有太多的降价文档，无法在 Makefile 中硬编码它们的名称。通常，这是通过使用通配符文件掩码来解决的，它枚举源目录中的相关文件名，并在前面加上目标目录的路径:

```
ALL_MDS_IN_OBJ = $(wildcard $(OBJ)/*.md)
ALL_HTML_IN_BIN = $(addprefix $(BIN)/, $(notdir $(ALL_MDS_IN_OBJ:.md=.html)))

default: $(ALL_HTML_IN_BIN)

$(BIN)/%.html: \
    $(OBJ)/%.md \
    | $(BIN)

    ... convert mds to html with pandoc ...

$(OBJ)/%.md: \
    $(SRC)/source_data.json \
    generate-mds-from-json.py \
    | $(OBJ)

    ./generate-mds-from-json.py 
```

Enter fullscreen mode Exit fullscreen mode

问题是，当克隆回购协议或执行`make clean`后第一次运行`make`时，`$(OBJ)`中还没有降价文件。通配符不匹配任何东西，所以`make`愉快地宣布不需要做任何事情并退出。

为了应对这种情况，我在每次更改 json 文件或脚本之前都要手动运行生成器，当然，从长远来看，这是乏味且容易出错的。

然后在 stackoverflow 上看到[这个问题，终于开窍了。](https://stackoverflow.com/questions/991841/makefile-dependency-for-unknown-files-in-known-directory-for-docbook)

正如 [ChrisW](https://stackoverflow.com/questions/991841/makefile-dependency-for-unknown-files-in-known-directory-for-docbook#answer-992029) 解释的那样，可以通过引入对与构建工件分开保存的 sentinel 文件的依赖来强制第一次运行生成器，sentinel 文件的唯一目的是包含上次运行生成器的时间戳；如果生成成功，文件将被触摸，并在`make clean`被删除。

```
MDS_SENTINEL = .mds_sentinel

default: \
    $(MDS_SENTINEL) \
    $(ALL_HTML_IN_BIN) \
    ... other deps ...

$(MDS_SENTINEL): \
    $(SRC)/source_data.json \
    generate-mds-from-json.py

    ./generate-mds-from-json.py \
        && touch $(MDS_SENTINEL)

clean:
    rm -r $(MDS_SENTINEL) $(OBJ)/* $(BIN)/* 
```

Enter fullscreen mode Exit fullscreen mode

但这仍然不够；如上所述，在运行生成器之前，通配符函数仍然会被计算，导致空文件列表和提前退出。有必要调用两次`make`——第一次生成中间文件，第二次进一步处理它们。与`./generate-mds-from-json.py && make`相比，不得不做`make && make`并不是一个巨大的胜利。

幸运的是，保罗·鲁布在同一个线程中解决了这个问题，他建议从菜谱中递归地运行`make`。这个*内部* make 将在所有文件生成后扩展通配符，并正确处理这些文件。

所以最终的解决方案看起来像:

```
MDS_SENTINEL = .mds_sentinel

ALL_MDS_IN_OBJ = $(wildcard $(OBJ)/*.md)
ALL_HTML_IN_BIN = $(addprefix $(BIN)/, $(notdir $(ALL_MDS_IN_OBJ:.md=.html)))

default: \
    $(MDS_SENTINEL) \
    $(ALL_HTML_IN_BIN) \
    ... other deps ...

$(MDS_SENTINEL): \
    $(SRC)/source_data.json \
    generate-mds-from-json.py

    ./generate-mds-from-json.py \
        && touch $(MDS_SENTINEL) \
        && make mds

mds: $(ALL_HTML_IN_BIN)

clean:
    rm -r $(MDS_SENTINEL) $(OBJ)/* $(BIN)/* 
```

Enter fullscreen mode Exit fullscreen mode

*本帖原载于[blog . tznvy . eu](https://blog.tznvy.eu/2017-07-27-dealing-with-dependence-on-generated-source-files-in-a-makefile)T3】*