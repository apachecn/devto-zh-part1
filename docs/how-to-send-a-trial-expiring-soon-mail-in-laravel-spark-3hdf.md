# ★如何在 Laravel Spark 中发送“试用即将到期”的邮件

> 原文：<https://dev.to/freekmurze/how-to-send-a-trial-expiring-soon-mail-in-laravel-spark-3hdf>

我目前正在构建一个名为[哦，亲爱的](https://ohdearapp.com/)的 webapp:一个易于使用且漂亮的网站监视器。它最近进入了测试阶段。在写这篇文章的时候，每个人都可以[注册一个新账户](https://ohdearapp.com/register)。注册后，您将开始为期 10 天的试用期。

Oh Dear 是建立在 Laravel Spark 之上的，这是一个基于 Laravel 的模板，用于启动 SaaS 项目。它提供了将用户组织成团队的逻辑，处理试用期、订阅、支付、发票等等。

Spark 包括租赁用户创建和加入团队的选项。当此团队模式处于活动状态时，订阅与团队而非用户相关。当用户注册并创建他/她的第一个团队时，没有激活的订阅，但是团队处于试用期。

不幸的是，Spark 不会向那些处于试用期的团队所有者发送邮件，试用期即将到期。幸运的是，自己添加很容易。我将在这篇文章中告诉你如何做到这一点。在这个过程中，你会学到一些关于批量发送邮件的好的通用技巧。我们开始吧！

## 决定哪些团队应该被邮寄

首先，我们将向`Team`模型添加一个函数，该函数确定团队是否在即将到期的试验中。该函数将为没有订阅的团队返回`true`,这些团队的试用将在当前时间的两天内结束。

```
public function onSoonExpiringTrial(): bool
{
    if ($this->subscribed()) {
        return false;
    }

    if (! $this->onGenericTrial()) {
        return false;
    }

    return now()->addDays(2)->greaterThan($this->trial_ends_at);
} 
```

## 向团队表添加额外的字段

接下来，我们应该创建一个迁移，将名为`trial_expiring_mail_sent_at`的字段添加到 teams 表中。我们将在下一节解释为什么我们需要这个字段。

```
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class AddTrialExpiringMailSentAtToTeamsTable extends Migration
{
    public function up()
    {
        Schema::table('teams', function (Blueprint $table) {
            $table->timestamp('trial_expiring_mail_sent_at')->nullable();
        });
    }
} 
```

## 邮寄车队的主人

实际的邮件是在一个名为`ohdear:email-teams-with-expiring-trials`的 Artisan 命令中发送的。这个命令被安排给每天运行。下面是该命令的实际代码:

```
namespace App\Console\Commands;

use App\Mail\TrialExpiringSoon;
use App\Team;
use Exception;
use Illuminate\Console\Command;
use Illuminate\Support\Facades\Mail;

class EmailTeamsWithExpiringTrials extends Command
{
    protected $signature = 'ohdear:email-teams-with-expiring-trials';

    protected $description = 'Email teams with expiring trials.';

    protected $mailsSent = 0;

    protected $mailFailures = 0;

    public function handle()
    {
        $this->info('Sending trial expiring soon mails...');

        Team::all()
            ->filter->onSoonExpiringTrial()
            ->each(function (Team $team) {
                $this->sendTrialEndingSoonMail($team);
            });

        $this->info("{$this->mailsSent} trial expiring mails sent!");

        if ($this->mailFailures > 0) {
           $this->error("Failed to send {$this->mailFailures} trial expiring mails!");
        }
    }

    protected function sendTrialEndingSoonMail(Team $team)
    {
        try {
            if ($team->wasAlreadySentTrialExpiringSoonMail()) {
                return;
            }

            $this->comment("Mailing {$team->owner->email} (team {$team->name})");
            Mail::to($team->owner->email)->send(new TrialExpiringSoon($team));

            $this->mailsSent++;

            $team->rememberHasBeenSentTrialExpiringSoonMail();
        } catch (Exception $exception) {
            $this->error("exception when sending mail to team {$team->id}", $exception);
            report($exception);
            $this->mailFailures++;
        }
    }
} 
```

在`sendTrialEndingSoonMail`中，邮件的实际发送被包装在一个 try/catch 块中。因此，如果在发送邮件时出现问题，命令不会中断，而是继续发送给下一个团队。

在发送邮件之前，我们将调用`wasAlreadySentTrialExpiringSoonMail`。如果`trial_expiring_mail_sent_at`不是`null`，该函数将返回`true`。邮件发送后，调用`rememberHasBeenSentTrialExpiringSoonMail`会将团队的`trial_expiring_mail_sent_at`设置为当前日期时间。

所有这些将确保命令是可重启的。如果运行该命令时出现问题，您可以简单地在解决问题后，再次运行该命令。对`trial_expiring_mail_sent_at`的检查将确保没有团队被邮寄两次。

## 即将到期的邮件

您可能已经注意到，在上一节的命令代码中，邮件本身是一个[可邮寄的](https://laravel.com/docs/5.5/mail#generating-mailables)。这里真的没有什么特别的东西，但是为了完整起见，我将包含代码。

```
namespace App\Mail;

use App\Models\Team;
use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class TrialExpiringSoon extends Mailable
{
    use Queueable, SerializesModels;

    /** @var \App\Team */
    public $team;

    public function __construct(Team $team)
    {
        $this->team = $team;
    }

    public function build()
    {
        return $this
            ->subject("Your Oh Dear! trial account will expire soon")
            ->markdown('mail.trialExpiringSoon', [
                'team' => $this->team,
            ]);
    }
} 
```

## 测试命令

Laravel 有一个[邮件假](https://laravel.com/docs/5.5/mocking#mail-fake)来轻松测试邮件是否发送以及如何发送。在本测试中，我们创建了一个团队，该团队正在试用，将于 2018 年 1 月 31 日到期。我们使用 Carbon 的`setTestNow`功能来伪造当前时间。测试本身应该是不言自明的。

```
namespace Tests\Unit\Commands;

use App\Mail\TrialExpiringSoon;
use App\Team;
use App\User;
use Carbon\Carbon;
use Illuminate\Support\Facades\Mail;
use Laravel\Spark\TeamSubscription;
use Tests\TestCase;

class EmailTeamsWithExpiringTrialsTest extends TestCase
{
    /** @var \App\Team */
    protected $team;

    /** @var \App\User */
    protected $user;

    public function setUp()
    {
        parent::setUp();

        $this->user = factory(User::class)->create();

        $this->team = factory(Team::class)->create([
            'trial_ends_at' => Carbon::create(2018, 1, 31),
            'owner_id' => function () {
                return $this->user->id;
            }]);

        $this->team->users()->attach($this->user, ['role' => 'admin']);

        Mail::fake();
    }

    /** @test */
    public function it_can_send_a_mail_concerning_a_trial_expiring_soon()
    {
        $this->setNow(2018, 1, 29);
        $this->artisan('ohdear:email-teams-with-expiring-trials');
        Mail::assertNotSent(TrialExpiringSoon::class);

        $this->setNow(2018, 1, 30);
        $this->artisan('ohdear:email-teams-with-expiring-trials');
        Mail::assertSent(TrialExpiringSoon::class, 1);
        Mail::assertSent(TrialExpiringSoon::class, function (TrialExpiringSoon $mail) {
            return $mail->hasTo($this->user->email);
        });
    }

    /** @test */
    public function it_will_send_the_mail_concerning_a_trial_expiring_soon_only_once()
    {
        $this->setNow(2018, 1, 30);

        $this->artisan('ohdear:email-teams-with-expiring-trials');
        Mail::assertSent(TrialExpiringSoon::class, 1);

        $this->artisan('ohdear:email-teams-with-expiring-trials');
        Mail::assertSent(TrialExpiringSoon::class, 1);
    }

    /** @test */
    public function it_will_not_send_the_mail_concerning_a_trial_expiring_soon_only_if_the_team_has_a_subscription()
    {
        $this->setNow(2018, 1, 30);

        TeamSubscription::create([
            'name' => 'default',
            'team_id' => $this->team->id,
            'stripe_id' => 'my-plan-id',
            'stripe_plan' => 'my-plan',
            'quantity' => 1,
        ]);

        $this->artisan('ohdear:email-teams-with-expiring-trials');

        Mail::assertNotSent(TrialExpiringSoon::class);
    }

    protected function setNow(int $year, int $month, int $day)
    {
        $newNow = Carbon::create($year, $month, $day)->startOfDay();

        Carbon::setTestNow($newNow);

        return $this;
    }
} 
```

## 在关闭

我希望你喜欢这个关于如何给 Laravel Spark 添加一个试用过期邮件的小教程。这篇博文中的代码是在[使用的实际代码，哦，天哪！](https://ohdearapp.com/)。因此，如果你想在你的邮箱里收到真正的试用过期邮件，[创建一个账户](https://ohdearapp.com/register)，然后等待 8 天:-)