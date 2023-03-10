# 单元测试重组 hoc

> 原文：<https://dev.to/squgeim/unit-testing-recompose-hocs-3nh4>

我是[改编](https://github.com/acdlite/recompose)的超级粉丝。它允许我们编写纯粹的、功能性的、“愚蠢的”组件，允许我们在它提供的庞大 hoc 集合中转储所有的逻辑。太牛逼了。

我已经用了很多次了，有一件事一直困扰着我:如何正确地测试它们？

一方面，由于组件变得真正纯粹，一系列针对不同道具组合的快照测试几乎涵盖了它们。

mapStateToProps、mapStateToDispatch 和 mergeProps covers connect 的简单测试。

当涉及到一个特设，它变得有点棘手。

一种方法是对实际呈现的最终组件进行常规快照测试。但是这不是重复我们为纯组件编写的测试吗？既然我们知道他们在给定的道具下行为正常，我们就不需要担心他们。

就我个人所见，最常见的使用案例是，它从道具中获取输入，获取新信息或以某种方式转换该输入，并将输出作为道具包含到下一个组件中。

因此，如果我们只需要测试 HOC 的行为，我们真正关心的是对于给定的一组输入属性，它返回什么属性。或者，在基于 redux 的应用程序的情况下，对于给定的一组输入，它调度哪组动作(对于非 redux 应用程序，我还没有真正考虑过这个问题)。

想象一个用日期和天气向用户问候的组件。

> 你好，约翰！这是一个阳光明媚的星期天！

更好的是，让我们把它写成:

```
import React from 'react';
import { compose, withProps } from 'recompose';

import { getFirstName } from '../utils/name';
import { getDayFromDate } from '../utils/date';
import { getHumanReadableWeather } from '../utils/weather';

const Greeter = ({ firstName, day, weather }) => (
  <div>
      Hello, {firstName}! It is a {weather} {day}!
  </div>
);

/**
 * This HOC takes a more crude version of currentUser, date and
 * weather data and maps them to a version that is easily
 * used in the component. That way, the end component is not
 * dependent on the implementation detail or API response format
 * for these information.
 */
export const enhance = compose(
  withProps(props => ({
    firstName: getFirstName(props.currentUser.name),
    day: getDayFromDate(props.date),
    weather: getHumanReadableWeather(props.weather)
  }))
);

export default enhance(Greeter); 
```

我们现在需要测试的是增强器是否返回正确的道具。

*<旁注>这看起来像是一件微不足道的测试。关键是，当进行 TDD 时，测试是首先编写的，我们不能(在大多数情况下)预见实现会变得多么复杂。**</旁注>*

如果我不知道更好的，并被迫写一个测试，它会是这样的:

```
import React from 'react';
import renderer from 'react-test-renderer';

import Greeter from './greeter';

const weatherData = {
  weather: [{
    id: 804,
    main: "clouds",
    description: "overcast clouds",
    icon: "04n"
  }],
  main: {
    temp: 289.5,
    humidity: 89,
    pressure: 1013,
    temp_min: 287.04,
    temp_max: 292.04
  },
  wind: {
    speed: 7.31,
    deg: 187.002
  },
  rain: {
    '3h': 0
  },
  clouds: {
    all: 92
  },
};

it('should render a component with props name, day and weather', () => {
  const greeter = renderer.create(
    <Greeter
      currentUser={{ name: 'Shreya Dahal' }}
      date={new Date(1514689615530)}
      weather={weatherData}
    />
  ).toJSON();

  expect(greeter).toMatchSnapshot();
}); 
```

良好状态[快照测试](https://facebook.github.io/jest/docs/en/snapshot-testing.html)。

这里面有很多问题。

第一，我们依赖于渲染的内容来推断增强器返回的内容。我不同意我们从第二手资料推断我们逻辑的有效性。一个主要的问题是，我们渲染的组件可能不会使用所有传递的属性。这是一个问题，因为特设的目的是它可以在多个组件中重用；我们必须用多个组件测试同一个 HOC 才能看到全貌。

第二，我们不能这样做 TDD。快照测试适用于组件，因为我们并不真正 TDD 视图，但是编写逻辑是 TDD 的闪光点。

一个晴朗的夜晚，我懒洋洋地浏览着 [recompose 的 API 文档](https://github.com/acdlite/recompose/blob/master/docs/API.md)，看到了一个让我脑中充满幻想的方法。创作方法:

```
createSink(callback: (props: Object) => void): ReactClass 
```

> 创建一个组件，该组件不呈现任何内容(null ),但在接收新的属性时调用回调。

这个工厂函数接受一个回调并返回一个组件，该组件不呈现任何内容，但每次接收到任何道具时都会调用回调。因此，如果这个 sink 组件用一个 HOC 进行了增强，回调就可以准确地告诉我们这个 HOC 传入了什么道具。

所以我们可以这样做来测试上面欢迎者例子中的增强器:

```
import React from 'react';
import renderer from 'react-test-renderer';
import { createSink } from 'recompose';

import { enhance } from './greeter';

it('should render a component with props name, day and weather', () => {
  const sink = createSink(props => {
    // This callback will be called for each set of props passed to the sink
    // We can use `toMatchObject` to test if the given key-value pairs are
    // present in the props object.
    expect(props).toMatchObject({
      name: 'Shreya',
      day: 'sunday',
      weather: 'cloudy',
    });
  });

  const EnhancedSink = enhance(sink);

  renderer.create(
    <EnhancedSink
      currentUser={{
        name: 'Shreya Dahal',
      }}
      date={new Date(1514689615530)}
      weather={weatherData}
    />
  );
}); 
```

一个简单的数据输入，数据输出。TDD 走！

现在来看看有副作用的 hoc:在生命周期中分派动作的 hoc。

因此，有一个特设，获取一个给定的接触，并包括在道具消费线:

```
import React from 'react';
import { connect } from 'react-redux';
import { compose, lifecycle } from 'recompose';

// You'd probably have a proper selector instead
const getContactById = (state, id) => id && state.contacts[id] || {};

const withContact = compose(
  connect(
    (state, props) => ({
      contact: getContactById(state, props.contactId),
    }),
    dispatch => ({
      fetchContact(id) {
        dispatch(contactActions.fetchContact(id))
      },
    })
  ),
  lifecycle({
    componentDidMount() {
      // Fetch details for the given contactId on mount.
      this.props.fetchContact(this.props.contactId);
    },
    componentWillReceiveProps(nextProps) {
      // Fetch details for the new contactId if the contactId prop has changed.
      if (nextProps.contactId !== this.props.contactId) {
        this.props.fetchContact(nextProps.contactId);
      }
    }
  })
);

export default withContact; 
```

我们如何着手测试呢？

如果我们需要使用 connect，它将需要被包装在一个带有存储的提供者中。为此，我们可以使用 [redux-mock-store](https://github.com/arnaudbenard/redux-mock-store) 。然后，我们可以很容易地提取出所有已经被分派到模拟存储的动作的列表。

componentDidMount 中调度的测试动作很简单:

```
import React from 'react';
import renderer from 'react-test-renderer';
import configureStore from 'redux-mock-store';
import { Provider, connect } from 'react-redux';

import withContact from './withContact';
import * as contactActions from '../actions/contactActions';

const mockStore = configureStore([]);

// Component that renders nothing. Used as the end point of an HOC.
const NullComponent = () => null;

it('should dispatch a FETCH_CONTACT action on mount', () => {

  const store = mockStore({});

  const EnhancedSink = withContact(NullComponent);

  renderer.create(
    <Provider store={store}>
      <EnhancedSink contactId={214} />
    </Provider>
  );

  expect(store.getActions()).toContainEqual(
    contactActions.fetchContact(214)
  );
}); 
```

测试 componentWillReceiveProps 类似。我们可以使用 react-test-renderer 的 testInstance.update 方法，用不同的 props 重新渲染根组件，它会做正确的事情:为新组件调用 componentDidMount，为旧组件调用 componentWillReceiveProps。

```
it('should fetch a new contact when prop is changed', () => {
  const store = mockStore({});

  const EnhancedSink = withContact(NullComponent);

  const RootComponent = ({ id }) => (
    <Provider store={store}>
      <EnhancedSink contactId={id} />
    </Provider>
  );

  // First mount the component with first props
  const renderInstance = renderer.create(<RootComponent id={123} />);

  // Clear actions that may have been dispatched during mount.
  store.clearActions();

  // Then, change the props
  renderInstance.update(<RootComponent id={456} />);

  expect(store.getActions()).toContainEqual(
    contactActions.fetchContact(456)
  );
}); 
```

很好。

这看起来像是测试两个生命周期方法的大量代码，但是这些已经像这样被有意地分开了。didMount 和 willReceiveProps 测试可以进入同一个测试套件(describe block ),并且可能使用同一个 store、EnhancedSink 和 RootComponent。这也将极大地简化 willReceiveProps 块。我想说的是你可以用更简单的方法。

无论哪种方式，多花一点时间和精力编写测试(如果代码更简单，或者更好的是，如果没有代码的话)都是值得的。