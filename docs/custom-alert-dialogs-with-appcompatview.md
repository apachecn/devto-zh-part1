# 带有自定义视图的警报对话框

> 原文：<https://dev.to/subbramanil/custom-alert-dialogs-with-appcompatview>

我们一直在使用 *AlertDialogs* 来提醒用户，就像任何 android 应用程序一样。警告对话框是通知用户需要执行的问题或操作的一种很好且简单的方式。然而，有好几次，我发现自己看到许多重复的警告对话框创建和管理逻辑，以及跨各种活动和片段的不一致的风格。为了解决这个问题，我跟随网上的一些好文章，使用 **MVP** 设计模式创建了一个小模块。

完成后，错误控制模块如下所示，

```
|  
|--- ErrorHandler  
|--- ErrorPresenter   
|--- ErrorPresenterImpl  
|--- ErrorInteractor  
|--- ErrorInteractorImpl  
|--- ErrorCodes  
|--- ErrorResources  
|--- MaterialDialog  
|--- DialogView  
|--- DialogType 
```

Enter fullscreen mode Exit fullscreen mode

ErrorHandler 是一个单独的程序，处理创建警告对话框的管理。MVP 设计模式使用*error interacter*(模型) *DialogView* (视图)*error Presenter*(Presenter)接口实现。我正致力于创建一个基于 **MVP** 实现的 android 库项目，我会写另一篇关于它的博客。

最后，创建警报对话框的代码片段如下所示，

```
 errorHandlingDialog = errorHandler.createDialog(DialogType.ERROR);
 errorHandlingDialog.setErrorCode(errorCode);        
 errorHandlingDialog.showOkayButton(true, okBtnTxt);
 errorHandlingDialog.setOnConfirmListener(okBtnlistener);        
 errorHandlingDialog.showCancelButton(true, cancelBtnTxt);
 errorHandlingDialog.setOnCancelListener(cancelBtnlistener);
 errorHandlingDialog.show(); 
```

Enter fullscreen mode Exit fullscreen mode

对话框创建如下，

```
 private void createDialog(DialogType dialogType) {
    AlertDialog.Builder builder = new AlertDialog.Builder(mContext, R.style.error_dialog_style);

    View mDialogView = LayoutInflater.from(mContext).inflate(R.layout.material_dialog, null, false);
    mDialogTitleView = (AppCompatTextView) mDialogView.findViewById(R.id.AlertDialogTitle);
    mDialogContentView = (AppCompatTextView) mDialogView.findViewById(R.id.AlertDialogContent);
    mDialogExtraContentView = (AppCompatTextView) mDialogView.findViewById(R.id.AlertDialogExtraContent);

    mDialogCancelBtn = (AppCompatButton) mDialogView.findViewById(R.id.AlertCancelButton);
    mDialogOkayBtn = (AppCompatButton) mDialogView.findViewById(R.id.AlertConfirmButton);

    builder.setView(mDialogView);

    switch (dialogType) {
        case ERROR:
            builder.setCancelable(false);
            break;
        case WARNING:
            builder.setCancelable(true);
            break;
        case INFO:
            builder.setCancelable(true);
            break;
    }
    mDialog = builder.create();
    mDialog.getWindow().setLayout(500, WindowManager.LayoutParams.WRAP_CONTENT);
} 
```

Enter fullscreen mode Exit fullscreen mode

布局文件如下所示，

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="@color/dialog_bgcolor"
    android:orientation="vertical"
    android:padding="@dimen/_30dp">
     <android.support.v7.widget.AppCompatTextView
        android:id="@+id/AlertDialogTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>

    <android.support.v7.widget.AppCompatTextView
        android:id="@+idAlertDialogContent"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>

    <android.support.v7.widget.AppCompatTextView
        android:id="@+id/AlertDialogExtraContent"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>

    <android.support.v7.widget.AppCompatButton
        android:id="@+id/AlertCancelButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/error_handling_cancel_text"/>

    <android.support.v7.widget.AppCompatButton
        android:id="@+id/AlertConfirmButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/error_handling_okay_text"/>

</RelativeLayout> 
```

Enter fullscreen mode Exit fullscreen mode

对于明显的眼睛来说，代码片段确实有意义，并且看起来是有效的。我们是这样认为的，而且它正在起作用。它通过了单元测试。然而，当我们开始将代码集成到应用程序中，并使用它来取代*重复的*警告对话框时，我们开始注意到一个奇怪的崩溃发生了。

```
java.lang.ClassCastException: android.widget.LinearLayout$LayoutParams cannot be cast to com.android.internal.widget.ActionBarOverlayLayout$LayoutParams 
```

Enter fullscreen mode Exit fullscreen mode

堆栈跟踪没有指向应用程序中的任何代码，而是指向 android 视图。我们意识到我们在某个地方搞砸了，但是我们没有想到要检查对话框创建代码。

在摆弄了应用程序代码并试图重现问题后，一名开发人员发现直到对话框出现时才发生崩溃。即，直到调用了`errorHandlingDialog.show()`。

进一步调试代码，我们发现下面一行代码导致了崩溃。

```
mDialog.getWindow().setLayout(500, WindowManager.LayoutParams.WRAP_CONTENT); 
```

Enter fullscreen mode Exit fullscreen mode

在谷歌的帮助下，我发现这个问题与布局参数和定位的错误使用有关。

> 有用的链接:
> 
> 1.  [警告对话框的布局参数](http://stackoverflow.com/questions/33171114/what-layoutparams-should-be-used-in-an-alertdialog)
> 2.  [指定自定义对话框视图尺寸](http://stackoverflow.com/questions/37514116/how-to-specify-correct-dialog-size-in-xml-layout-file-for-android-dialog)

我更新了代码如下，

```
mDialog = dialogBuilder.create();
Window window = mDialog.getWindow();
if (window != null) {
    window.setLayout(LinearLayout.LayoutParams.WRAP_CONTENT, LinearLayout.LayoutParams.WRAP_CONTENT);
    window.setGravity(Gravity.CENTER);
} 
```

Enter fullscreen mode Exit fullscreen mode

并将*布局 _ 宽度*和*布局 _ 高度*由*匹配 _ 父*修改为*换行 _ 内容*T8】

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">

</RelativeLayout> 
```

Enter fullscreen mode Exit fullscreen mode

现在代码更有意义了，我想我完成了。然而，我遇到了另一个令人惊讶和奇怪的问题(因为我首先不理解代码)。

```
java.lang.IllegalStateException: ActionBarView can only be used with android:layout_width="match_parent" (or fill_parent) 
```

Enter fullscreen mode Exit fullscreen mode

堆栈跟踪再次没有指向应用程序，但这次我准备好了。

我意识到问题在于设置视图的宽度。我之前没有为提醒对话框使用任何样式，我决定使用它。

> 有用的链接:
> [为提醒对话框提供有效的父主题](https://issuetracker.google.com/issues/36931487)

我创建了一个如下的样式并应用于提醒对话框。

```
<style name="AppTheme.Dialog" parent="android:Theme.Light">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowFrame">@null</item>
    <item name="android:windowIsFloating">true</item>
    <item name="android:windowContentOverlay">@null</item>
    <item name="android:maxWidth">500dp</item>
    <item name="android:windowAnimationStyle">@android:style/Animation.Dialog</item>
</style>

<style name="error_dialog_style" parent="@style/AppTheme.Dialog">

</style> 
```

Enter fullscreen mode Exit fullscreen mode

```
AlertDialog.Builder dialogBuilder = new AlertDialog.Builder(mContext, R.style.error_dialog_style); 
```

Enter fullscreen mode Exit fullscreen mode

这最终修复了非法状态异常崩溃。最后，我们的应用程序现在有了漂亮统一的警告对话框，减少了重复代码。 ***万岁！！*T3】**