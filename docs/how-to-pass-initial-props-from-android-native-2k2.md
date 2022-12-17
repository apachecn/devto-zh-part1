# 如何从 Android 原生传初始道具？

> 原文：<https://dev.to/ryohlan/how-to-pass-initial-props-from-android-native-2k2>

有 iOS 原生的传递初始道具的官方文档，但不是 Android。

它由 ReactActivityDelegate 的 getLaunchOptions 传递。

因此，重写 MainActivity 的 createractivitydelegate 和 ReactActivityDelegate 的 getLaunchOptions。

```
public class MainActivity extends ReactActivity {
    @Override
    protected ReactActivityDelegate createReactActivityDelegate() {
        return new ReactActivityDelegate(this, getMainComponentName()) {
            @Nullable
            @Override
            protected Bundle getLaunchOptions() {
                Bundle bundle = new Bundle();
                bundle.putString("message", "Hello world from Android Native");
                return bundle;
            }
        };
    }
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

它作为根组件的道具被接收，根组件是 appregistry . register component .
的第二个参数

```
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';

export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text>{this.props.message}</Text>
      </View>
    );
  }
}

... 
```

Enter fullscreen mode Exit fullscreen mode