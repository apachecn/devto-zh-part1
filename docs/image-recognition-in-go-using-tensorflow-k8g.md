# 基于张量流的围棋图像识别

> 原文：<https://dev.to/plutov/image-recognition-in-go-using-tensorflow-k8g>

[![image_recognition_in_go_using_tensorflow](img/de3b282b3ad67d6b357f367bb416a1a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--01dRUl3k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://pliutau.com/IRiGuTF.jpg)

这是这个视频的文字版:[package main # 4:Go 中使用 Tensorflow 的图像识别](https://youtu.be/P8MZ1Z2LHrw)。

Tensorflow 是一个用图形表示计算的计算库。它的核心是用 C++实现的，也有不同语言的绑定，包括 Go。

在过去的几年中，机器学习领域在解决图像识别的难题方面取得了巨大的进展。

机器学习的挑战之一是弄清楚如何将训练好的模型部署到生产环境中。训练完模型后，您可以“冻结”它并将其导出以在生产环境中使用。

对于一些常见的用例，我们开始看到组织共享他们训练过的模型，你可以在 [TensorFlow 模型报告](https://github.com/tensorflow/models)中找到一些。

在本文中，我们将使用其中的一个，称为 [Inception](https://github.com/tensorflow/models/tree/master/research/inception/inception) 来识别图像。

我们将构建一个小的命令行应用程序，它将图像的 URL 作为输入，并按顺序输出标签。

首先，我们需要安装 TensorFlow，Docker 在这里非常有用，因为 Tensorflow 的安装可能很复杂。有一个带 Tensorflow 的 Docker 图像，但是没有 Go，所以我找了一个带 Tensorflow 加 Go 的图像来缩小 Dockerfile。

```
FROM ctava/tensorflow-go

RUN mkdir -p /model && \
  curl -o /model/inception5h.zip -s "http://download.tensorflow.org/models/inception5h.zip" && \
  unzip /model/inception5h.zip -d /model

WORKDIR /go/src/imgrecognition
COPY . .
RUN go build
ENTRYPOINT [ "/go/src/imgrecognition/imgrecognition" ] 
```

Enter fullscreen mode Exit fullscreen mode

让我们从简单的 main.go 开始，它将解析命令行参数并从 URL:
下载图像

```
func main() {
    os.Setenv("TF_CPP_MIN_LOG_LEVEL", "2")

    if len(os.Args) < 2 {
        log.Fatalf("usage: imgrecognition <image_url>")
    }
    fmt.Printf("url: %s\n", os.Args[1])

    // Get image from URL
    response, e := http.Get(os.Args[1])
    if e != nil {
        log.Fatalf("unable to get image from url: %v", e)
    }
    defer response.Body.Close()
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以构建并运行我们的程序:

```
docker build -t imgrecognition .
docker run imgrecognition https://www.iaspaper.net/wp-content/uploads/2017/10/Rabbit-Essay.jpg 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要加载我们的模型。模型包含两个文件中的图形和标签:

```
const (
    graphFile = "/model/imagenet_comp_graph_label_strings.txt"
    labelsFile = "/model/imagenet_comp_graph_label_strings.txt"
)

graph, labels, err := loadModel()
if err != nil {
    log.Fatalf("unable to load model: %v", err)
}

func loadModel() (*tf.Graph, []string, error) {
    // Load inception model
    model, err := ioutil.ReadFile(graphFile)
    if err != nil {
        return nil, nil, err
    }
    graph := tf.NewGraph()
    if err := graph.Import(model, ""); err != nil {
        return nil, nil, err
    }

    // Load labels
    labelsFile, err := os.Open()
    if err != nil {
        return nil, nil, err
    }
    defer labelsFile.Close()
    scanner := bufio.NewScanner(labelsFile)
    var labels []string
    for scanner.Scan() {
        labels = append(labels, scanner.Text())
    }

    return graph, labels, scanner.Err()
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们终于可以开始使用 tensorflow Go 软件包了。为了能够处理我们的图像，我们需要对它进行规范化，因为 Inception model 希望它采用某种格式，它使用来自 ImageNet 的图像，它们是 224x224。但这有点棘手。我们来看看:

```
func normalizeImage(body io.ReadCloser) (*tensorflow.Tensor, error) {
    var buf bytes.Buffer
    io.Copy(&buf, body)

    tensor, err := tensorflow.NewTensor(buf.String())
    if err != nil {
        return nil, err
    }

    graph, input, output, err := getNormalizedGraph()
    if err != nil {
        return nil, err
    }

    session, err := tensorflow.NewSession(graph, nil)
    if err != nil {
        return nil, err
    }

    normalized, err := session.Run(
        map[tensorflow.Output]*tensorflow.Tensor{
            input: tensor,
        },
        []tensorflow.Output{
            output,
        },
        nil)
    if err != nil {
        return nil, err
    }

    return normalized[0], nil
}

// Creates a graph to decode, rezise and normalize an image
func getNormalizedGraph() (graph *tensorflow.Graph, input, output tensorflow.Output, err error) {
    s := op.NewScope()
    input = op.Placeholder(s, tensorflow.String)
    // 3 return RGB image
    decode := op.DecodeJpeg(s, input, op.DecodeJpegChannels(3))

    // Sub: returns x - y element-wise
    output = op.Sub(s,
        // make it 224x224: inception specific
        op.ResizeBilinear(s,
            // inserts a dimension of 1 into a tensor's shape.
            op.ExpandDims(s,
                // cast image to float type
                op.Cast(s, decode, tensorflow.Float),
                op.Const(s.SubScope("make_batch"), int32(0))),
            op.Const(s.SubScope("size"), []int32{224, 224})),
        // mean = 117: inception specific
        op.Const(s.SubScope("mean"), float32(117)))
    graph, err = s.Finalize()

    return graph, input, output, err
} 
```

Enter fullscreen mode Exit fullscreen mode

TensorFlow Go 中的所有操作都是用会话完成的，所以我们需要初始化、运行和关闭它们。在 makeTransformImageGraph 中，我们定义了规范化的规则。

我们需要在初始模型图上再初始化一个会话来寻找匹配:

```
// Create a session for inference over modelGraph.
session, err := tf.NewSession(modelGraph, nil)
if err != nil {
    log.Fatalf("could not init session: %v", err)
}
defer session.Close()

output, err := session.Run(
    map[tf.Output]*tf.Tensor{
        modelGraph.Operation("input").Output(0): tensor,
    },
    []tf.Output{
        modelGraph.Operation("output").Output(0),
    },
    nil)
if err != nil {
    log.Fatalf("could not run inference: %v", err)
} 
```

Enter fullscreen mode Exit fullscreen mode

它将返回每个标签的概率列表。我们现在需要的是循环遍历所有概率，并在`labels`切片中找到标签。并打印前 5 名。

```
res := getTopFiveLabels(labels, output[0].Value().([][]float32)[0])
for _, l := range res {
    fmt.Printf("label: %s, probability: %.2f%%\n", l.Label, l.Probability*100)
}

func getTopFiveLabels(labels []string, probabilities []float32) []Label {
    var resultLabels []Label
    for i, p := range probabilities {
        if i >= len(labels) {
            break
        }
        resultLabels = append(resultLabels, Label{Label: labels[i], Probability: p})
    }

    sort.Sort(Labels(resultLabels))
    return resultLabels[:5]
} 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们能够测试我们的图像，并找到程序会说什么:

```
docker build -t imgrecognition .
docker run imgrecognition https://www.iaspaper.net/wp-content/uploads/2017/10/Rabbit-Essay.jpg
label: rabbit, probability: 86.72%
... 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们使用预训练的模型，但也可以在 TensorFlow 中从 Go 训练我们的模型，我肯定会做一个关于它的视频。

[该程序的完整代码](https://github.com/plutov/packagemain/tree/master/04-tensorflow-image-recognition)