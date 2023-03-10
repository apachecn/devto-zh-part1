# WPF Xaml 的替代方案

> 原文：<https://dev.to/julienju2pom/an-alternative-to-xaml-for-wpf>

# 流体

FluentWPF 是一个提供替代 Xaml 的库。

# 为什么？

*   减少冗长
*   提供更大的灵活性
*   提高可重用性
*   利用工具和 C#语言功能
*   简化单元测试

# 如何

*   提供流畅的 API
*   使这个库易于扩展
*   重用现有的 WPF 类型
*   提供许多示例用法

# 什么

让我们看看一些代码是什么样子的。

## 简单窗口

```
new Window()
  .AsFluent<Window>()
  .DataContext(new RootViewModel())
  .NoBorder()
  .NoResize() 
```

我希望代码非常简洁，但是让我们编写 xaml 的等价代码:

```
<Window
  x:Class="SomeNameSpace.MyWindow"
  xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
  xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
  xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
  xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
  xmlns:local="clr-namespace:SomeNameSpace.ViewModels"
  ResizeMode="NoResize"
  WindowStyle="None"
  >
  <Window.DataContext>
   <local:RootViewModel />
  </Window.DataContext> 
```

## 对齐很容易

```
new Expander()
  .AsFluent()
  .Top()
  .Size(400, 400) 
```

事实上，对齐`Left`或`Right`或`Center`或`Bottom`一样简单。

## 设置一个属性

```
new TextBlock()
  .AsFluent()
  .Set(FrameworkElement.MarginProperty, new Thickness(4))
  .Set(TextBlock.TextProperty, "Some text")) 
```

## 试验绑定

```
new Button()
  .AsFluent()
  .Contains("Button1")
  .Bind(BindingExtensions
    .OneTime(ButtonBase.CommandProperty)
    .With(nameof(RootViewModel.SimpleCommand))) 
```

绑定 API 应该大大简化绑定语法。你当然可以绑定`OneWay`或者`TwoWay`或者`OneWayToSource`或者`OneTime`。
您可以使用`With`定义绑定源，并指定 DataContext 属性的路径或使用`WithSelf`绑定到自身。

## 绑定和转换器

```
new ProgressBar()
  .AsFluent()
  .Bind(BindingExtensions
    .OneWay(RangeBase.ValueProperty)
    .With(nameof(SomeViewModel.Progress))
    .Convert(x => Math.Round((double) x))) 
```

如果你想让你的视图模型的属性适应你的视图，你仍然可以提供一个`IValueConverter`，但是你也可以提供一个 lambda 表达式或者一个方法，并且保持它的简单。

## 创建一个网格并填充它

```
new Grid()
  .AsFluent()
  .DefaultCellSize("*", "*")
  .Cell(GridCellExtensions.Create()
    .Contains(new Button()
      .AsFluent()
      .Contains("Button1")))
  .Cell(GridCellExtensions.Create()
    .Column(1)
    .Contains(new Button()
      .AsFluent()
      .Contains("Button2"))) 
```

网格 API 旨在简化网格处理。例如，你不需要首先声明行和列。如果需要，您可以定义默认的宽度和高度，并为任何单元格指定一个特定的值。上面的例子仅仅展示了网格 API 的一小部分

## 简单风格创作

```
Style aCheckBox = StyleExtensions.Create()
    .Set(Control.ForegroundProperty, new SolidColorBrush(Colors.White))
    .Set(Control.BackgroundProperty, new SolidColorBrush(Colors.Gray))
    .AsStyle<CheckBox>(); 
```

这相当于:

```
<Style x:Key="ACheckBox" TargetType="CheckBox">
  <Setter Property="Foreground" Value="White" />
  <Setter Property="Background" Value="Gray" />
</Style> 
```

嗯，这是一个非常简单的例子，FluentWPF 不是更短，甚至有点冗长。但是等等，触发器 API 和主题 API 会有很大的帮助！

## 带触发器的样式

```
Style aCheckBox = StyleExtensions.Create()
  .Set(Control.ForegroundProperty, new SolidColorBrush(Colors.Gray))
  .When(TriggerExtensions
    .Property(ToggleButton.IsCheckedProperty)
    .Is(true)
    .Then(Control.FontWeightProperty, FontWeights.Bold)
    .Then(Control.ForegroundProperty, new SolidColorBrush(Colors.White))
  .AsStyle<CheckBox>(); 
```

这相当于:

```
<Style x:Key="ACheckBox" TargetType="CheckBox">
  <Setter Property="Foreground" Value="White" />
  <Style.Triggers>
    <Trigger Property="IsChecked">
      <Setter Property="FondWeight" Value="Bold" />
      <Setter Property="Foreground" Value="White" />
    </Trigger>
  </Style.Triggers>
</Style> 
```

## 简单控件模板

```
ControlTemplate template = TemplateExtensions.Create<Border>()
  .Set(FrameworkElement.HorizontalAlignmentProperty, HorizontalAlignment.Stretch)
  .Contains(TemplateExtensions.Create<ContentPresenter>())
  .AsControlTemplate<ListBoxItem>(); 
```

这是一个非常简单的 ListBoxItem 元素的 ControlTemplate。

## 数据模板也很简单

```
DataTemplate template = TemplateExtensions.Create<Border>()
  .Set(FrameworkElement.HorizontalAlignmentProperty, HorizontalAlignment.Stretch)
  .Contains(TemplateExtensions.Create<ContentPresenter>())
  .AsDataTemplate<MyClass>(); 
```

您可以使用与 ControlTemplate 相同的 API 来创建 DataTemplate。

## 带模板绑定的控件模板

```
ControlTemplate template = TemplateExtensions.Create<Border>()
  .Set(FrameworkElement.HorizontalAlignmentProperty, HorizontalAlignment.Stretch)
  .Contains(TemplateExtensions.Create<ContentPresenter>())
  .TemplateBinding(Border.BackgroundProperty, ListBoxItem.BackgroundProperty)
  .AsControlTemplate<ListBoxItem>(); 
```

## 主题化？

正在进行的工作也将提供一个流畅的 API 来简化主题创建。
API 将:

*   提供一种定义调色板的方法
*   为任何控件类型提供一种定义默认样式的简单方法
*   负责资源字典加载

# 路线图

*   涵盖与 XAML 相同的功能
    *   支持模板/样式
    *   支持绑定/转换器
    *   支持动画
    *   支持触发器
    *   支持行为
*   编写样本
*   提供一个代码友好的主题引擎
*   更多样本
*   提供 Visual studio 代码片段
*   API 文档(或更多样本)
*   将 API 功能扩展到 XAML 之外
    *   利用 lambdas(触发器？)
    *   支持样式的继承/混合
*   进一步减少 API 冗长

# 反馈

现在 FluentWPF 还没有完成，也没有发布，但是我很想从社区中得到关于这个项目的反馈。
我计划发布 FluentWPF，作为一个捆绑了一个非常高级的演示应用程序的库。
许可证还没有定义，我还不确定是否开源。