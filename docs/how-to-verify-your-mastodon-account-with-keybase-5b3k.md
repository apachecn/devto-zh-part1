# 如何用 Keybase 验证你的乳齿象账户

> 原文：<https://dev.to/ryanmaynard/how-to-verify-your-mastodon-account-with-keybase-5b3k>

[![mastodon and keybase logos](img/f0b99ae1139c8abb9758eb884bccdd36.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ss0srk0r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ryanmaynard.img/mastodon/mastodonkeybase.png)

*本文最初发布于 2017 年 4 月 8 日*我的[个人网站](https://developer.mozilla.org/en-US/docs/Tools)

在写这篇文章的时候，分散式网络[乳齿象](https://github.com/tootsuite/mastodon)最近从相对的默默无闻中迅速走红。任何人都可以在<上创建自己的乳齿象实例！——摘录——>自己的领域，有自己的规则。所有相互连接的实例的集合被称为 Fe diversity。这里有很多乳齿象的例子。

#### 身份验证

与任何较新的社交网络一样，存在身份验证的问题。为了防止滥用，您是否需要在所有实例上创建您的首选用户名？你如何证明自己？

Keybase 还没有一个内置的选项来验证一个人的乳齿象帐户。Keybase 创始人克里斯·科恩[在推特上说他是否应该等待实施它](https://twitter.com/malgorithms/status/850075699130052608)，以防未来几周乳齿象发生重大结构性变化。这似乎是一个合理的担忧。

[![Chris Coyne tweet](img/5627298bf704617436da0121a71a05c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8xt2BpG8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ryanmaynard.img/mastodon/coyne-tweet.png)

暂时，你可以简单地用 Keybase 签署一个消息来验证你自己。

##### 创建您的密钥库凭证

确保您已经安装了 [keybase cli](https://keybase.io/docs/command_line) 。

```
keybase sign -m "Hi! I'm @you@mastodonserver.tld on Mastodon." 
```

Enter fullscreen mode Exit fullscreen mode

比如这里是我的:

```
keybase sign -m "Hi! I'm @ryanmaynard@mastodon.cloud on mastodon 
```

Enter fullscreen mode Exit fullscreen mode

拿着结果，在你的乳齿象账号[上发个帖子像这样](https://mastodon.cloud/@ryanmaynard/211915)。

[![Keybase Sign](img/c3d135b720b495b6523d069d5fae8bde.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ILhZpYjb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ryanmaynard.img/mastodon/keybase-sign.png)

##### 验证一个密钥库凭证

将张贴的校样的内容放入文件中。

```
keybase verify < keybase_proof_filename 
```

Enter fullscreen mode Exit fullscreen mode

结果应该是这样的:

[![Keybase Verification](img/80a9aca8253d7d77232e261ec56f9f23.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NvrbpKLy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ryanmaynard.img/mastodon/keybase-verify.png)

##### 替代密钥库凭证

另一个你可以使用的方法是，使用 [Keybase 文件系统，KBFS](https://keybase.io/docs/kbfs) 发布一个公共证明

可以贴一个`I am <username> on Mastodon`的变种

```
cd /keybase/public/username/
touch mastodonproof.md 
```

Enter fullscreen mode Exit fullscreen mode

将在
可见

```
https://keybase.pub/username/mastodonproof.md 
```

Enter fullscreen mode Exit fullscreen mode

您也可以将`index.html`或`index.md`添加到您的`/keybase/username/`文件夹中，并且可以在`username.keybase.pub`访问它

比如[这里是我的](https://ryanmaynard.keybase.pub)。

[![Keybase Pub](img/ce15c5fb3906c53fc007663bed17ebbc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7F6wMT4h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ryanmaynard.img/mastodon/keybase-pub.png)

谢谢 [@todb](https://mastodon.xyz/@todb) 给[这个想法](https://mastodon.xyz/@todb/206070)，也谢谢 [@seabre](https://mastodon.xyz/@seabre) 给我指出来。

#### 找到你的朋友

如果你想在一开始就“引进”一些你的 Twitter 粉丝，Heroku 上有一个非官方的[应用，乳齿象桥](https://mastodon-bridge.herokuapp.com/)。不言而喻:使用风险自担。

#### 追随机器人

有人组装了一个 [followbot](https://github.com/gled-rs/mastodon-autofollow) ，如果你喜欢的话。

#### 支持

如果你喜欢乳齿象，那就毫不犹豫地扔几块钱给开发商加格龙吧。这是他的第一页。如果你想以这种方式提供帮助的话，还有很多 T2 问题需要解决。