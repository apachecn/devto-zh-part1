# 使用高阶组件制作一致的反应形式

> 原文：<https://dev.to/derekbrimley/making-consistent-react-forms-using-a-higher-order-component-2oc>

表单可能是 React 应用程序中一个棘手的部分。虽然有一个统一的方法来创建表单是很好的，但是表单也需要是可定制的。表单可以有不同的样式，使用不同的验证方法，并以不同的方式提交(即提交给 API 端点或典型的表单提交)。在我们的应用程序中，我们尝试了几种构造表单的方法，现在每种表单处理这些问题的方式都略有不同。我们决定提出一个可以在整个应用程序中使用的解决方案，它将足够灵活，可以处理不同的情况，但也提供有用的功能。

我们正在使用的模式在某些地方被称为子组件的函数。有些人把它贴上了反模式的标签，但是其他人认为它比普通的、令人厌烦的高阶组件更有能力。就目前而言，它是有效的。也许有一天我们会意识到我们方式的错误，并将其重构为未来酷的新模式。但今天不是那一天。

我们想要一个极简组件，它能为我们做一些事情:

1.  设置每个字段的默认值，并跟踪任何更改，以及更改是否被触动。
2.  返回一个带有错误信息的对象。
3.  跟踪表单是否可以提交。
4.  提供可用于调用提交函数的函数。

该函数的基本轮廓如下:

```
<FormContainer fieldDefaults={fieldDefaults} errorFuncs={errorFuncs} onSubmit={onSubmit}>
  {({ fields, errorValues, triggerSubmit, submitDisabled }) => {
    return(...)
  }}
</FormContainer> 
```

Enter fullscreen mode Exit fullscreen mode

因此，表单接受一组默认值、一组验证字段的函数和一个提交函数。该组件返回字段值列表、任何错误、触发提交的函数以及表单是否有效的布尔值。这样，您就可以按照自己的想法来构建表单，并且将来重新安排或更新表单域或逻辑也很容易。组件的定义相当简单。设置状态有点复杂，我就详细解释一下。

```
state = {
  fields: {
    ...Object.keys(this.props.fieldDefaults).reduce((acc, curr) => (
      {
        ...acc,
        [curr]: {
          value: this.props.fieldDefaults[curr],
          isDirty: false,
        },
      }
    ), {}),
  },
  errorFuncs: this.props.errorFuncs,
} 
```

Enter fullscreen mode Exit fullscreen mode

要理解这里发生了什么，你需要理解两件事。首先是 reduce 函数，你可以在这里读到。第二，对象析构，你可以在这里学到

基本上，这设置了表单的初始状态。容器在一个对象中发送，该对象包含字段名称和字段初始值的键值对。此函数创建一个带有关键字“field”的对象，其中每个字段都有一个对象。每个字段对象都有一个值(由容器指定)和一个初始“isDirty”值(false)。使用“isDirty”值是为了让容器知道用户是否已经更改了该字段，因此在此之前不会显示任何错误。函数运行后，状态可能如下所示:

```
{
  fields: {
    firstName: {
      value: '',
      isDirty: false,
    },
    lastName: {
      value: '',
      isDirty: false,
    },
    email: {
      value: '',
      isDirty: false,
    },
  },
  errorFuncs: { ... }
} 
```

Enter fullscreen mode Exit fullscreen mode

组件格式化它将发送回的数据，并通过使用参数呈现其子组件来发送数据:

```
return (
  this.props.children({
    fields, errorValues, onChange, triggerSubmit, submitDisabled
  })
); 
```

Enter fullscreen mode Exit fullscreen mode

onChange 函数在状态中设置新的字段值，并将“isDirty”字段设置为 true。

以这种方式解决 React 表单可以让您完全控制表单的显示方式，但是您仍然可以得到验证、错误和受控表单的所有好处。我们在表单中使用这个组件已经有一段时间了，我喜欢它的简单性和一致性。有什么事情你会做得不一样吗？关于以上如何工作的问题？请告诉我，我一直在寻求改进！