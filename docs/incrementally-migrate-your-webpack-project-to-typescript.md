# 将 webpack 项目增量迁移到 TypeScript

> 原文：<https://dev.to/bartw/incrementally-migrate-your-webpack-project-to-typescript>

### TL；速度三角形定位法(dead reckoning)

如果你想迁移到 [TypeScript](https://www.typescriptlang.org/) 但是不想同时迁移你所有的代码，你可以配置 [webpack](https://webpack.github.io/) 来捆绑 TypeScript 和 JavaScript 代码。

你可以在这里找到一个演示项目[。](https://github.com/bartw/incrementally_migrate_to_typescript)

### 如何？

要将 TypeScript 添加到 webpack 项目中，可以按照下列步骤操作:

安装所需的开发依赖项:

```
npm install --save-dev @types/jest awesome-typescript-loader typescript @types/lodash 
```

Enter fullscreen mode Exit fullscreen mode

创建一个 tsconfig.json 文件，指定您想要将文件传输到 ES6:

```
{
  "compilerOptions": {
    "target": "es6"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

将 awesome-typescript-loader 添加到您的 webpack 配置文件中，首先通过 typescript transpiler 加载您的 TypeScript，然后通过 Babel:

```
module: {
  rules: [
    { test: /\.jsx?$/, exclude: /node_modules/, use: "babel-loader" },
    { 
      test: /\.tsx?$/, 
      exclude: /node_modules/, 
      use: ["babel-loader", "awesome-typescript-loader" }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

为了阻止 Babel 将 ES6 模块语法转换成 commonjs，您需要更新。babelrc 文件:

```
{
  "presets": [["es2015", { "modules": false }], "react"],
  "plugins": ["transform-class-properties"]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在到了棘手的部分:保持 Jest 测试运行。为了将 Jest 告知 transpile TypeScript 并让普通的 JavaScript 在 Babel-Jest 中运行，需要一些额外的配置。

在 package.json 中添加以下 Jest 配置:

```
"jest": {
  "moduleFileExtensions": ["ts", "tsx", "js", "jsx"],
  "transform": {
    "^.+\\.(ts|tsx)$": "<rootDir>/preprocessor.js",
    "^.+\\.(js|jsx)$": "babel-jest"
  },
  "testRegex": "(/ __tests__ /.*|\\.(test|spec))\\.(ts|tsx|js|jsx)$"
} 
```

Enter fullscreen mode Exit fullscreen mode

创建一个 preprocessor.js 文件来传输类型脚本测试:

```
const tsc = require("typescript");

module.exports = {
  process(src, path) {
    if (path.endsWith(".ts") || path.endsWith(".tsx")) {
      return tsc.transpile(src, {}, path, []);
    }
    return src;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

英寸 babelrc 您需要为测试环境添加一个单独的配置，以便更好地使用 Jest:

```
{
  "presets": [["es2015", { "modules": false }], "react"],
  "plugins": ["transform-class-properties"],
  "env": {
    "test": {
      "presets": ["es2015", "react"],
      "plugins": ["transform-class-properties"]
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该能够开始将文件一个接一个地迁移到 TypeScript，而不会中断测试或构建。