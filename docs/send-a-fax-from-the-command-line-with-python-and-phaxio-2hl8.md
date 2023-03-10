# 用 Python 和 Phaxio 从命令行发送传真

> 原文：<https://dev.to/seanlane/send-a-fax-from-the-command-line-with-python-and-phaxio-2hl8>

*2019 年注意:我已经创建了一个小型的兼职项目网站，如果你不想弄乱 Python 脚本，可以让你毫不费力地快速发送传真:[FaxASAP.com](https://faxasap.com)T3】*

如果你想快速、廉价、轻松地发送一份简单的传真，Phaxio 和 Python 是一个不错的组合。下面是我写的一个小脚本，基于皮特·基恩写的这个 [Ruby 脚本，有点过时了。有 Phaxios Python 库，但我遇到了一些问题，这似乎是最简单的解决方案。优点:没有外部依赖。缺点:它使用参数`shell=True`来代表`subprocess.call`，但这应该不是问题，因为你只是在凌晨 2 点使用它来发送快速传真，你不想明天为这个特权支付 UPS/FedEx/任何人太多的钱，对吗？](https://www.petekeen.net/command-line-faxing)

请注意，我没有以任何方式隶属于 Phaxio，它只是碰巧晚了，我需要发送一份传真，他们检查了所有正确的框。我偶然发现了 Phaxio，但对于一个只想每年发送一次快速传真的人来说，它很棒。定价大约是 0.07 美元一页(在我写这篇文章的时候，因为注册，我收到了 1.00 美元的帐户信用)，所以它非常适合我的用例。

## 设置

1.  在[Phaxio:https://www.phaxio.com/](https://www.phaxio.com/)注册一个账户
2.  获取您的 API 密钥: [Phaxio API 凭证](https://console.phaxio.com/api_credentials)
3.  将它们放入下面的脚本中(你也可以在尝试之前使用测试键来确保这是可行的。)
4.  运行脚本，例如，如果我将脚本保存到`fax.py`，我要发送给汤米·图通，我要发送的文件是`letter.pdf`，我会使用下面的:`./fax.py +15558675309 /path/to/letter.pdf`

```
#!/usr/bin/env python3 from subprocess import call
import sys

if len(sys.argv) <= 2:
    print("Usage: send_fax NUMBER FILENAME...")
    exit(-1)

number = sys.argv[1]   
api_key = 'put_api_key_here'
api_secret = 'put_api_secret_here'

command_args = [
  "curl",
  "https://api.phaxio.com/v2/faxes",
  "-u '{}:{}'".format(api_key, api_secret),
  "-F 'to={}'".format(number)
]

for file in sys.argv[2:]:
    command_args.append("-F 'file=@{}'".format(file))

call(' '.join(command_args), shell=True) 
```

脚本可以在这个 GitHub Gist 这里找到:[https://Gist . GitHub . com/Sean lane/67504 BF 39696 de 8 c 0 BC 88 ad 89844 F9 df](https://gist.github.com/seanlane/67504bf39696de8c0bc88ad89844f9df)

随意叉一下，提出改进意见。