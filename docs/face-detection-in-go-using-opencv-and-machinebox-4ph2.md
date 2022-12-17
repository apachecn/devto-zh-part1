# 基于 OpenCV 和 MachineBox 的围棋人脸检测

> 原文：<https://dev.to/plutov/face-detection-in-go-using-opencv-and-machinebox-4ph2>

[![Face Detection in Go using OpenCV and MachineBox](img/92cb7768fb7c94fe2110685cd960f0f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--McCm7t6z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pliutau/DETECT.jpg)

这是这个视频的文字版: [packagemain #5:使用 OpenCV 和 MachineBox 的围棋人脸检测](https://youtu.be/rbZeZNVA-Q4)。

我发现了一个非常好的开发人员友好的项目 [MachineBox](https://machinebox.io/) ，它在 Docker 容器内提供了一些机器学习工具，包括人脸检测、自然语言理解等等。它在 Go 中有 SDK，所以我们将建立一个程序来检测我的脸。我们还将使用 OpenCV 从网络摄像头捕捉视频，它也有 Go 绑定。

通过运行 Docker 容器可以非常容易地安装 MachineBox。首先我们需要在 [machinebox.io](https://machinebox.io/) 上注册并获得密钥，然后我们可以设置环境变量`MB_KEY`并在端口 8080:
上运行 facebox

```
export MB_KEY="YOUR_MB_KEY"
docker run -d -p 8080:8080 -e "MB_KEY=$MB_KEY" machinebox/facebox 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们转到 [localhost:8080](http://localhost:8080) 并验证它是否准备好了，现在我们可以从我们的 go 程序中使用 Go SDK 与 facebox 进行通信。

为了能够从网络摄像头捕捉视频和识别人脸，我们必须安装 OpenCV。我使用的是 GoCV.io，它为 OpenCV 提供了 Go 绑定。安装可能很复杂，但它在我装有 OpenCV3 的 Mac 上运行良好。我们也去打包吧。

```
brew install opencv3
go get gocv.io/x/gocv 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要设置一些环境变量，我们可以使用来自 gocv 包 repo 的`env.sh`:

```
cd $GOPATH/src/gocv.io/x/gocv
source env.sh 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们创建一个 main.go 文件并从网络摄像机:

```
package main

import (
    "log"

    "gocv.io/x/gocv"
)

func main() {
    // open webcam. 0 is the default device ID, change it if your device ID is different
    webcam, err := gocv.VideoCaptureDevice(0)
    if err != nil {
        log.Fatalf("error opening web cam: %v", err)
    }
    defer webcam.Close()

    // prepare image matrix
    img := gocv.NewMat()
    defer img.Close()

    // open display window
    window := gocv.NewWindow("packagemain")
    defer window.Close()

    for {
        if ok := webcam.Read(img); !ok || img.Empty() {
            log.Print("cannot read webcam")
            continue
        }

        // show the image in the window, and wait 100ms
        window.IMShow(img)
        window.WaitKey(100)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 OpenCV，我们还可以使用分类器来识别图像上的人脸。让我们这样做，为此我们将使用哈尔级联分类器。我已经下载了这个 XML 文件来使用，你可以在 GoCV 资源库中找到它。使用 GoCV，还可以为每个面绘制矩形来突出显示它。

```
var (
    blue          = color.RGBA{0, 0, 255, 0}
    faceAlgorithm = "haarcascade_frontalface_default.xml"
)

...

// load classifier to recognize faces
classifier := gocv.NewCascadeClassifier()
classifier.Load(faceAlgorithm)
defer classifier.Close()

for {
    //...

    // detect faces
    rects := classifier.DetectMultiScale(img)
    for _, r := range rects {
        // Save each found face into the file
        imgFace := img.Region(r)
        imgFace.Close()

        // draw rectangle for the face
        size := gocv.GetTextSize("I don't know you", gocv.FontHersheyPlain, 3, 2)
        pt := image.Pt(r.Min.X+(r.Min.X/2)-(size.X/2), r.Min.Y-2)
        gocv.PutText(img, "I don't know you", pt, gocv.FontHersheyPlain, 3, blue, 2)
        gocv.Rectangle(img, r, blue, 3)
    }

    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

为了能够识别人脸，我们需要训练我们的模型，在 Facebox 中这很容易，我们只需上传一些图像并设置名称。我会把我的脸保存到一个文件夹里。

```
imgName := fmt.Sprintf("%d.jpg", time.Now().UnixNano())
gocv.IMWrite(imgName, imgFace) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们来看一下 [localhost:8080](http://localhost:8080) 。训练 Facebox 有几个选择:我们可以用 API，可以用网页或者可以用 Go SDK。

对于这个例子，最简单的方法是使用网页中的表单。

现在，在我们训练它之后，我们可以检查 Facebox 是否可以在图像上找到我的脸，并且我们可以打印我的名字。

```
fbox = facebox.New("http://localhost:8080") 
```

Enter fullscreen mode Exit fullscreen mode

GoCV 没有获取图像的选项，所以我们将打开我们保存的文件。

```
buf, err := gocv.IMEncode("jpg", imgFace)
if err != nil {
    log.Printf("unable to encode matrix: %v", err)
    continue
}

faces, err := fbox.Check(bytes.NewReader(buf))
f.Close()
if err != nil {
    log.Printf("unable to recognize face: %v", err)
}

var caption = "I don't know you"
if len(faces) > 0 {
    caption = fmt.Sprintf("I know you %s", faces[0].Name)
} 
```

Enter fullscreen mode Exit fullscreen mode

这不是广告，但我真的认为 MachineBox 是一个很好的项目，可以在简单的 docker 容器中为您提供一些机器学习能力。

[GitHub 上该程序的完整代码](https://github.com/plutov/packagemain/tree/master/05-gocv-machinebox)