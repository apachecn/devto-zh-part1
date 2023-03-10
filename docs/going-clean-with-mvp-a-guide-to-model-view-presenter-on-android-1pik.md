# 使用 MVP 清理| Android 上的模型-视图-演示者指南

> 原文：<https://dev.to/xuhaibahmad/going-clean-with-mvp-a-guide-to-model-view-presenter-on-android-1pik>

> 面对现实吧！我们所有人都在`MainActivity.java`里面写了成吨成吨的代码，很多人现在仍然这样做…

当你开始使用一种新的语言或框架时，编写能够“工作”的代码是可以接受的。毕竟，最重要的是完成工作，对吗？不完全是！

我们都从同一个地方开始，每个“你好，世界”都标志着一项成就。然而，随着你成长为一个有经验的开发人员，你开始意识到面向对象编程不仅仅是写代码让*工作*。

几乎每个项目在其整个生命周期中都需要某种程度的测试和维护。这使得为未来的合作者编写易于阅读、修改、测试和维护的模块化代码成为一个严格的要求。

> 编写代码的时候，要把最终维护你的代码的人想象成一个知道你住哪儿的暴力精神病患者。约翰·伍兹

显然，我们的*“我爱`MainActivity`”*的家伙不会发现这是一项容易的任务。为什么？因为当你在类中需要的地方编写每一段代码时。你最终得到了一个代码库，当然，它完成了工作，但是从长远来看，它几乎不可能进行测试和维护。

* * *

## 模型-视图-演示者

MVP 是一种架构模式，它试图通过在单独的表示层后面提供更简单的活动和所有重要的工作来将业务逻辑从视图中分离出来。这导致了高度可读、可维护和最重要的可测试的代码。

有些人可能认为 MVP 本质上不是一种架构模式，因为它只负责管理数据的表示。然而，我们离开这里的辩论，继续到“*为什么？*“方面。

## 需要 MVP？

Android 活动充斥着紧密耦合的组件，一切都以*“把代码扔在墙上”*的方式构建。这使得 android 代码难以测试和扩展成为一个固有的问题。任何经历过学习 android 应用程序测试的痛苦的人都可以告诉你，在开始的时候，由于不可测试的*(这甚至是一个词吗？)* SDK 组件和生命周期管理的复杂性。

当您在一个单独的表示层中将业务逻辑从活动中分离出来时，像测试任何其他 java 类一样模拟和测试您的代码就变得很简单了。

除了代码测试，另一个困难是现有代码库的维护。如果你正在阅读这篇文章，并且理解我到目前为止所说的话，我假设你已经熟悉了由[罗伯特·c·马丁也就是鲍勃叔叔](https://en.wikipedia.org/wiki/Robert_Cecil_Martin)撰写的[坚实的](https://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29)面向对象设计原则。五项原则的最后一部分指的是*依赖倒置原则(DIP)* ，它鼓励程序员以一种**依赖抽象而非具体实现的方式编写代码**。

如今 MVP 的实现方式(或者至少是我学会的方式)是通过描述`Views`和`Presenters`职责的`Contract`接口，使得代码的维护简单到只需在相应的类中向接口及其实现添加一个新方法。请注意，这种分离还允许我们为相同的业务逻辑提供可互换的视图。

现在让我们通过构建一个非常基础的 ***hello world MVP app*** 来为理论上的东西划上一个句号，并探索模型、视图和 Presenter 的实现细节。

* * *

在我们继续之前，重要的是你要明白没有 ***【终极】*** 或 ***【最佳】*** 实现 MVP 的方法。大多数人使用他们自己的实现，只做一些修改，只要最终目标达到，这是完全可以接受的。也就是构建您的代码，将实现逻辑从表示层中分离出来。我正在做的方式只是我喜欢的方式，如果你发现它的另一种变化，不要混淆，使用适合你的风格。

## 模型

模型是负责管理数据的接口。Model 的职责包括使用 API、缓存数据、管理数据库等等。该模型也可以是与负责这些职责的其他模块进行通信的接口。

为了简单起见，我在这个例子中没有使用任何模型类，但是我希望你明白这个想法。

## 查看

不要把这和你的观点混淆。MVP 中的视图只是负责管理用户界面的组件。这包括所有的 UI 操作，比如设置文本到某个`TextView`，切换`Views`的可见性，显示动画等等。基本上，所有与用户界面有关的东西都放在这个地方。

> 尽可能让你的观点保持沉默。

这个想法是让你的`Presenters`不知道“如何”向用户呈现数据。他们所拥有的只是一个`View`实例，该实例将这些数据作为输入并执行表示任务。因此，如果您要向用户显示一个错误，以防他忘记填写表单中的某个字段，那么您的演示者所要做的就是调用`View`实例上的`view.displayError(errorMessage)`方法。

现在意识到`Presenter`不知道接下来会发生什么，错误是显示为`Toast`，还是显示为`Dialog`或`Snackbar`，或者消息是什么颜色等等。另一方面，`View`不知道它所提供的数据及其来源，它只对接受所提供的数据感兴趣，并相应地操纵 UI。这使得在测试你的业务逻辑时很容易模仿`Views`，这样你就可以专注于重要的东西。

我喜欢定义我的`Views`的方式是通过一个*视图接口*，它驻留在那个特定活动的*契约接口*中。

```
interface View extends BaseView<Presenter> {

    void displayMessage(String text);

    void displayResult(int result);
} 
```

现在我所要做的就是创建一个新的类，让它实现这个接口，这样我们就有了一个候选对象，它可以作为我们活动的视图。我猜你可能已经猜到了，我们可以为不同的用例提供多种视图实现。

```
public class MainActivity extends AppCompatActivity implements ContractMain.View{

    @BindView(R.id.toolbar)
    Toolbar mToolbar;
    @BindView(R.id.container)
    CoordinatorLayout mContainer;
    @BindView(R.id.numberOneEditText)
    EditText mNumberOneEditText;
    @BindView(R.id.numberTwoEditText)
    EditText mNumberTwoEditText;
    @BindView(R.id.resultTextView)
    TextView mResultTextView;

    private ContractMain.Presenter mPresenter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(this);
        setSupportActionBar(mToolbar);

        mPresenter = new MainPresenter(this, this);
        mPresenter.start();
    }

    @Override
    public void displayMessage(String text) {
        Snackbar.make(mContainer, text, Snackbar.LENGTH_LONG).show();
    }

    @Override
    public void setPresenter(ContractMain.Presenter presenter) {
        mPresenter = presenter;
    }

    @Override
    public ContractMain.Presenter getPresenter() {
        return mPresenter;
    }

    @OnClick(R.id.addButton)
    public void onAddButtonClicked() {
        String numberOne = mNumberOneEditText.getText().toString();
        String numberTwo = mNumberTwoEditText.getText().toString();

        mPresenter.performAddition(numberOne, numberTwo);
    }

    @Override
    public void displayResult(int result) {
        mResultTextView.setText(String.valueOf(result));
    }
} 
```

## 主讲人

把主持人想象成这里的 ***老板*** 。与您在 ***`I Love MainActivity`*** 时代管理活动中的一切类似，现在演示者处理所有管理任务，让您的活动/片段不那么繁忙。因此，不要把你的`DatabaseHandler`、`ApiHandler`、`PeanutButterMaker`和诸如此类的实例扔到 activity 类中，让它看起来像一大堆蹩脚的代码，你只需给它提供`Presenter`实例，并把所有其他重要的东西放在你的`Presenter`中。

现在，就像视图一样，呈现者也是通过拥有它的活动的契约接口内的子接口来定义的。

```
interface Presenter extends BasePresenter {

    void performAddition(String numberOne, String numberTwo);

    boolean isEmptyInput(String numOne, String numTwo);
} 
```

现在我需要做的就是让我的活动/片段实现这个接口，并为定义的操作提供实现。

```
class MainPresenter implements ContractMain.Presenter {

    private final ContractMain.View mView;
    private final Context mContext;

    MainPresenter(ContractMain.View view, Context context) {
        mView = view;
        mContext = context;
    }

    @Override
    public void start() {
        // Do your initialization work here
    }

    @Override
    public void performAddition(String numberOne, String numberTwo) {

        if (isEmptyInput(numberOne, numberTwo)) {
            // Display error message if any of the inputs is empty
            mView.displayMessage(mContext.getString(R.string.error_empty_input));
        } else {
            // Compute and pass the result to view for display
            final int firstNumber = Integer.parseInt(numberOne);
            final int secondNumber = Integer.parseInt(numberTwo);

            final int result = firstNumber + secondNumber;
            mView.displayResult(result);
        }
    }

    @Override
    public boolean isEmptyInput(String numOne, String numTwo) {
        return numOne == null || numOne.length() == 0 ||
            numTwo == null || numTwo.length() == 0;
    }
} 
```

* * *

## 胜负

> 所以你可能想知道除了将我们的活动分成 3 部分之外，我们还实现了什么？

使用这种方法的第一个好处是我们实现了一个更加模块化和可扩展的架构。如果您要为这个活动添加**减法**功能，您所要做的就是在`Presenter`接口中定义一个新方法`void performSubtraction(String numberOne, String numberTwo)`并提供它的实现。您的契约是代码本身的文档。

第二个也是最重要的变化是我们的活动现在是完全可测试的。您可以简单地模拟视图，并使用您的`Presenter`测试所有的应用程序逻辑。下面是我们之前实现的活动的几个测试。我使用`Mockito`来模仿依赖性。

```
@RunWith(MockitoJUnitRunner.class)
public class MainTest {

    private static final String EMPTY_INPUT_ERROR_MESSAGE
        = "Please Provide Both Numbers!";

    @Mock
    private ContractMain.View mView;
    @Mock
    private Context mContext;

    private ContractMain.Presenter mPresenter;

    @Before
    public void setUp() throws Exception {
        mPresenter = new MainPresenter(mView, mContext);
    }

    @Test
    public void performAddition_shouldReturnResult() {
        final String numberOne = String.valueOf(2);
        final String numberTwo = String.valueOf(3);

        mPresenter.performAddition(numberOne, numberTwo);

        verify(mView).displayResult(5);
    }

    @Test
    public void performAddition_shouldDisplayErrorMessage() {
        final String numberOne = String.valueOf(2);
        final String numberTwo = null;
        // Mock error string because our mock context
        // wouldn't be able to return it
        when(mContext.getString(R.string.error_empty_input))
            .thenReturn(EMPTY_INPUT_ERROR_MESSAGE);

        mPresenter.performAddition(numberOne, numberTwo);

        verify(mView).displayMessage(EMPTY_INPUT_ERROR_MESSAGE);
    }
} 
```

* * *

## 结论

在这一点上，它可能对您有意义，也可能没有意义，但迟早，您会意识到，如果需要适当的测试并能够长期保持可维护性，您的代码中需要某种程度的模块化。如果不是 MVP，您可能会选择其他几种可用的架构模式之一。对我来说，在 Android 的背景下，MVP 比其他任何东西都更有意义。

* * *

<sub>图像权属于 macoscope。</sub>

* * *

源代码可以在 [GitHub 库](https://github.com/xuhaibahmad/MVP-Tutorial)获得。

如有建议和疑问，只需[联系我](http://linkedin.com/in/xuhaibahmad)。