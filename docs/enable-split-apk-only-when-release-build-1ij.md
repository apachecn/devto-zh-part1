# 仅在发布版本时启用拆分 apk。

> 原文：<https://dev.to/sys1yagi/enable-split-apk-only-when-release-build-1ij>

在开发过程中，大多数时候你不需要分裂 apk，但不像贝娄工作设置。

```
buildType {
  debug {
    splits {
      abi {
        println ("split settings for debug.")
        enable false
      }
    }
  }

  release { 
    splits {
      abi {
        println ("split settings for release.")
        enable true
        reset()
        include 'armeabi-v7a', 'arm64-v8a', 'x86', 'x86_64'
        universalApk true
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是因为设置过程是顺序执行的。

```
./gradlew assembleDebug
> ...
> split settings for debug.
> split settings for release.
> ... 
```

Enter fullscreen mode Exit fullscreen mode

# 方案 1

您可以在 gradle 脚本上访问 gradle 任务名称。按任务名称控制拆分，如下所示。

```
android {
  splits {
    abi {
      enable gradle.startParameter.taskNames.contains("assembleRelease")
      reset()
      include 'armeabi-v7a', 'arm64-v8a', 'x86', 'x86_64'
      universalApk true
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 方案二

您可以将参数传递给 gradle 脚本。

```
android {
  splits {
    abi {
      enable project.hasProperty("enabledSplitApks")
      reset()
      include 'armeabi-v7a', 'arm64-v8a', 'x86', 'x86_64'
      universalApk true
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

根据需要控制拆分 apk。

```
./gradlew assembleDebug -PenabledSplitApks 
```

Enter fullscreen mode Exit fullscreen mode

它通过省略额外的处理节省了构建时间😄