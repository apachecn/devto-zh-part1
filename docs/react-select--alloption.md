# 反应-选择+分配选项

> 原文：<https://dev.to/alex_escalante/react-select--alloption>

如果你正在使用 React 库 [react-select](https://github.com/JedWatson/react-select) ，你会发现它没有实现“全选”选项。自己实现这个功能并不难，看看下面的要旨就知道了。您甚至会发现如何在控件上制作本地化包装，以备不时之需。

看看相关的东西:

```
// specify props.allowSelectAll = true to enable!
const Select = props => {
  if (props.allowSelectAll) {
    if (props.value.length === props.options.length) {
      return (
        <ReactSelect
          {...props}
          value={[props.allOption]}
          onChange={selected => props.onChange(selected.slice(1))}
        />
      );
    }

    return (
      <ReactSelect
        {...props}
        options={[props.allOption, ...props.options]}
        onChange={selected => {
          if (
            selected.length > 0 &&
            selected[selected.length - 1].value === props.allOption.value
          ) {
            return props.onChange(props.options);
          }
          return props.onChange(selected);
        }}
      />
    );
  }

  return <ReactSelect {...props} />; }; 
```

Enter fullscreen mode Exit fullscreen mode

您可以在以下网址找到完整的要点:

[https://gist . github . com/AlexEscalante/251032 be 95767366742 FCE 75 bdfa 269 b](https://gist.github.com/AlexEscalante/251032be95767366742fce75bdfa269b)

如果你觉得这有用或有任何意见，请留言！