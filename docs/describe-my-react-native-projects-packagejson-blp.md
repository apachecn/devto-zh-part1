# 描述我的 React 原生项目的 package.json

> 原文：<https://dev.to/vsanna/describe-my-react-native-projects-packagejson-blp>

```
{
    "name": "missus",
    "version": "0.0.1",
    "private": true,
    "scripts": {
        "start": "node node_modules/react-native/local-cli/cli.js start",
        "test": "jest",
        "flow": "node node_modules/flow-bin/cli.js",
        "devtool": "react-devtools"
    },
    "dependencies": {
        "react": "16.0.0",
        "react-native": "0.50.4",
        "react-native-message-bar": "^2.0.7",
        "react-native-router-flux": "^4.0.0-beta.24",
        "react-native-vector-icons": "^4.4.2",
        "react-redux": "^5.0.6",
        "redux": "^3.7.2",
        "redux-logger": "^3.0.6"
    },
    "devDependencies": {
        "babel-jest": "21.2.0",
        "babel-plugin-transform-class-properties": "^6.24.1",
        "babel-preset-es2015": "^6.14.0",
        "babel-preset-react-native": "4.0.0",
        "babel-preset-stage-3": "^6.24.1",
        "flow-bin": "^0.59.0",
        "jest": "21.2.1",
        "react-test-renderer": "16.0.0",
        "reactotron-react-native": "^1.13.2",
        "reactotron-redux": "^1.13.0",
        "remote-redux-devtools": "^0.5.12"
    },
    "jest": {
        "preset": "react-native"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode