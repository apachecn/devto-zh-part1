# 在 Windows 上调用由 Go from mruby 创建的库，并在命令提示符下显示网络摄像机的图像

> 原文：<https://dev.to/kjunichi/called-the-library-created-by-go-from-mruby-on-windows-and-displayed-the-image-of-the-web-camera-at-the-command-prompt-chb>

# 背景

我用 Go 语言创建了一个库，在 Windows 命令提示符下指定文件和 URL，并在命令提示符下显示图像

# 准备

Go 端输入 jpeg 和 png 作为输入源，它会原样通过文件从 mruby 调用，担心 SSD w 的负担，当然速度也是。

## 把从 C 传来的起始地址当作一个 Go 片

我找到了这个链接:

*   [https://github.com/golang/go/issues/13656](https://github.com/golang/go/issues/13656)

显然，

*   [https://github . com/golang/Go/wiki/CGO # Turning _ C _ arrays _ into _ Go _ slices](https://github.com/golang/go/wiki/cgo#Turning_C_arrays_into_Go_slices)

写起来好像还不错(容量最多 2 GB 好像有限制..)

```
func termPutImage(imageData *C.uchar, width, height C.int) {
    len := 3 * width * height
    slice := (*[1 << 30]C.uchar)(unsafe.Pointer(imageData))[:len:len] 
```

Enter fullscreen mode Exit fullscreen mode

## 转换为图像类型的 Go 语言

如下所示，我们能够将从 C 端接收的数组(PPM 的 P6 格式)转换为图像类型。

```
img := image.NewRGBA(image.Rect(0, 0, int(width), int(height)))
for h := 0; h < int(height); h++ {
    for w := 0; w < int(width); w++ {
        r := uint8(slice[3*(h*int(width)+w)])
        g := uint8(slice[3*(h*int(width)+w)+1])
        b := uint8(slice[3*(h*int(width)+w)+2])
        c := color.RGBA{r, g, b, 0}
        img.SetRGBA(w, h, c)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使 libhogehoge.a 格式为 Go

在 Windows 上，不能创建共享库，但支持归档格式库的输出。

```
go build -buildmode = c-archive -o libimgtype.a main.go 
```

Enter fullscreen mode Exit fullscreen mode

这将在中创建一个库。一种格式。对于 MinGW 的 gcc 环境，您可以立即使用它。

## 使 MSVC·mruby 可用创建一个 lib 文件和链接

我们这次要用的名为 [mruby-webcam](https://github.com/kjunichi/mruby-webcam) 的 mrbgem 用的是 OpenCV 的 MSVC 版本，根据它的说法，我们会用 MSVC。

因此，准备一个 def 文件，并从该文件和。前一个(存档)格式的库。此外，生成必要的。lib 文件来自。在 MSVC 处理系统中链接时定义。有必要做一些麻烦的工作。

# 如何制作一个 dll

DLL 与 gcc 的 MinGW 版本一起提供(gcc 由 cgo 指定)。

```
gcc -m64 -shared -o imgtype.dll imgtype.def libimgtype.a -Wl,--allow-multiple-definition -static -lwinmm -lntdll -lWs2_32 
```

Enter fullscreen mode Exit fullscreen mode

# 如何创建一个 lib 文件

的。lib 文件是用附加到 MSVC 的 lib 命令完成的。

```
lib /machine:x64 /def:imgtype.def 
```

Enter fullscreen mode Exit fullscreen mode

现在我已经准备好融入 mruby 穆鲁比了。

*   [https://github.com/kjunichi/libimgtype](https://github.com/kjunichi/libimgtype)

# 从 mruby 调用 Go

如果你来这里，你只写 mrbgem。编写创建的。mrbgem.rake 中的 Lib 文件如下:

```
spec.linker.flags_before_libraries << "imgtype.lib" 
```

Enter fullscreen mode Exit fullscreen mode

您可以将 imgtype.lib 文件的路径传递给环境变量 lib，mruby.exe 和 mirb.exe 可以通过将 Go 库与 rake 合并来创建。

# 运行

此 mrb gem 要求如下:

```
conf.gembox 'default'
conf.gem '../mruby-webcam'
conf.gem '../mruby-imgtype'
conf.gem :github => 'matsumoto-r/mruby-sleep' 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个 mruby 脚本，它在命令提示符或 Vim:终端显示网络摄像头图像。

```
Imgtype.init

cam = Webcam.new
cam.setFmt "ppm"
cam.capture {|img|
  Imgtype.imgtype img
}
while true
    cam.snap
    Sleep::usleep(1000)
    if Imgtype.get_key == "ESC" then
        break
    end
end
Imgtype.close 
```

Enter fullscreen mode Exit fullscreen mode

# 结果

Imgtype.get_key 直接读取 Windows API 并响应。我很惊讶在没有轮询的情况下得到键输入。

*   [https://t.co/gtYXg0tOA3](https://t.co/gtYXg0tOA3)