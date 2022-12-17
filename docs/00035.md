# 如何使用亚马逊 S3 和 Laravel 上传图片

> 原文：<https://dev.to/whoisryosuke/how-to-upload-images-using-amazon-s3-and-laravel-2f0f>

需要使用 Laravel 将图像上传到您的 S3 实例？幸运的是，Laravel 让*建立连接*变得非常简单，**开始上传到云端**。但是它对亚马逊有什么要求呢？系上你的 polos，让我们获得 *AWS 认证。*👌

我假设你有非常基本的 AWS 知识，在亚马逊有一个活跃的帐户。如果你还没有，我推荐[注册他们的 1 年免费试用](https://aws.amazon.com/free/)，这是一个疯狂的交易。

## AWS 方式

我们将创建一个 S3 存储桶，添加适当的策略，并将一个 IAM 用户与其相关联。我们将使用 Laravel 通过 IAM 用户的凭证连接到 S3 桶(这就是为什么他们需要适当的权限即策略)。

## 斗技挑战

第一步是[以 root 用户身份登录您的 AWS 帐户](https://signin.aws.amazon.com/)。然后自己导航到网站的[S3 部分](https://s3.console.aws.amazon.com/s3/home)。

1.  单击蓝色的“+创建存储桶”按钮。
2.  为您的 S3 存储桶输入一个唯一的 slug，为服务器选择一个区域，然后单击 next。
3.  保留这里的默认设置，确保“管理公共权限”选择:“不授予公共读取权限。”。点击下一步！
4.  单击查看。

恭喜，你有一个新的 S3 桶。现在，让我们将一个 IAM 用户分配给这个存储桶。

## IAM 时间

我们必须创建一个新用户(或者您可以使用现有用户)，并添加

[一个桶策略，允许您的 IAM 用户上传到 S3](https://laracasts.com/discuss/channels/laravel/amazon-s3-upload-acl/replies/355774)

1.  创建一个新用户并保存他们的访问 id 和密码。
2.  对下面的用户应用新策略，将“my-bucket”替换为之前的 bucket slug:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:ListAllMyBuckets",
                "s3:GetBucketLocation"
            ],
            "Resource": [
                "arn:aws:s3:::my-bucket"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl", 
                "s3:GetObject",
                "s3:DeleteObject"
            ],
            "Resource": [
                "arn:aws:s3:::my-bucket/*"
            ]
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您在查看文件时仍然得到“拒绝访问”的提示，请尝试将以下代码片段添加到您的策略中，这些代码片段来自[亚马逊的 FAQ](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html#example-bucket-policies-use-case-2) :

```
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Sid":"AddPerm",
      "Effect":"Allow",
      "Action":["s3:GetObject"],
      "Resource":["arn:aws:s3:::examplebucket/*"]
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

## 与 Laravel 连接

既然我们已经能够创建一个 bucket 并为其分配一个用户，那么让我们向 Laravel 添加凭证，以便它能够连接！

1.  打开你的。ENV 文件并添加以下变量(如果它们不存在)。确保添加您的凭证、存储桶名称和[存储桶的区域代码](https://docs.aws.amazon.com/general/latest/gr/rande.html):

```
AWS_ACCESS_KEY_ID=your_key_here
AWS_SECRET_ACCESS_KEY=your_secret_here
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=my-bucket 
```

Enter fullscreen mode Exit fullscreen mode

1.  进入你的文件系统配置文件(`/laravel-project/config/filesystems.php`)，确保你使用的是环境变量。`disks`数组的`S3`数组应该是这样的:

```
's3' => [
        'driver' => 's3',
        'key' => env('AWS_ACCESS_KEY_ID'),
        'secret' => env('AWS_SECRET_ACCESS_KEY'),
        'region' => env('AWS_DEFAULT_REGION'),
        'bucket' => env('AWS_BUCKET'),
    ], 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！你把拉弗尔和你的 S3 桶联系起来了。简单对吗？

## 伸缩我的 S3

让我们测试一下是否一切正常！你可以查看官方的 Laravel 文档来获得更多关于使用文件系统的信息，但是你可以在一个控制器中使用下面的代码片段来运行一个快速测试:

```
$my_file = 'file.txt';
$handle = fopen($my_file, 'w') or die('Cannot open file: '.$my_file);
$data = 'Test data to see if this works!';
fwrite($handle, $data);

$storagePath = Storage::disk('s3')->put("uploads", $my_file, 'public'); 
```

Enter fullscreen mode Exit fullscreen mode

我们创建一个文本文件，将文本插入其中，并将该文本文件上传到 S3 存储。在`put`方法中还有第三个参数被定义为 **public** ，这使得文件可以公开访问。您可以删除该文件，使其成为私有文件。

S3 上的文件路径存储在`$storagePath`变量中，因此您可以将其存储在 DB 中，并通过使用`Storage::disk('s3')->get($storagePath)`来查找帖子。

## 一切尽在天斗

Laravel 使得使用像 S3 这样的存储系统变得毫不费力，但是像 AWS 这样的服务通过复杂的策略使过程变得复杂。我希望这能为您节省 30 分钟或更多的时间，在糟糕的文档中编写和研究 IAM 策略。

奥斯卡保持常规

* * *

**继续阅读:**

*   [AWS 地区代码](https://docs.aws.amazon.com/general/latest/gr/rande.html)
*   [基本 PHP 文件处理](https://davidwalsh.name/basic-php-file-handling-create-open-read-write-append-close-delete)
*   [管理员访问的存储桶策略示例](https://laracasts.com/discuss/channels/laravel/amazon-s3-upload-acl/replies/355774)
*   [只读匿名访问的亚马逊示例存储桶策略](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html#example-bucket-policies-use-case-2)