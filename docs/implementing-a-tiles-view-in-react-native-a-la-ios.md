# 在 React Native 中实现平铺视图

> 原文：<https://dev.to/emilios1995/implementing-a-tiles-view-in-react-native-a-la-ios>

我最近有一个 react-native 项目，其中某个屏幕有以下要求:

*   有等间距排列的方块的屏幕。
*   排列必须总是从左到右(看起来有点像 iOS 主屏幕)。
*   每行的平铺数量必须是可配置的。必须相应地计算尺寸和余量。

在这种情况下，flex 显然是不够的，所以经过大量的实验，我想到了下面的公式:

```
// tpr = tiles per row
const calcTileDimensions = (deviceWidth, tpr) => {
  const margin = deviceWidth / (tpr * 10);
  const size = (deviceWidth - margin * (tpr * 2)) / tpr;
  return { size, margin };
}; 
```

这个函数和正确的样式(我将向您展示)一起提供了预期的结果。看看这些图片:

`tpr = 3`:[![tpr = 3](img/de3071fd7d408f3994cc41c3eed76e81.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JloqvCi6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ajsh0aefHsTjj-5wA4gqaUA.png)
`tpr = 2`:[![tpr = 2](img/b25eb5d9b6ed93f5cad5fb5bc06c6198.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9fe8g34_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AkDv7Qv65ZOof5f0m01-QfQ.png)

以下是完整的使用示例:

```
import React, { Component } from 'react';
import { Text, View, StyleSheet, Dimensions } from 'react-native';
const { width } = Dimensions.get("window");

export default class App extends Component {
  render() {
    const tileDimensions = calcTileDimensions(width, 2)  // -> change this number and see!
    const tiles = 'Lorem Ipsum Dolor Sit Amet'.split(' ')
    return (
      <View style={styles.container}>
        {tiles.map(i => Item({...tileDimensions, text: i}))}     
      </View>
    );
  }
}

const Item = ({size, margin, text}) => (
  <View style={[styles.item, {width: size, height: size, marginHorizontal: margin}]}>
    <Text style={styles.itemText}>{text}</Text>
  </View> )

const calcTileDimensions = (deviceWidth, tpr) => {
  const margin = deviceWidth / (tpr * 10);
  const size = (deviceWidth - margin * (tpr * 2)) / tpr;
  return { size, margin };
};

const styles = StyleSheet.create({
  container: {
     justifyContent: "flex-start", flexDirection: "row", flexWrap: "wrap", marginTop: 30
  },
  item: {
    backgroundColor: 'yellow',  
     alignSelf: "flex-start",
     alignItems: 'center',
     justifyContent: 'center',
     marginBottom: 20
  },
  itemText: {
    fontSize: 20
  }
}); 
```

这是作为[博览会](expo.io)的[小吃](https://snack.expo.io/BysG-9o3x)。请随意使用它！
希望这对你有用。感谢阅读！