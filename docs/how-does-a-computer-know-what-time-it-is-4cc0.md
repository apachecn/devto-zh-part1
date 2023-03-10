# 电脑怎么知道现在是什么时间？

> 原文：<https://dev.to/dotnet/how-does-a-computer-know-what-time-it-is-4cc0>

这是我的网站档案中的一个帖子，但我认为它仍然很有趣！

最近，我和一个客户一起工作，他需要在特定时间处理一些业务流程。那么你怎么知道现在是什么时间呢？我们一起努力，不是吗！

事实证明，以时钟漂移的形式使用它有一个缺陷，想想当你的微波炉和手机不再显示相同的时间时，即使你确定前几天你设置了相同的时间！

在 Windows 上，你可以进入你的日期时间设置，并且有一个与时间服务器同步的选项。默认情况下，这是`time.windows.com`，在我的 IT 职业生涯中，我见过很多这样的对话，但从来没有真正关注过*我的电脑如何知道现在是 12 月 24 日晚上 11 点 59 分*，你呢？所以你的电脑会同步到这个地方，它会定期同步(你可以控制它，但不是很多)，这给我的客户带来了一个问题，时钟漂移可能会给他们带来问题。

## 嗯，几点了？

如果我们正在建立我们的问题，我们需要知道现在是什么时间，非常准确。嗯，有一种非常特殊的时钟，即[原子钟](https://en.wikipedia.org/wiki/Atomic_clock)。因为这些东西有点大，你不能真的把它放在你的桌子上，所以让我们想想下一件事，Windows 如何知道现在是什么时间？这是通过使用原子钟(或类似的精密设备，如 GPS)并通过[网络时间协议(ntp)](https://en.wikipedia.org/wiki/Network_Time_Protocol) 来实现的。你连接到一个 ntp 服务器，然后连接到一个服务器，直到你点击一个原子钟，或一个**地层 0** 时间仪器。

ntp 是一个非常简单的协议，你使用一个 UDP 套接字并得到一个字节数组，长度为 48 个字符，其中包含所有重要的信息。

所以让我们用 F#实现一个 ntp 客户端。

## 与服务器对话

我们需要做的第一件事是连接我们的 ntp 服务器。我将继续使用`time.windows.com`作为我的样本，但是有许多不同的时间服务器，具有不同的层级(`time.windows.com`是层级 4，这意味着有 4 个来自*源*)。

我将首先解析我的服务器的 IP，让我们创建一个`getEndPoint`函数:

```
let getEndPoint (server : string) = 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我将使用`System.Net.Dns`来获取 IP 地址:

```
 let getEndPoint (server : string) =
        let address =
            Dns.GetHostEntryAsync(server) 
```

Enter fullscreen mode Exit fullscreen mode

但是这是一个`async`方法(和 C# `async`)，所以我们要做一些流水线操作:

```
 let getEndPoint (server : string) =
        let address =
            Dns.GetHostEntryAsync(server)
            |> Async.AwaitTask
            |> Async.RunSynchronously 
```

Enter fullscreen mode Exit fullscreen mode

这将返回一个`IPHostEntry`，然后我可以使用它来获取主机的 IP，我将从中获取第一个 IP:

```
 let getEndPoint (server : string) =
        let address =
            Dns.GetHostEntryAsync(server)
            |> Async.AwaitTask
            |> Async.RunSynchronously
            |> fun entry -> entry.AddressList
            |> fun list -> list.[0] 
```

Enter fullscreen mode Exit fullscreen mode

最后，我将使用端口`123`创建端点连接，这是标准的 ntp 端口:

```
 let getEndPoint (server : string) =
        let address =
            Dns.GetHostEntryAsync(server)
            |> Async.AwaitTask
            |> Async.RunSynchronously
            |> fun entry -> entry.AddressList
            |> fun list -> list.[0]
        new IPEndPoint(address, 123) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备好通过 UDP 套接字连接我们的 ntp 服务器:

```
 let getTime (ipEndpoint : IPEndPoint) =
        let ntpData =
            [| for i in 0..47 ->
                match i with
                | 0 -> byte 0x1B
                | _ -> byte 0 |]

        let socket = new Socket(AddressFamily.InterNetwork, SocketType.Dgram, ProtocolType.Udp)
        socket.Connect(ipEndpoint)
        socket.ReceiveTimeout <- 3000
        socket.Send(ntpData) |> ignore
        socket.Receive(ntpData) |> ignore
        socket.Close()
        ntpData 
```

Enter fullscreen mode Exit fullscreen mode

F#使得生成数据包数组变得非常容易，使用`match`意味着我们可以根据需要设置不同的字节(例如，我在第一个字节中初始化一个版本)。我*应该*将`Originate Timestamp`设置在 32 - 39 字节，你也可以在比赛中这样做，但我有点懒(注意:不这样做意味着更难根据延迟计算出时间)。

## 提取我们的数据

现在我有了一个字节数组，其中充满了从我们的 ntp 服务器返回的有用信息，但它是原始字节，可读性不是很强，所以我们可能希望将其分开。我将创建一个记录类型来表示它:

```
 type NtpResponse =
        { Stratum : int
          ReferenceTime : DateTime
          OriginateTime : DateTime
          ReceiveTime : DateTime
          TransmitTime : DateTime } 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将创建一个函数来分解我们的字节数组

```
 let extractor (ntpData : byte[]) =
        { Stratum = int ntpData.[1]
          ReferenceTime = DateTime.Now
          OriginateTime = DateTime.Now
          ReceiveTime = DateTime.Now
          TransmitTime = DateTime.Now } 
```

Enter fullscreen mode Exit fullscreen mode

我提取的第一部分是地层，它告诉我们服务器是什么级别的。如果您想知道服务器离时间源有多近，此信息非常有用，因为离时间源越远，发生的网络跃点就越多。我们还将值从一个字节转换为一个整数，这在 F#中需要比在 C#中更明确

但是我们拥有的数据中更重要的部分是日期/时间部分，它们是 8 个字节，分别从 16、24、32 和 40 开始，所以让我们看看如何将它们从数组中取出。

组成时间戳的 8 个字节由 64 位定点时间戳和 32 位浮点值组成，我们需要将 64 位定点时间戳分成 32 位整数值和 32 位浮点值，前者表示自 Unix Epoch 以来的秒数，后者表示时间戳的精度。

为此，我将创建一个函数，它接受 8 个字节并给我一个时间戳:

```
 let extractTime ntpData position =
        let intPart = BitConverter.ToUInt32(ntpData, int position)
        let fracPart = BitConverter.ToUInt32(ntpData, int position + 4) 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`uint32`来表示我们提取的数据(因为它可能是一个很大的数字)。但是接下来我们需要处理的是值是[大端序](https://en.wikipedia.org/wiki/Endianness)符号，所以我们需要交换它来使用。这个函数并不难写...

```
 let swapEndianness (x : uint64) =
        (((x &&& uint64 0x000000ff) <<< 24) + ((x &&& uint64 0x0000ff00) <<< 8) + ((x &&& uint64 0x00ff0000) >>> 8)
        + ((x &&& uint64 0xff000000) >>> 24)) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以用它来更新我们的`extractTime`方法，以生成毫秒并创建我们的`DateTime`对象:

```
 let extractTime ntpData position =
        let intPart = BitConverter.ToUInt32(ntpData, int position)
        let fracPart = BitConverter.ToUInt32(ntpData, int position + 4)
        let ms =
            ((swapEndianness (uint64 intPart)) * uint64 1000)
            + ((swapEndianness (uint64 fracPart)) * uint64 1000) / uint64 0x100000000L
        (new DateTime(1900, 1, 1, 0, 0, 0, DateTimeKind.Utc)).AddMilliseconds(float ms) 
```

Enter fullscreen mode Exit fullscreen mode

像这样的时候，我真的很想念 C#的运算符重载...

有了我们的`extractTime`方法，让我们回去完成`extractor` :

```
 let extractor extractTime (ntpData : byte[]) =
        let extractor' = extractTime ntpData
        let referenceTime = extractor' 16
        let originateTime = extractor' 24
        let receiveTime = extractor' 32
        let transmitTime = extractor' 40

        { Stratum = int ntpData.[1]
          ReferenceTime = referenceTime
          OriginateTime = originateTime
          ReceiveTime = receiveTime
          TransmitTime = transmitTime } 
```

Enter fullscreen mode Exit fullscreen mode

通过一点局部应用，我们可以将`extractTime`方法绑定到`ntpData`上，这样就更容易调用了。

## 整理这一切

写完所有函数后，我们现在可以计算出时间:

```
 let time = getEndPoint "time.windows.com"
                |> getTime
                |> extractor extractTime

    printfn "The time is %s" (time.TransmitTime.ToString("dd/MM/yyyy hh:mm:ss.fffffff")) 
```

Enter fullscreen mode Exit fullscreen mode

我们结束了。好吧*大部分时间*，我给你留下了一些练习，亲爱的读者，如果你想完成这个的话:

*   在发送给 ntp 服务器的数据中设置`Originate Time`
*   使用客户端发送请求的时间和服务器上的时间来确定无延迟的**实际**时间(使用[时钟同步算法](https://en.wikipedia.org/wiki/Network_Time_Protocol#Clock_synchronization_algorithm)
*   从响应包中提取其他部分，如版本、轮询间隔(这样您就知道多久可以访问一次服务器)等

## 结论

时间都是相对的，但有了 ntp，我们可以计算出它的相对程度。使用一点点 F#我们可以查询 ntp 服务器并获得时间信息。希望看到我们认为理所当然的协议是如何工作的会有点意思。