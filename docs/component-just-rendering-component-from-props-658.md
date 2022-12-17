# 组件只是从道具渲染组件

> 原文：<https://dev.to/eccyan/component-just-rendering-component-from-props-658>

我这样写组件:

```
import React from 'react';
import PropTypes from 'prop-types';

class Renderer extends React.Component {
  constructor(props) {
    super(props);
  }

  render() {
    return React.createElement(this.props.component, this.props);
  }

  static propTypes = {
    component: PropTypes.element.isRequired,
  };
}

export default Renderer; 
```

Enter fullscreen mode Exit fullscreen mode

这个组件可以这样使用:

```
import Renderer from './components/renderer'
import AwesomeComponent from './components/awesome_component'
import hoc from './components/awesome_hoc'

() => {
  render <Renderer component={hoc(AwesomeComponent)} />; } 
```

Enter fullscreen mode Exit fullscreen mode

我使用`<Renderer />`的原因是我不想在 JSX 使用`React.createElement`。
我不应该这样定义无用的常数:

```
import Renderer from './components/renderer'
import AwesomeComponent from './components/awesome_component'
import hoc from './components/awesome_hoc'

() => {
  const HocComponent = hoc(AwesomeComponent);
  render <HocComponent />;
} 
```

Enter fullscreen mode Exit fullscreen mode

有人知道这种组件的名称吗？