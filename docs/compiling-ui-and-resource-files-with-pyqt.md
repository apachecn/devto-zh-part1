# 用 PyQt 编译 UI 和资源文件

> 原文：<https://dev.to/martinplicka/compiling-ui-and-resource-files-with-pyqt>

> 这篇文章最初发表在几年前的 https://mplicka . cz/en/blog/compilating-ui-and-resource-files-with-pyqt 上，是关于 Qt4/PyQt4 的。由于它仍然有一些来自谷歌搜索的点击量，一些人可能会发现它很有用。

PyQt 是流行的 Qt 库的 python 绑定(最近变成了 LGPLed 用于非商业目的)。如果使用 Qt Designer，您必须将 UI 或 Qt 资源文件的 XML 描述编译成 Python 代码，以便在您的应用程序中使用它们。

在 C++中，你可以很容易地使用`qmake`工具或 Automake 来创建合适的 Makefile。对于 Python 来说，不需要 Makefile，因为 Python 在将模块加载到 VM 时编译它们。有了 PyQt，您可以使用`uic`模块来编译 ui 文件或者将它们作为 Python 类加载。

或者，您可以像使用合适的 Qt 工具一样使用`pyuic4`和`pyrcc4`命令行工具(`uic`、`rcc`)。我就是用这种方式产生 Python 代码的。两种工具的用法相似:

```
#compile ui file from Qt Designer
pyuic4 ui_file.ui -o compiled_ui_file.py
#compile resource file (icons, etc..)
pyrcc4 resource_file.qrc -o compiled_resource_file.py 
```

Enter fullscreen mode Exit fullscreen mode

以前，我用一个 shell 脚本一次性编译 project 中的所有资源文件(因为我太懒了)。这种解决方案有两个主要缺点。首先，编译所有资源比只编译修改过的文件花费更多的时间。这随项目中的资源文件数量而变化。第二个更让我讨厌。因为我也用 Mercurial 跟踪 UI 和资源文件的编译版本(还没有特别的原因)，所以它总是创建新的变更集，因为“pyuic4”工具将关于编译时间的信息添加到 Python 代码注释中。所以我更新了我的 Makefile 知识(也使用了一些旧的代码片段)并创建了下面的 Makefile 来分别编译 UI 和资源文件。

```
###### EDIT ##################### 
#Directory with ui and resource files RESOURCE_DIR = src/resources

#Directory for compiled resources COMPILED_DIR = src/ui

#UI files to compile UI_FILES = confirm.ui main.ui repair.ui settings.ui statistics.ui
#Qt resource files to compile RESOURCES = images.qrc 

#pyuic4 and pyrcc4 binaries PYUIC = pyuic4.bat
PYRCC = pyrcc4

#################################
# DO NOT EDIT FOLLOWING 
COMPILED_UI = $(UI_FILES:%.ui=$(COMPILED_DIR)/ui_%.py)
COMPILED_RESOURCES = $(RESOURCES:%.qrc=$(COMPILED_DIR)/%_rc.py)

all : resources ui 

resources : $(COMPILED_RESOURCES) 

ui : $(COMPILED_UI)

$(COMPILED_DIR)/ui_%.py : $(RESOURCE_DIR)/%.ui
    $(PYUIC) $< -o $@

$(COMPILED_DIR)/%_rc.py : $(RESOURCE_DIR)/%.qrc
    $(PYRCC) $< -o $@

clean : 
    $(RM) $(COMPILED_UI) $(COMPILED_RESOURCES) $(COMPILED_UI:.py=.pyc) $(COMPILED_RESOURCES:.py=.pyc) 
```

Enter fullscreen mode Exit fullscreen mode

它与 GNU Make(来自 Windows 上的 Cygwin 或 MigGW)一起工作。它以下列方式编译文件:

*   window.ui - > ui_window.py (GUI 定义)
*   images.qrc - > images_rc.py(参考资料)

现在你只需在控制台中输入`make`就可以轻松编译修改过的文件。确保您的当前工作目录包含如上所示的 Makefile。