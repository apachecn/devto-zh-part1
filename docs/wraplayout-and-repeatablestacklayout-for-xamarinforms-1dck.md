# Xamarin 的 WrapLayout 和 RepeatableStackLayout。形式

> 原文：<https://dev.to/muak_x/wraplayout-and-repeatablestacklayout-for-xamarinforms-1dck>

现在，Xamarin。窗体没有像 WPF WrapLayout 那样在视图边界上换行的布局。

所以我把布局库命名为 AiForms.Layout。

## 演示

### repeatablewrapplayout

[https://www.youtube.com/embed/9kE-OA9jz8c](https://www.youtube.com/embed/9kE-OA9jz8c)

### 重复表栈

[https://www.youtube.com/embed/pKUF1dDYvtE](https://www.youtube.com/embed/pKUF1dDYvtE)

## 储存库

[https://github.com/muak/AiForms.Layouts](https://github.com/muak/AiForms.Layouts)

## Nuget 包

[https://www.nuget.org/packages/AiForms.Layouts/](https://www.nuget.org/packages/AiForms.Layouts/)

### 安装

```
Install-Package AiForms.Layouts 
```

您需要将此软件包安装到。NETStandard / PCL 项目和每个平台项目。

#### iOS

如果不使用 XamlCompilationOptions。编译，需要在 AppDelegate.cs 中写以下代码；否则用不着。

```
public override bool FinishedLaunching(UIApplication app, NSDictionary options) {
    global::Xamarin.Forms.Forms.Init();

    AiForms.Layouts.LayoutsInit.Init();  //need to write here

    LoadApplication(new App(new iOSInitializer()));

    return base.FinishedLaunching(app, options);
} 
```

## 概述

### 包装布局

WrapLayout 水平排列元素，在父视图边界自动换行。
除了换行功能之外，还有通过指定一行中的元素数量来自动使每个元素宽度一致的功能。

#### 参数

*   间隔
    *   元素之间的边距。
*   统一列
    *   如果指定大于 0，子宽度将是父宽度除以该数字的宽度。
*   伊斯夸尔
    *   指定 true，子高度将等于其宽度。

#### 如何用 Xaml 写

```
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:l="clr-namespace:AiForms.Layouts;assembly=AiForms.Layouts"
        x:Class="Sample.Views.MainPage">
    <StackLayout>
        <l:WrapLayout Spacing="4" UniformColumns="3" IsSquare="true" HorizontalOptions="FillAndExpand">
            <BoxView Color="Red" />
            <BoxView Color="Blue" />
            <BoxView Color="Green" />
            <BoxView Color="Black" />
            <BoxView Color="Yellow" />
        </l:WrapLayout>
    </StackLayout>
</ContentPage> 
```

### repeatablewrapplayout

RepeatableWrapLayout 是与 DataTemplate 和 DataTemplateSelector 相对应的 WrapLayout。

#### 参数

*   ItemTapCommandProperty
    *   点击元素时调用的命令。
*   项目源
    *   什么是 ListView ItemsSource。
*   项目模板
    *   什么是 ListView ItemTemplate。

#### 如何写入 Xaml

```
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:l="clr-namespace:AiForms.Layouts;assembly=AiForms.Layouts"
        x:Class="Sample.Views.MainPage">
    <StackLayout>
        <ScrollView HorizontalOptions="FillAndExpand">
            <l:RepeatableWrapLayout
                ItemTapCommand="{Binding TapCommand}"
                ItemsSource="{Binding BoxList}"
                Spacing="3" UniformColumns="{Binding UniformColumns}"
                IsSquare="{Binding IsSquare}" >
                <l:RepeatableWrapLayout.ItemTemplate>
                    <DataTemplate>
                        <StackLayout BackgroundColor="{Binding Color}" >
                            <Label
                                VerticalTextAlignment="Center" HorizontalTextAlignment="Center"
                                Text="{Binding Name}"  />
                        </StackLayout>
                    </DataTemplate>
                </l:RepeatableWrapLayout.ItemTemplate>
            </l:RepeatableWrapLayout>
        </ScrollView>
    </StackLayout>
</ContentPage> 
```

### 重复表栈

RepeatableStack 是对应于 DataTemplate 和 DataTemplateSelector 的 StackLayout。

#### 参数

*   项目源
*   项目模板

#### 如何写入 Xaml

```
<!-- Horizontal -->
<ScrollView Orientation="Horizontal" HeightRequest="86">
<al:RepeatableStack Orientation="Horizontal" ItemsSource="{Binding BoxList}" HeightRequest="86">
    <al:RepeatableStack.ItemTemplate>
        <DataTemplate>
            <ContentView BackgroundColor="{Binding Color}" WidthRequest="80" HeightRequest="80" Padding="3" />
        </DataTemplate>
    </al:RepeatableStack.ItemTemplate>
</al:RepeatableStack>
</ScrollView>

<!-- Vertical -->
<ScrollView>
<al:RepeatableStack Orientation="Vertical" ItemsSource="{Binding BoxList}">
    <al:RepeatableStack.ItemTemplate>
        <DataTemplate>
            <ContentView BackgroundColor="{Binding Color}" WidthRequest="80" HeightRequest="80" Padding="3" />
        </DataTemplate>
    </al:RepeatableStack.ItemTemplate>
</al:RepeatableStack>
</ScrollView> 
```