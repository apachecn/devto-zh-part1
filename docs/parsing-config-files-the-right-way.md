# 正确解析配置文件

> 原文：<https://dev.to/dmerejkowsky/parsing-config-files-the-right-way>

*首发于[我的博客](https://dmerej.info/blog/post/parsing-config-files-the-right-way/)*

解析配置文件是我们程序员每天都要做的事情。但是你确定你这样做是正确的吗？

让我们来了解一下！

在本文的其余部分，我们将假设我们想要在一个名为`frob`的命令行程序中解析一个包含 github 访问令牌的配置文件。

# Javascript 中的

你可以这样写:

```
/* in config.json */
{
  "auth":
  {
    "github":
    {
      "token": "ab642ef9zf"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
/* in frob.js */
const config = require('./config');
const token = config.auth.github.token;
... 
```

Enter fullscreen mode Exit fullscreen mode

嗯，这是假设我们正在使用`node`。在浏览器或任何其他 Javascript 环境中实现这一点是留给读者的练习:)

不过，上述方法有几个问题。为了解释它们，我们将切换到我更熟悉的语言，并查看问题和潜在解决方案的列表。

# 语法

首先，对配置文件使用`JSON`可能不是一个好主意。所以我们要用 YAML 来代替。以下是几个原因:

*   像 JSON 一样，我们可以直接映射到“普通旧数据”Python 类型(列表、字典、整数、浮点、字符串和布尔值)

*   语法是明确定义的，所有实现的行为都是一样的。(JSON 的情况*不是*，详见[解析 JSON 是雷区](http://seriot.ch/parsing_json.php))

*   我们可以在配置文件中添加注释。

*   文件对人类来说更容易阅读。比较:

```
{  "auth":  {  "github":  {  "token":  "ab642ef9zf"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

```
auth:
  github:
    token:  "ab642ef9zf" 
```

Enter fullscreen mode Exit fullscreen mode

*   元素可以任意嵌套。(`.ini`文件只有一级“节”，而`.toml`只有两级)

*   同样的数据有几种表达方式，所以我们可以选择可读性更好的:

```
shopping_list:
 - eggs
 - bacon
 - tomatoes
 - beans

tags: ["python", "testing"] 
```

Enter fullscreen mode Exit fullscreen mode

*   空白很重要，所以文件*有*要适当缩进。

# 位置

第二，`config.json`文件被硬编码到紧靠源代码的位置。

这意味着如果我们不小心的话，它可能会被添加到版本控制系统中。

因此，我们将尝试与免费桌面标准兼容。

基本上，这意味着我们应该:

*   如果设置了 XDG _ 配置 _ 主页环境变量，在`$XDG_CONFIG_HOME/frob.yml`中查找配置文件。
*   如果没有，请在`~/.config/frob.yml`中查找
*   如果在家中找不到，请在`/etc/xdg/frob.yml`中查找默认值

这样做将有助于我们遵循最小惊讶原则，因为，由于今天许多程序遵循这些规则，我们实现的用户将会期望我们也这样做。

幸运的是，我们不必实现所有这些，我们可以使用`pyxdg`库:

```
import xdg.BaseDirectory

cfg_path = xdg.BaseDirectory.load_first_config("frob.yml")
if cfg_path:
   ... 
```

Enter fullscreen mode Exit fullscreen mode

# 错误处理

有时文件根本不存在，所以我们想通知我们的用户:

```
cfg_path = xdg.BaseDirectory.load_first_config("frob.yml")

if not cfg_path:
    raise InvalidConfig("frob.yml not found") 
```

Enter fullscreen mode Exit fullscreen mode

有时文件会存在，但是`read_text()`会因为某种原因(比如权限问题)而失败:

```
import pathlib

try:
   config_file = pathlib.Path(cfg_path)
   contents = config_file.read_text()
except OSError as read_error:
    raise InvalidConfig(f"Could not read file {cfg_path}: {read_error}") 
```

Enter fullscreen mode Exit fullscreen mode

有时文件会存在，但会包含无效的 YAML:

```
import ruamel.yaml

contents = config_file.read_text()
try:
    parsed = ruamel.yaml.safe_load(contents)
except ruamel.yaml.error.YAMLError as yaml_error:
    details = format_error(yaml_error.context_mark.line, yaml_error.context_mark.column)
    message = f"{cfg_path}: YAML error: {details}"
    raise InvalidConfig(message) 
```

Enter fullscreen mode Exit fullscreen mode

# 图式

这就是事情变得棘手的地方。如果文件存在，可读，包含有效的 YAML 代码，但用户在编写时打错了，该怎么办？

这里有几个我们应该处理的情况:

```
# empty config: no error

# `auth` section is here but does not contain
# a `github` entry: no error
auth:
  gitlab:
    ...

# `auth.github` section is here but does not
# contain `token`, this is an error:
auth:
  github:
    tken: "ab642ef9zf" 
```

Enter fullscreen mode Exit fullscreen mode

处理这个问题的一个简单方法是编写这样的代码:

```
parsed = ruamel.yaml.safe_load(contents)
auth = parsed.get("auth")
if auth:
    github = auth.get("github")
    token = github.get("token")
    if not token:
        raise InvalidConfig("Expecting a key named 'token' in the
                            'github' section of 'auth' config") 
```

Enter fullscreen mode Exit fullscreen mode

这很快就变得乏味了。更好的方法是使用`schema`库:

```
import schema
auth_schema = schema.Schema(
  {
    schema.Optional("auth"):
    {
      schema.Optional("github") :
      {
        "token": str,
      }
    }
  }
)

try:
    auth_schema.validate(parsed)
except schema.SchemaError as schema_error:
    raise InvalidConfig(file_path, schema_error) 
```

Enter fullscreen mode Exit fullscreen mode

# 保存

最后但同样重要的是，有时我们会想要自动保存配置文件。

在这种情况下，重要的是保存的配置文件仍然类似于原始文件。

对于`ruamel.yaml`，这是通过使用`RoundtripLoader`
来完成的

```
def save_token(token):
    contents = config_file.read_text()
    config = ruamel.yaml.load(contents, ruamel.yaml.RoundTripLoader)
    config["auth"]["github"]["token"] = token
    dumped = ruamel.yaml.dump(config, Dumper=ruamel.yaml.RoundTripDumper)
    config_file.write_text(dumped) 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

唷！对于一个看似简单的任务来说，这是一个很大的工作量。但是我确实相信经历所有这些麻烦是值得的:我们覆盖了许多边缘情况，并确保我们总是有非常清楚的错误消息出现。当事情变糟时，这样编写的代码的用户会非常感激。干杯！

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。