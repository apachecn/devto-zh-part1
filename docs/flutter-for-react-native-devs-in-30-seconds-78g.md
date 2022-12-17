# 在 30 秒内为反应原生设备颤动

> 原文：<https://dev.to/swyx/flutter-for-react-native-devs-in-30-seconds-78g>

你可能听说过 [Flutter](https://flutter.io) ，谷歌对 React Native 的回答。你应该知道什么？

*   它是用大量借鉴 Java 的 [Dart](https://www.dartlang.org/) 语言编写的。但是 Javascript 爱好者会发现它非常容易阅读(见下文)
*   [IntelliJ 或 Android Studio](https://flutter.io/ide-setup/) 是推荐使用 Flutter 插件的 ide。
*   与 RN 不同，Flutter 不使用 Javascript 桥，它直接编译成本地 iOS/Android 文件。(但是在“慢速模式”开发中，它作为一种解释语言运行，所以您仍然可以进行[热重装](https://flutter.io/hot-reload/))
*   与 RN 不同的是，Flutter 自带“电池”，有意见 **[路由](https://flutter.io/routing-and-navigation/)****[动画](https://flutter.io/animations/)****[i18n](https://flutter.io/tutorials/internationalization/)**和**主题**！！！特别是，它带有一系列内置的[小部件样式](https://flutter.io/widgets/)，你可以使用材料设计或“库比蒂诺”(又名苹果“灵感”)设计。还有，我提到**路由了吗？！？！？！**在这里，让我说句公道话:

# 颤振自带路由！！！1!！！

*   动画非常好。你能在 React Native 里[做这个](https://flutter.io/animations/hero-animations/)吗？

[https://www.youtube.com/embed/CEcFnqRDfgw](https://www.youtube.com/embed/CEcFnqRDfgw)

*   像 RN 一样，Flutter 使用了很多熟悉的范例，包括`class`扩展、`setState`和事件处理程序。如果你能读 RN，你就能读 Flutter:

```
class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text(widget.title),
      ),
      body: new Center(
        child: new Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            new Text(
              'You have pushed the button this many times:',
            ),
            new Text(
              '$_counter',
              style: Theme.of(context).textTheme.display1,
            ),
          ],
        ),
      ),
      floatingActionButton: new FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: new Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
} 
```

*   安装体验非常流畅:

```
git clone -b alpha https://github.com/flutter/flutter.git
export PATH=`pwd`/flutter/bin:$PATH
flutter doctor 
```

试试看！ [http://flutter.io](http://flutter.io) 而谷歌 Codelab 教程是[这里](https://codelabs.developers.google.com/codelabs/flutter)。或者看谷歌 I/O 视频！

[https://www.youtube.com/embed/w2TcYP8qiRI](https://www.youtube.com/embed/w2TcYP8qiRI)