# 8 行代码的短信管理

> 原文：<https://dev.to/gonedark/sms-admin-in-8-lines-of-code>

我对自己制造的产品充满热情。但是我试着在它们之间平衡我的时间。对于 SaaS 产品来说，这可能很困难。尤其是像 [Laravel Shift](https://laravelshift.com) 这样的付费 SaaS 产品。你不想错过收入。

虽然 Shift 是全自动服务，但有时需要人工干预。例如，如果客户使用另一种支付方式或 Git，就会出现相关问题。在这种情况下，我必须手动换档。这包括:

1.  连接到服务器
2.  查找班次
3.  创建工作
4.  将作业推入队列

我以支持为荣。除非我在睡觉，否则我会努力在一个小时内解决所有问题。尽管上面的步骤不到一分钟，我宁愿把时间花在别的事情上。你的产品应该是有趣的，而不是负担。

我知道你们开发人员在想——为什么不建立一个 web 管理呢？嗯，[YAGNI](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it)——这就是原因。

考虑开发(和维护)这样一个网站管理员的成本。我需要建立登录，列表/详细信息页面，搜索和行动本身。同样从体验的角度来看，这与之前的步骤数大致相同。当然，用任何框架都很容易构建。但是我不需要它。

最终，我*所需要的*就是一个**快捷方式**来运行一个移动中的**。回顾近两年的支持，我经常有现成的班次号码。创建作业并将其添加到队列最多只有两行代码。所以步骤不是痛点。**

 **痛点是*连接服务器*。除非我想随身携带笔记本电脑，否则我无法连接到服务器来运行班次。(实际上，我在高峰期会带着笔记本电脑。)

我一直带着什么？我的手机。我已经在看手机里的支持邮件了。当我需要手动运行一个班次来回复或发送一条短信时，这不是很好吗？

## 显示代码

尽管背景故事强调了找到正确解决方案的重要性，但我知道你们开发人员想看代码。

作为短信平台我用的是 [Nexmo](https://www.nexmo.com/) 。像 Nexmo 这样的服务使得处理短信通信变得非常容易。我租了一个电话号码，设置了一个网络挂钩。就是这样。

现在，只要那个电话号码收到短信，Nexmo 就会向这个网络端点发送数据。该数据包含元信息，如唯一的消息 ID 和时间戳，以及发送电话号码和消息正文。

实际上，我需要开发的只是一个 web 端点。换挡是用[拉弗尔](//https;//laravelshift.com)造的。这意味着我可以通过运行`php artisan make:controller --resource Api/SmsController`快速生成一个 API 资源。

我去掉了额外的*动作*，因为我只想要`store`端点。由于 Nexmo 发送了一个`POST`请求，这是一个自然的选择。从 *RESTful* 的角度来看，这也是合适的，因为这个端点*创建*一个新的作业并将其放入队列。

```
class SmsController extends Controller
{
    public function store(RerunRequest $request)
    {
        $order = Order::findOrFail($request->get('text'));

        Queue::push(new PerformShift($order));

        return null;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是对应的路线:

```
Route::apiResource('sms', 'Api\SmsController', ['only' => ['store']]); 
```

Enter fullscreen mode Exit fullscreen mode

熟悉 Laravel 的人可能注意到了`RerunRequest`传给了`store()`。这是一个[表单请求](https://laravel.com/docs/5.5/validation#creating-form-requests)，它在 Laravel 中处理请求验证。在这种情况下，它执行一些非常基本的检查，以确保消息是预期的。

```
class RerunRequest extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return true;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            'msisdn' => 'required|integer|in:15025555555',
            'text' => 'required|integer',
        ];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一句，我很惊讶在 Laravel 中有这么多[可用的验证规则，却没有一个`equals`或`is`规则。我不得不使用`in`来验证发送电话号码是我的电话号码。](https://laravel.com/docs/5.5/validation#available-validation-rules)

就是这样。几个生成的类和 8 行自定义代码。它满足了我所有的要求。我可以在任何有手机信号的地方保持快速的支持。更好的是，它节省了我的时间。过去需要一分钟的事情现在只需要几秒钟。我已经用过几次了。

## 为什么是 Nexmo？

接收短信是免费的。这意味着我支付的只是一个电话号码。这一成本不到 10 美元/年——大约是一个班次的成本。因此，虽然有其他平台，但 Nexmo 完全符合要求。

***有想造的东西吗？**让我们配对吧！我提供[结对编程会议](https://coaching.pureconcepts.net)，在那里我们可以一起解决你的项目。这是编写代码和提升技能的好方法。***