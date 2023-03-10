# 演示:🚨警惕！警惕！

> 原文：<https://dev.to/thinknimble/presenting-alert-alert-667>

我构建了一个独立的 JavaScript 库来为任何 web 项目添加 UI 通知。我称之为:“警戒！警报！”

*   [Codepen 上的演示](https://codepen.io/whusterj/pen/qEWMwG)。
*   [Github 上的代码](https://github.com/whusterj/alert-alert)。
*   [VueJS 插件](https://www.npmjs.com/package/vue-alert-alert)

你需要在使用前编译 LESS CSS 我最终会添加一个 Gruntfile 什么的。

用起来很简单:

```
Alert.alert('info', 'A message', {timeout: 7000}); 
```

最后一个参数是可选的。如果超时，通知将在给定的毫秒数后消失。

这不是什么突破性的东西，但我对那些似乎将如此琐碎的事情过于复杂化的选项感到沮丧。

* * *

### 完整源代码

下面是完整的源代码，总共大约 150 行 JavaScript 和 CSS(更少)。

**alert-alert.js**

```
'use strict';

module.exports = (function () {

  var container,
      CONTAINER_ID = 'aa-container',
      ALERT_CLASS = 'aa-notification';

  exports = {
    alert: _alert
  };

  return exports;

  /////////////////////////////////////////
  // functions

  function _alert (type, message, config) {
    if (typeof(config) === 'undefined') { config = {}; }
    if (!container) { container = _genNotificationContainer(); }
    container.appendChild(
      _genAlertDiv(type, message, config.timeout)
    );
  }

  function _genNotificationContainer () {
    if (container) { return; }
    var containerDiv = document.createElement('div');
    containerDiv.id = CONTAINER_ID;
    document.body.appendChild(containerDiv);
    return containerDiv;
  }

  function _genAlertDiv (type, message, timeout) {
    var alertDiv = document.createElement('div');
    alertDiv.className = ALERT_CLASS + '  ' + type;
    alertDiv.innerHTML = message;

    //
    alertDiv.addEventListener('click', _alertClickHandler);

    //
    if (timeout) {
      alertDiv.timeout = setTimeout(
        function () {
          _removeAlert(alertDiv);
        }, timeout); 
    }

    return alertDiv;
  }

  function _removeAlert (alert) {
    window.clearTimeout(alert.timeout);
    container.removeChild(alert);
  }

  function _alertClickHandler (event) {
    _removeAlert(event.currentTarget);
  }

})(); 
```

**alert-alert.css**

```
#aa-container {
  position: fixed;
  top: 12px;
  right: 12px;
  z-index: 1001;
}

.aa-notification {
  width: 300px;
  min-height: 40px;
  padding: 12px;
  margin-bottom: 12px;
  background-color: white;
  border-left: 4px solid rgb(100, 100, 100);
  border-radius: 3px;
  cursor: pointer;
  box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.6);
  transition: 0.3s linear all;
}

.aa-notification:hover {
  transform: scale(1.06);
  box-shadow: 3px 3px 4px gba(0, 0, 0, 0.6);
}

.aa-notification.info {
  border-left-color: rgb(100, 100, 200);
}

.aa-notification.success {
  border-left-color: rgb(100, 180, 100);
}

.aa-notification.warning {
  border-left-color: rgb(230, 215, 100);
}

.aa-notification.error {
  border-left-color: rgb(230, 100, 100);
} 
```