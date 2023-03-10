# Android 视图黄油刀简介

> 原文：<https://dev.to/lemuelogbunude/intro-to-butter-knife-for-android-views-d4e>

## 黄油刀？

黄油刀是一个非常不错的 Android 视图注入库。Butter knife 有助于减少大量样板代码(例如重复的 findViewById 调用)。如果您处理过具有大量视图的活动，您就会知道当您的代码与“findViewById”聚集在一起时会变得多么笨拙。如果你有这个问题，黄油刀是一个很好的图书馆，这将是一个关于如何使用黄油刀的介绍。

即使你不想使用它，有一个想法也是好的，这样当你的 Android 朋友在谈论黄油刀时，你就不会想到面包ðÿ˜。

一张图片代表一千个单词，一段代码代表一万个单词...ðŸ˜ .所以我会举例说明什么时候用黄油刀，什么时候不用，这样你就能很容易看出区别。在理解了这里将要演示的内容之后，你可以通过学习更多关于这个库的知识来扩展你的知识。

只是让你知道，Butter knife 不会以任何方式减慢你的应用程序，因为它使用编译时注释，因此在运行时没有额外的开销。

## 黄油刀前

我将要使用的界面在下面，它有助于你理解正在发生的事情。

UI 中有一个文本视图和两个按钮。我们要做两件事，有和没有黄油刀，这是设置按钮上的 onClick 监听器和编辑文本视图ðÿ˜‹.

# 用户界面

[![Mountain View](img/ded305b50f2969ccc41c9e354ed8c84a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uu9rO7Km--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/71z2bgg62fs01tqec76w.PNG)T3】

我希望您在 XML 中注意的唯一重要的事情是“android:id”值，它指向视图的 id。

下面是这个视图的 XML 代码:

```
 <Button
        android:id="@+id/Abtn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_alignParentStart="true"
        android:layout_marginBottom="203dp"
        android:layout_marginStart="48dp"
        android:text="Button" />

    <Button
        android:id="@+id/Bbtn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentEnd="true"
        android:layout_alignTop="@+id/Abtn"
        android:layout_marginEnd="24dp"
        android:text="Button" />

    <TextView
        android:id="@+id/BKtxt"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentTop="true"
        android:layout_marginEnd="28dp"
        android:layout_marginTop="74dp"
        android:layout_toStartOf="@+id/Bbtn"
        android:text="ButterKnife"
        android:textSize="20sp" /> 
```

Enter fullscreen mode Exit fullscreen mode

有了这个，如果我想为第一个按钮设置一个 onClick 监听器，它有一个 ID“Abtn”，这就是我不使用 Butter Knife 可能做的事情:

```
public class MainActivity extends AppCompatActivity {

    Button firstButton;//Button variable
    TextView BKtxt;//TextView variable

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        firstButton = (Button) findViewById(R.id.Abtn);//Button
        BKtxt = (TextView) findViewById(R.id.BKtxt);//Textview

        firstButton.setOnClickListener(new View.OnClickListener() {//set onclick listener for Button
            @Override
            public void onClick(View view) {
                BKtxt.setText("It works");
            }
        });

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

因此，当第一个按钮被点击时，它实际上将文本视图的文本更改为“它工作了”。不错吧。好吧，让我们试试用黄油刀ðÿ”。

## 使用黄油刀

为了使用黄油刀，你必须添加一些依赖。
将以下内容添加到 **app/build.gradle** 文件

```
dependencies {

//bla bla bla
//.
//.
//.

    compile 'com.jakewharton:butterknife:8.4.0'
    annotationProcessor 'com.jakewharton:butterknife-compiler:8.4.0'
} 
```

Enter fullscreen mode Exit fullscreen mode

还要确保升级到最新的 Gradle 版本，以使用 annotationProcessor 语法ðÿ'š.

下面的例子是用黄油刀完成的。

```
public class MainActivity extends AppCompatActivity {

    @BindView(R.id.BKtxt)
    TextView BKtxt;//This is all we need for the text view 

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ButterKnife.bind(this);//important to add this under super.onCreate and setContentView
    }

    @OnClick(R.id.Abtn)
    public void firstclick(Button button)
    {
        BKtxt.setText("It works");
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

它还可以做很多有趣的事情，例如，我可以这样做，而不是为每个按钮创建点击监听器:

```
 @OnClick({R.id.Abtn,R.id.Bbtn})//the two buttons now use the same onClick
    public void firstclick(Button button)
    {
        BKtxt.setText("It works");
    } 
```

Enter fullscreen mode Exit fullscreen mode

您还可以将视图分组，并作为一个组对其执行操作:

```
// Group the views together
   @BindViews({ R.id.Abtn,R.id.Bbtn})
    List<Button> daButtons; 
```

Enter fullscreen mode Exit fullscreen mode

所以，如果你还没有尝试过，那就试试吧！对于其他只是想有一个想法的人来说，这是好的。你至少知道它的意思和优点。继续阅读更多关于它，它真的是一个很好的工具ðÿ'。