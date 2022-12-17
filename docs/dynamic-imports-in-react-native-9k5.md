# React-Native 中的动态导入

> 原文：<https://dev.to/emilios1995/dynamic-imports-in-react-native-9k5>

你有没有尝试过在 React-Native 应用中这样做？

```
<Image source={require(img/" + this.props.image)} /> 
```

如果有，您可能会发现所有的导入都是在编译时静态分析的，并且必须是一个简单的字符串，而不是动态表达式。然而，在最近的一个项目中，这非常令人沮丧；我的要求是在应用捆绑包中包含一个图像文件夹，并根据服务器的响应渲染其中一个图像。
我们能做的，就是要求所有的图片都在一个文件中:

```
// asseimg/index.js
const images = {
  dog: require("./dog.png"),
  cat: require("./cat.png")
}
export default images; 
```

然后，我们可以这样使用它:

```
// app/imageView.js
import images from "../assets/images"
const View = (props) => 
  <Image source={images[this.props.image]} /> 
```

问题是，我的图像文件夹有很多图像，我不可能手动要求所有的图像。因此，我利用自动化的力量，创建了一个简单的脚本来为我制作 asseimg/index.js 文件:

```
// prepareImages.js
const fs = require("fs");
const files = fs.readdirSync("./assets/images").filter(x => x.includes("png"));
const ex =
  "{\n" +
  files.map(x => `"${x.split(".png")[0]}": require("./${x}"),`).join("\n") +
  "}";
const res = "export default " + ex;
fs.writeFileSync("./asseimg/index.js", res); 
```

这将产生一个很好的文件导出文件夹中的每个图像。
感谢您的阅读！

页（page 的缩写）s:我发现了一个巴别塔插件，它做的事情是这样的:[https://github.com/dushaobindoudou/babel-plugin-require-all](https://github.com/dushaobindoudou/babel-plugin-require-all)但是文档不是很清楚(是中文还是什么)，所以我测试了它，它是这样工作的:
给定:`const imges = requireAll('./assets/imgs')`
它将输出:

```
const $assets_images_cat = require('./asseimg/cat.png');

const $assets_images_dog = require('./asseimg/dog.png');

const images = {
  $assets_images_cat: $assets_images_cat,
  $assets_images_dog: $assets_images_dog
}; 
```