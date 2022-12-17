# 用 WebRTC 创建多人游戏

> 原文：<https://dev.to/rynobax_7/creating-a-multiplayer-game-with-webrtc>

我这个项目的目标是开发一个在线多人游戏，使用尽可能少的服务器资源。我希望能够在一个用户的浏览器中运行游戏服务器，并允许其他玩家连接到它。我还想让这个项目简单到可以在博客中解释。

**技术**

在我搜索对等网络技术的过程中，我很快发现了 [WebRTC](https://webrtc.org/) ，它似乎非常适合我的项目。WebRTC 是一种新的 web 标准，它为浏览器提供了实时通信能力。我看到的大多数 WebRTC 例子都是建立视频或音频流，但是您也可以传输任意数据。在我的例子中，我可以使用数据通道向主机传输用户输入，向玩家传输游戏状态。

然而，WebRTC 并没有完全消除对服务器的需求。为了建立连接，两个浏览器必须交换少量信息。一旦建立了连接，通信就完全是点对点的了。

**图书馆**

WebRTC API 相当复杂，所以我寻找一个库来简化它。我遇到的功能最全的是 [PeerJS](https://github.com/peers/peerjs) ，但它已经两年没有更新了。我很快遇到了一些迫使我放弃它的主要错误。我决定使用[简单对等](https://github.com/feross/simple-peer)，它为使用 WebRTC 连接和通信提供了一个简单的 API。来自他们的文档:

```
var SimplePeer = require('simple-peer')

var peer1 = new SimplePeer({ initiator: true })
var peer2 = new SimplePeer()

peer1.on('signal', function (data) {
  // when peer1 has signaling data, give it to peer2 somehow
  peer2.signal(data)
})

peer2.on('signal', function (data) {
  // when peer2 has signaling data, give it to peer1 somehow
  peer1.signal(data)
})

peer1.on('connect', function () {
  // wait for 'connect' event before using the data channel
  peer1.send('hey peer2, how is it going?')
})

peer2.on('data', function (data) {
  // got a data channel message
  console.log('got a message from peer1: ' + data)
}) 
```

Enter fullscreen mode Exit fullscreen mode

**建立连接**

为了在两个浏览器之间建立连接，我需要交换大约 2 kb 的信令数据。我选择使用 Firebase 实时数据库，因为它允许我轻松地在两个浏览器之间同步数据，并且免费层提供了大量的存储空间。

从用户的角度来看，主机给玩家一个四个字母的代码，玩家用它来连接游戏。从浏览器的角度来看，这个过程只是稍微复杂一点。作为参考，我的数据库规则是这样的:

```
{
  "rules": {
    "rooms": {
      // 4 Digit room code used to connect players
      "$room_code": {
        "host": {
           "$player": {
             "$data": {
               "data": {
                 // Data from the host for the player
               }
             }
           }
        },
        "players": {
          "$player": {
            "$data": {
              "data": {
                // Data from the player for the host
              }
            }
          }
        },
        "createdAt": {
          // Timestamp set by host when room is created
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**主持房间**

为了主持一个房间，主持人首先通过随机尝试 4 个字符代码来生成一个代码，直到它找到一个没有被使用的房间。如果文件室不存在于数据库中，或者文件室是在 30 分钟前创建的，则认为文件室未被使用。当游戏开始时，主机应该删除房间，但我想确保避免僵尸房间。当主机找到一个开放的房间时，主机的浏览器会将自己添加为该房间的主机，并监听玩家。

```
function getOpenRoom(database){
 return new Promise((resolve, reject) => {
   const code = generateRoomCode();
   const room = database.ref('rooms/'+code);
   room.once('value').then((snapshot) => {
     const roomData = snapshot.val();
     if (roomData == null) {
       // Room does not exist
       createRoom(room).then(resolve(code));
     } else {
       const roomTimeout = 1800000; // 30 min
       const now = Date.now();
       const msSinceCreated = now - roomData.createdAt;
       if (msSinceCreated > roomTimeout) {
         // It is an old room so wipe it and create a new one
         room.remove().then(() => createRoom(room)).then(resolve(code));
       } else {
         // The room is in use so try a different code
         resolve(getOpenRoom(database));
       }
     }
   })
 });
} 
```

Enter fullscreen mode Exit fullscreen mode

**加入游戏**

玩家通过输入房间代码和他们的用户名来加入游戏。玩家的浏览器通过在路线`rooms/[code]/players`中添加条目来通知主机。当玩家得到他们的信令数据时，这些数据将在路由`rooms/[code]/players/[name]`中存入数据库。

```
// code and name are entered by user
const peer = new SimplePeer({initiator: true});
this.peer = peer;
this.setState({host: peer});

// Sending signaling data from player
peer.on('signal', (signalData) => {
  const nameRef = database.ref('/rooms/'+code+'/players/'+name);
  const newSignalDataRef = nameRef.push();
  newSignalDataRef.set({
    data: JSON.stringify(signalData)
  });
});

// Listen for signaling data from host for me
const hostSignalRef = database.ref('/rooms/'+code+'/host/'+name);
hostSignalRef.on('child_added', (res) => {
  peer.signal(JSON.parse(res.val().data));
}); 
```

Enter fullscreen mode Exit fullscreen mode

主持人监听新加入的玩家。当新玩家连接时，主机消耗他们发送的信号，并在路由`rooms/[code]/host/[name]`上用自己的信号回复。

```
// Listen for new players
playersRef.on('child_added', (res) => {
  const playerName = res.key;

  // Create Peer channel
  const peer = new SimplePeer();

  // Listen for signaling data from specific player
  playerRef.on('child_added', (res) => peer.signal(JSON.parse(res.val().data)));

  // Upload signaling data from host
  const signalDataRef = database.ref('/rooms/'+code+'/host/'+playerName);
  peer.on('signal', (signalData) => {
    const newSignalDataRef = signalDataRef.push();
    newSignalDataRef.set({
      data: JSON.stringify(signalData)
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

从此以后，主机和玩家可以使用`peer.on(‘data’, cb)`和`peer.send(data)`进行通信。玩家的机器一旦与主机连接就终止其 firebase 连接，主机在游戏开始时也这样做。

就是这样！在这一点上，我在主机和所有玩家之间进行了双向通信，就像我在传统服务器上一样，所以剩下的就是制作游戏并在玩家之间传递数据。

**获取用户输入**

每当键改变状态时，用户输入作为 JSON 对象发送。示例:`{ up: true }`

主机跟踪每个玩家的输入状态，并使用它们来移动每一帧的玩家。

**分享游戏状态**

为了保持游戏开发简单，我想使用 2D 游戏框架 [Phaser](https://github.com/photonstorm/phaser-ce) 。游戏在主机上运行，处理物理和碰撞之类的事情。每一帧，每个精灵的位置和大小都被序列化并发送给每个玩家。为了使事情变得简单，我简单地使用精灵数据在玩家的浏览器中每一帧重画整个游戏。因为我的游戏只使用了少量的 sprite，所以这个过程很好，但是一个更复杂的游戏可能需要一个更有效的过程来共享游戏状态。

**游戏玩法**

我制作的测试所有这些的游戏是一个简单的侧边滚动条。平台随机出现，最后留在平台上的玩家获胜。抱歉，如果你遇到任何不可能的差距，我没有花太多时间来抛光。

**注释**

因为游戏服务器运行在玩家的一台机器上，所以它很容易受到玩家的操纵。只要你的朋友不是骗子，这个系统应该很适合和朋友一起玩游戏。

**结论**

我可以建立一个点对点的多人游戏，每个玩家只需要 2kb 的服务器带宽。我应该能够支持 Firebase 免费层每月 500，000 名玩家！额外的收获是，我能够让我的代码足够简洁，适合这篇文章中的大部分内容。我认为 WebRTC 是一项很好的技术，我很高兴看到其他项目也将使用它来构建。

在这里玩游戏[，在这里](https://rynobax.github.io/jump-game/)查看源代码[！](https://github.com/rynobax/jump-game)