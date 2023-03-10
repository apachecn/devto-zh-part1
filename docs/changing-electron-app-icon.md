# 更改电子应用程序图标

> 原文：<https://dev.to/onmyway133/changing-electron-app-icon>

原帖[https://github.com/onmyway133/blog/issues/66](https://github.com/onmyway133/blog/issues/66)

### 生成 icns

*   生成`.iconset`
*   运行`iconutil -c icns "Icon.iconset"`。请注意，图标名称的首字母必须小写

[![icns](img/a4a5cd9a35309397de36c748b37ca902.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XIUqbnDv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/2284279/29305228-da1a8560-8197-11e7-8826-6a278157abf2.png)

### 使用 icns

*   在`main.js`中，指定`icon`

```
win = new BrowserWindow({
  width: 800, 
  height: 600,
  icon: __dirname +  '/Icon/Icon.icns'
}) 
```

Enter fullscreen mode Exit fullscreen mode

您也可以使用帮助器 url 方法

```
const path = require('path')
const url = require('url')

const iconUrl = url.format({
  pathname: path.join(__dirname, 'Icon/Icon.icns'),
  protocol: 'file:',
  slashes: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 如果应用图标没有更新

*   我遇到一个问题，电子总是显示默认的应用程序图标。我尝试使用`png`、`NativeImage`、不同的图标尺寸，但还是有问题
*   转到`node_modules -> electron -> dist`，右击`Electron`，选择`View Info`
*   将另一个`icns`拖到左上角的图标中

[![info](img/9db436241d35a84ac8010a27901b8627.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xMo9kpKm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/2284279/29305340-83c8d058-8198-11e7-8057-742f904e2c1c.png)

### 同`electron-packager`一起发布

*   `icon`必须用`__dirname`指定(我们已经指定了)，这样`electron-packager`才能选择正确的图标