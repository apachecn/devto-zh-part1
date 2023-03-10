# 通过自定义 podspec 集成库

> 原文：<https://dev.to/onmyway133/integrating-library-via-custom-podspec-941>

原帖[https://github.com/onmyway133/blog/issues/81](https://github.com/onmyway133/blog/issues/81)

今天我将要集成一个还不支持 Cocoapods 的库。手动做的话会比较繁琐，因为你要配置`xcconfig`、`framework search path`、`assets`，这些步骤没有很好的文档记录。

您可以使用自定义的 podspec 来实现这一点。在我的情况下，我需要安装 [PinchSDK](https://bitbucket.org/fluxloop/pinch.installpackage/wiki/Home) 。首先，在项目文件夹中声明一个`PinchSDK.podspec`。

```
Pod::Spec.new do |s|
  s.name             = "PinchSDK"
  s.version          = "1.9.14"
  s.summary          = "Pinch samler dessuten inn data hver gang en mobilapplikasjon oppdager en Pinch-beacon."
  s.homepage         = "https://bitbucket.org/fluxloop/pinch.installpackage"
  s.source           = { :http => "https://bitbucket.org/fluxloop/pinch.installpackage/raw/master/iOS/PinchSDK.zip" }
  s.authors = 'Fluxloop'
  s.license = { type: 'MIT' }
  s.platform     = :ios, '8.0'
  s.requires_arc = true
  s.resource = 'PinchSDK/Pinch.bundle'
  s.vendored_frameworks = 'PinchSDK/PinchLibrary.framework'
  s.xcconfig = { 'OTHER_LDFLAGS': '-ObjC' }
  s.public_header_files = 'PinchSDK/PinchLibrary.framework/Headers/PinchLibrary.h'
  s.source_files = 'PinchSDK/PinchLibrary.framework/Headers/PinchLibrary.h'
end 
```

Enter fullscreen mode Exit fullscreen mode

然后，在你的`Podfile`中，你可以指向这个`podspec`

```
pod 'PinchSDK', podspec: 'PinchSDK.podspec' 
```

Enter fullscreen mode Exit fullscreen mode

最后，由于这个 PinchSDK 使用了`objc`，你需要在你的`bridging header`
中声明它

```
#import <PinchLibrary/PinchLibrary.h> 
```

Enter fullscreen mode Exit fullscreen mode

现在，只要`pod install`你就完成了ðÿž‰