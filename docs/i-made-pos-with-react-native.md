# 我做了:用反应本地发布

> 原文：<https://dev.to/eldelshell/i-made-pos-with-react-native>

这是我将撰写的一系列帖子的第一部分，解释了使用 React Native 为 Android 定制 POS 应用程序的开发进度。

## 上下文

我妻子刚刚在猫开了一家咖啡馆，人们在那里聚在一起喝咖啡，而猫则在周围玩耍，诸如此类。还有一家商店，顾客可以在那里购买与猫相关的装备，如吊坠、t 恤衫等。

在研究了市场上不同的 POS 选项后，我决定自己做一个。这是一个兼职项目，在周末和休息日花费了近 200 个小时的工作，在我们添加新功能时仍在开发中。

[![Features](img/a9758c60c2edf93e5fd33636cc4c48f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FEYXmjhg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/66r65oc2q37oatv3ro2e.png)

因此，主要要求是:

*   在 Android 平板电脑上运行(Galaxy Tab A10)
*   打印到蓝牙打印机。
*   跟踪客户与猫在一起的时间(一小时、半小时等)。
*   跟踪每张票上的客户数量。
*   用开/关日流程跟踪抽屉上的钱。
*   接受预订。
*   允许向票证添加一次性产品。
*   管理长期产品(咖啡、茶、杯子等)。

## 预习

当然，当有很多帖子的时候，谁会疯狂到写自己的帖子呢？首先，我们试图从不同的公司购买完整的解决方案，但这些公司超出了预算，我们担心我们的一些要求无法得到满足。所以我们去了亚马逊[1]，花了将近 100 美元买了蓝牙打印机和现金抽屉。因为我们家里已经有了几台平板电脑，我们节省了 200 英镑。那些基于 Windows 的带触摸屏的 POS 相当昂贵。

我还是不想把去谷歌 Play 商店的事情搞砸。有很多很棒的应用程序可以完成这项工作，比如 Loyverse。还有许多其他的，可以说是如此丑陋和复杂，比我自己做这个应用程序要花更多的时间。最后，没有一个符合我们的要求，所以要从头开始。

## 反应原生

这不是我的第一个 Android 或 React 原生应用程序，所以我知道我会遇到什么，我主要关心的是蓝牙打印机。幸运的是，我们得到的那份带有 iOS、Android、Windows 和 Linux 的源代码。

因此，有了大部分蓝牙代码，很简单的事情就是清理一下，这样我的眼睛就不会流血，然后将 Android 端与 React 原生端连接起来。

我的下一个关注点是 SQLite 数据库，我没有在 React Native 上使用过，但只在 Android 上使用过，经过一些调查后，我发现[React-Native-SQLite-storage](https://github.com/andpor/react-native-sqlite-storage)完成了这项工作，不需要数百个依赖项，也不碍事。第二部分将详细介绍这一点。

另一个担心是对货币和日期的处理，但拜托，现在是 2017 年，我们有 Moment 和 [Big](https://github.com/MikeMcl/big.js) 可以完美地工作。

这是使用的所有依赖项:

*   react-原生 0.47.2
*   Big.js 用于十进制数处理。
*   日期处理的时刻。
*   反应导航(我喜欢这个)
*   react-native-vector-icons 和 Material Icons(没有这个就不要启动项目)
*   react-native-SQLite-存储
*   react-native-fs

### 为什么不是安卓原生

我想到了这一点，但是在你做了一些 React 原生应用之后，你就会明白，在 JavaScript 上实现 UI 和业务逻辑要比在 Java 上容易得多。此外，开发流程要快得多，因为在模拟器上按下`rr`会刷新所有的更改，而不必等待任何构建过程。

特别是由于 flex，UI 更容易实现。是的，有一些限制，比如那些带有`TextInput`的限制，但是这些对于这个项目来说并不是一个大问题。

[![Main POS Screen](img/6b8f534e07be9670271a5ed224fc69ec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1MnUCjDZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nt722f97i1f7ennpxn29.png)

## 蓝牙打印机

我的 Android 源文件夹是这样结束的:

```
.
â””â”€â”€ com
    â””â”€â”€ polinekopos
        â”œâ”€â”€ bt
        â”‚Â Â  â”œâ”€â”€ BTModule.java
        â”‚Â Â  â”œâ”€â”€ BTModulePackage.java
        â”‚Â Â  â””â”€â”€ sdk
        â”‚Â Â      â”œâ”€â”€ BluetoothService.java
        â”‚Â Â      â”œâ”€â”€ Command.java
        â”‚Â Â      â”œâ”€â”€ PrinterCommand.java
        â”‚Â Â      â””â”€â”€ PrintPicture.java
        â”œâ”€â”€ MainActivity.java
        â””â”€â”€ MainApplication.java 
```

在`sdk`包下是打印机自带的所有东西，所以我只能用它来创建 React 本机接口。在`libs`文件夹中还有一个`btsdk.jar`文件和其他依赖项。

所有神奇的事情都发生在这里，在浏览了代码并做了一些重构(比如添加一个监听线程来通知断开事件)之后，我很好地理解了蓝牙打印机神奇的通信是如何工作的。TL:DR 它涉及到很多字节和缓冲区。

`BTModule`管理该服务和 React 原生应用之间的一切:

```
public class BTModule extends ReactContextBaseJavaModule {
    public BTModule(ReactApplicationContext reactContext) {
        ...
    }

    @ReactMethod
    public void connect(String address, Promise promise) { ... }

    @ReactMethod
    public void disconnect(Promise promise) { ... }

    @ReactMethod
    public void print(String message, Promise promise) { ... }

    @ReactMethod
    public void getState(Promise promise) { ... }

    private void sendEvent(String eventName) { ... }
} 
```

这些方法非常简单，React 可以直接使用。其他一切都在 React 本地文档的本节[中解释。](https://facebook.github.io/react-native/docs/native-modules-android.html)

在`MainActivity`上也有一些修改，以暂停和恢复与打印机的连接:

```
@Override
public synchronized void onResume() {
    super.onResume();
    if (mBluetoothAdapter == null) {
        return;
    }

    if (BluetoothService.get().getState() == BluetoothService.STATE_NONE) {
        BluetoothService.get().start();
    }
} 
```

从反应自然的角度来看，这很简单。顶部栏上的连接/断开按钮，也通过收听蓝牙事件和向打印机发送字符串来显示状态。

```
export class Banner extends React.Component {
    state = {
        bluetooth: 'bt-unknown'
    };

    componentWillMount() {
        events.forEach((e) => DeviceEventEmitter.addListener(e, () => this.setState({bluetooth: e})));
    }

    componentWillUnmount() {
        events.forEach((e) => DeviceEventEmitter.removeAllListeners());
    }

    getBluetoothIcon() {
        let icon = null;
        switch (this.state.bluetooth) {
            case 'bt-unknown':    icon = IconFactory.Icons.BTUnknown; break;
            case 'bt-connecting': icon = IconFactory.Icons.BTConnecting; break;
            case 'bt-connected':  icon = IconFactory.Icons.BTConnected; break;
            case 'bt-disconnect': icon = IconFactory.Icons.BTDisconnected; break;
        }
        return IconFactory.build(icon, styles.drawerIcon);
    }

    async onBluetooth() {
        const bluetooth = await BTModule.getState();
        this.setState({bluetooth: bluetooth});

        if(bluetooth !== 'bt-connected'){
            const id = ConfigManager.of().getBluetoothID();
            try {
                const deviceName = await BTModule.connect(id);
                AlertFactory.info(Messages.get('message.bluetooth.connected'));
            } catch (e) {
                AlertFactory.error(Messages.get('error.bluetooth.connection'));
            }
        }
    }

    render() {
        return (
            // ...
            <View style={styles.bannerRight}>
                <TouchableHighlight
                    underlayColor='transparent'
                    style={styles.drawerButton}
                    onPress={this.onBluetooth.bind(this)}>
                        { this.getBluetoothIcon() }
                </TouchableHighlight>
            </View>
        );
    }
} 
```

在这里，`DeviceEventEmitter`管理从应用程序本机发送的事件，这些事件来自在`BTModule`上定义的`sendEvent`方法，并使用它接收到的状态呈现视图。这样，蓝牙图标总是最新的。

`onBluetooth`功能绑定到按钮，检查我们是否断开连接，并尝试再次连接。无论发生什么，都会向用户显示一个警告。

对象负责管理和渲染由 T2 提供的图标。

打印相当容易，尽管它需要使用大量的`padStart`和`padEnd`来根据需要对齐内容。总之，在创建了一个包含所有行的数组之后，只需要调用`write`函数:

```
 async print() {
        // Some margin
        await BTModule.print(' ');
        await BTModule.print(' ');

        // Now the ticket
        this.lines.forEach((line) => BTModule.print(line));

        // Cut margin
        await BTModule.print(' ');
        await BTModule.print(' ');
    } 
```

[![This is how a ticket looks like after it was printed](img/797b55aebeec040651ae0c0feb76d0a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--69bbu71k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s22156kulmfaboz3lzk7.png)

这是第一部分，希望你喜欢并期待第二部分，在那里我将讨论 SQLite 方面的事情和一些关于 react-navigation 的好消息。

顺便说一句，你注意到上一张照片上的虫子了吗？看起来需要更多的工作！

### 脚注

1.  在亚马逊上购买这类东西时，要小心“一起购买”部分。我这边大错特错。我们买的打印机没有任何端口来连接抽屉(这是一个像电话一样的小端口)，所以我们必须手动打开它，热敏纸也是错误的。