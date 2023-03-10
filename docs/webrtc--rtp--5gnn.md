# WebRTC 使用的 RTP 扩展的简单介绍

> 原文：<https://dev.to/voluntas/webrtc--rtp--5gnn>

WebRTC 利用 RTP 收发声音和视频，而 RTP 有一种可以随心所欲地扩展的机制——RTP 扩展。

[RFC 8285-RTP 报头扩展的通用机制](https://tools.ietf.org/html/rfc8285)

最近，旧的 RFC 也曾被复盖，很难得，所以我简单介绍了一下 WebRTC 使用的 RTP 扩展。

关于 RTP 扩展，读 RFC 就知道了，所以省略。

## 铬 M63

Chrome 积极利用 RTP 扩展。 与其说是 Chrome，不如说是 libwebrtc 给人的印象。

```
a=extmap:1 urn:ietf:params:rtp-hdrext:ssrc-audio-level
a=extmap:2 urn:ietf:params:rtp-hdrext:toffset
a=extmap:3 http://www.webrtc.org/experiments/rtp-hdrext/abs-send-time
a=extmap:4 urn:3gpp:video-orientation
a=extmap:5 http://www.ietf.org/id/draft-holmer-rmcat-transport-wide-cc-extensions-01
a=extmap:6 http://www.webrtc.org/experiments/rtp-hdrext/playout-delay
a=extmap:7 http://www.webrtc.org/experiments/rtp-hdrext/video-content-type
a=extmap:8 http://www.webrtc.org/experiments/rtp-hdrext/video-timing 
```

Enter fullscreen mode Exit fullscreen mode

## 火狐 57

Firefox 给人的印象是必要的最低限度。 你好像对扩张不太感兴趣。

```
a=extmap:1/sendonly urn:ietf:params:rtp-hdrext:ssrc-audio-level
a=extmap:1 http://www.webrtc.org/experiments/rtp-hdrext/abs-send-time
a=extmap:2 urn:ietf:params:rtp-hdrext:toffset 
```

Enter fullscreen mode Exit fullscreen mode

## RTP summary

RTP 扩展在 SDP 中定义，并确定在通过 Offer/Answer 进行交互时是否支持。

将 extmap:1 的 1 称为本地 ID，决定可以通过其握手以 m=为单位随心所欲地决定。

实际上，Firefox 有两个本地 ID 1。 这是因为用 m=分隔。

本地 ID 后面有/sendonly，这称为直接，它明确表示使用此扩展的方向。

接下来是 URI。 IETF 的情况是 urn:ietf .. .的感觉。 除此之外的规则是以 http://的感觉写。

## RTP "Zhang Shaojie"

### urn:IETF:params:RTP-HD rext:ssrc-audio-level

这是 ssrc -音频级别扩展。 这是一个仅用于语音的扩展，它包含的 RTP 数据包音量为 0-127。

0 是最大的声音，127 是最小的声音。 包含在所有的 RTP 数据包中。

### urn:IETF:params:RTP-HD rext:toff set

这个功能是不支持 abs-send-time 的情况下使用的结构，因为 WebRTC 支持 abs-send-time，所以什么都不用在意。

### 【http://www.webrtc.org/experiments/rtp-hdrext/abs-send-time】T2

绝对发送时间( ABS-send-time )。 简单来说，是与 RTP 的 timestamp 分开，淡淡地 incremnt 的值。 但是并不是一直增加，而是 24 比特。

利用这一点，可以判断 RTP 的 SeqNum 是“旧的还是新的”。 很方便。

### urn:3gpp:视频导向

是狂热的扩张。 这是一个“成角度”的扩张。 旋转了 90 度等标志会进来。

接收方可以注意到视频“旋转了”。

### [http://www . IETF . org/id/draft-holmer-RM cat-transport-wide-cc-extensions-01](http://www.ietf.org/id/draft-holmer-rmcat-transport-wide-cc-extensions-01)

[https://tools . IETF . org/html/draft-holmer-RM cat-transport-wide-cc-extensions-01](https://tools.ietf.org/html/draft-holmer-rmcat-transport-wide-cc-extensions-01)

从插座发送的顺序号码会进来。 我想只有这个的话，不知道有什么用。

它假定为名为 transport-wide RTCP 反馈消息的 RTCP 反馈消息。

关于这个，因为会很长，所以只是“有那样的东西啊”的程度。

### 【http://www.webrtc.org/experiments/rtp-hdrext/playout-delay】T2

没有太多信息，我不知道这个。 说起来 URL 是 404。

[https://github.com/webrtc/webrtc-org/pull/117](https://github.com/webrtc/webrtc-org/pull/117)

### [http://www . webrtc . org/experiments/RTP-HD rext/video-content-type](http://www.webrtc.org/experiments/rtp-hdrext/video-content-type)

是实时还是屏幕共享等都会进来。

### 【http://www.webrtc.org/experiments/rtp-hdrext/video-timing】T2

规格和安装不同，请注意。 编码的开始时间和结束时间进入。 用于发送每帧的定时信息。

## 总结

RTP 扩展在浏览器中将 WebRTC 用作 P2P 时无需在意。 但是，也有通过利用这些扩展得到的统计信息，所以想和 chrome://webrtc-internals 成为好朋友的人总觉得应该记住。