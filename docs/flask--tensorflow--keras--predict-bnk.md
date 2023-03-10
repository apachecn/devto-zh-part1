# 在 Flask + Tensorflow + Keras 中来自模型的 predict 无法正常工作的问题

> 原文：<https://dev.to/yurfuwa/flask--tensorflow--keras--predict-bnk>

## 使用机器学习库的 predict API 开发

在 Web 服务中利用 AI 时，大多是以 microservices 方式在 VPC 内独立部署 API，用于内部。 特别是 Chainer 和 Tensorflow 这些机器学习系统的库，由于从其历史背景和数学库来看与 Python 很投缘，所以必然会选择 Python 的 WAF。

## pythonでmicroservicesなapiでのwaf

在 Python 上开发 web APP 应用程序时，Django、Flask 这两大巨头作为 WAF 正在崛起，但在推论大致简单的 API 开发中，Django 却显得格外冗长，附带了 Flask，让人觉得有些轻浮。

## 多线程中的模型数据的参照问题

无论是 Tensorflow 还是 Chainer，API 中的推理都需要读取作为机器学习成果而得到的模型。
在 Tensorflow + Keras 中，服务器启动时事先在内存中展开模型，构筑图表。 在请求时变成利用它的形式。

此时，如果在多线程上运行的话，由于会在与构建的图表线程不同的线程上接收请求，所以有必要在线程之间共享图表

```
 global thread:

        self.model = load_model(model_path)
        self.model._make_predict_function()
        self.graph = tf.get_default_graph()

another thread:

        with self.graph.as_default():
            labels = self.model.predict(data) 
```

Enter fullscreen mode Exit fullscreen mode

*   参见:[https://github.com/fchollet/keras/issues/2397](https://github.com/fchollet/keras/issues/2397)

## 无服务器架构中 Tensorflow 的利用

虽然是最近流行的无服务器软件，但 GCP Cloudfunction 原本就只能使用 nodejs，所以除外，AWS Lambda 是一种选择。

> Tensorflow 逐渐变大，但学习暂且不论，想在识别时只切出必要的最低限度的模块
> 
> — ふわこ ([@yurfuwa](https://dev.to/yurfuwa) ) [2017 年 11 月 8 日](https://twitter.com/yurfuwa/status/928094652288221184?ref_src=twsrc%5Etfw)

有这样的需求。 但是 AWS Lambda 有 50MB 限制，在一个 Lambda 函数中容量限制很严格。

> 在 Serverless 上拼命想做 Tensorflow，但是别说 Tensorflow 了，numpy 啊 scipy 啊都已经太重了，会死于 aws lambda，就算把. strip 或者. pyc 关掉也是杯水车薪
> 
> — ふわこ ([@yurfuwa](https://dev.to/yurfuwa) ) [2017 年 10 月 26 日](https://twitter.com/yurfuwa/status/923608036609900544?ref_src=twsrc%5Etfw)

坦率地放弃了无服务器，在 GCP AppEngine 附近采取措施似乎是打倒。
AWS 先生，能想想办法吗，这个。