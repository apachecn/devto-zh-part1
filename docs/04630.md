# S3 存储桶策略-非本金和λ函数

> 原文：<https://dev.to/neverendingqs/s3-bucket-policy-notprincipal-and-lambda-functions-2ndl>

AWS S3 存储桶策略有一个方便的`NotPrincipal`元素，允许您将操作限制到特定的主体。例如，以下允许您只允许从两个指定的角色中删除:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DenyObjectDeleteWithWhitelist",
            "Effect": "Deny",
            "NotPrincipal": {
                "AWS": [
                    "arn:aws:iam::123456789012:role/SuperRoleOne",
                    "arn:aws:iam::123456789012:role/SuperRoleTwo"
                ]
            },
            "Action": "s3:DeleteObject",
            "Resource": "arn:aws:s3:::mybucket/*"
        }
    ]
} 
```

但是如果您想将它限制为一个假定的角色呢？当我在做一个[无服务器框架](https://serverless.com/framework/)项目时，我想打开一个 S3 桶来允许从一个 Lambda 函数中删除(并且只删除那个 Lambda 函数)。

令人惊讶的是，下面的 S3 木桶政策并没有奏效:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DenyObjectDeleteWithWhitelist",
            "Effect": "Deny",
            "NotPrincipal": {
                "AWS": [
                    "arn:aws:iam::123456789012:role/my-lambda-role"
                ]
            },
            "Action": "s3:DeleteObject",
            "Resource": "arn:aws:s3:::mybucket/*"
        }
    ]
} 
```

通过一点挖掘和故障排除，我意识到 Lambda 函数承担了您提供的角色，并且这个承担的角色也必须添加到 S3 存储桶策略:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DenyObjectDeleteWithWhitelist",
            "Effect": "Deny",
            "NotPrincipal": {
                "AWS": [
                    "arn:aws:iam::123456789012:role/my-lambda-role",
                    "arn:aws:sts::123456789012:assumed-role/my-lambda-role/my-lambda-function"
                ]
            },
            "Action": "s3:DeleteObject",
            "Resource": "arn:aws:s3:::mybucket/*"
        }
    ]
} 
```

这将阻止任何实体从 S3 桶中删除，除了你的 Lambda 函数。

如果这不是您的确切场景，和/或您正在寻找更多细节，请查看 AWS 如何将亚马逊 S3 Bucket 访问限制为特定 IAM 角色。