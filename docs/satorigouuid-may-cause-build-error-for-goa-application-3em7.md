# satori/go.uuid 可能会导致 goa 应用程序的构建错误

> 原文：<https://dev.to/deadcheat/satorigouuid-may-cause-build-error-for-goa-application-3em7>

今天，我向生产部门发布了我的工作项目申请。但是我得到了这样的构建错误，

```
# myapp/vendor/github.com/goadesign/goa/uuid
vendor/github.com/goadesign/goa/uuid/uuid.go:18:23: not enough arguments in call to uuid.Must
    have (uuid.UUID)
    want (uuid.UUID, error) 
```

幸运的是，我偶然在 goa-slack-channel 上看到谈论同样的话题，我知道这个问题是由[这个问题(破坏 API 变化)](https://github.com/satori/go.uuid/issues/66)引起的

当然，他们在主分支上修复了这个问题。但是他们还没有添加发布标签。
所以你这样写“glide.yaml”就会面临这个问题。

```
import:
- package: github.com/goadesign/goa
  version: ^1.3.1
- package: github.com/satori/go.uuid
  version: ^1.2.0 
```

对于我的应用程序，修复 glide.yaml 如下。

```
import:
- package: github.com/goadesign/goa
  version: ^1.3.1
- package: github.com/satori/go.uuid
  version: master 
```

谢了。