# 亚马逊 Linux 2でHeadless Chromeを動かす

> 原文：<https://dev.to/ledsun/amazon-linux-2headless-chrome-2b97>

亚马逊 Linux 没有为 GUI 提供软件包。 Headless Chrome 依赖于 libgtk3，在 Amazon Linux 上安装 libgtk3 需要很大的工作量。 现在，亚马逊 Linux 2 为 GUI 提供了一个软件包，可以按照与 Red Hat Enterprise Linux 7.4 几乎相同的步骤运行 Headless Chrome。

让我们在亚马逊 Linux 2 上安装 Headless Chrome。

## EC2 实例制作

在 Amazon Linux 2 上创建 AWS EC2 实例。

### Prior conditions

[AWS 指令](https://aws.amazon.com/jp/cli/)的设定已结束。

*   键对`chromium-on-amazon-linux2`
*   可以 SSH 连接的安全组`sg-77d46f0e`

假设有。

## EC2 实例制作

```
aws ec2 run-instances --image-id ami-2a34b64c --count 1 --instance-type t2.micro --key-name chromium-on-amazon-linux2 --security-group-ids sg-77d46f0e
{
    "Groups": [],
    "Instances": [
        {
            "AmiLaunchIndex": 0,
            "ImageId": "ami-2a34b64c",
            "InstanceId": "i-0f3efae9b9002b82e",
            "InstanceType": "t2.micro",
            "KeyName": "chromium-on-amazon-linux2",
            "LaunchTime": "2018-01-10T06:11:19.000Z",
            "Monitoring": {
                "State": "disabled"
            },
            "Placement": {
                "AvailabilityZone": "ap-northeast-1a",
                "GroupName": "",
                "Tenancy": "default"
            },
            "PrivateDnsName": "ip-172-31-6-102.ap-northeast-1.compute.internal",
            "PrivateIpAddress": "172.31.6.102",
            "ProductCodes": [],
            "PublicDnsName": "",
            "State": {
                "Code": 0,
                "Name": "pending"
            },
            "StateTransitionReason": "",
            "SubnetId": "subnet-6bca3b1c",
            "VpcId": "vpc-4c30c129",
            "Architecture": "x86_64",
            "BlockDeviceMappings": [],
            "ClientToken": "",
            "EbsOptimized": false,
            "Hypervisor": "xen",
            "NetworkInterfaces": [
                {
                    "Attachment": {
                        "AttachTime": "2018-01-10T06:11:19.000Z",
                        "AttachmentId": "eni-attach-a2db2748",
                        "DeleteOnTermination": true,
                        "DeviceIndex": 0,
                        "Status": "attaching"
                    },
                    "Description": "",
                    "Groups": [
                        {
                            "GroupName": "launch-wizard-1",
                            "GroupId": "sg-77d46f0e"
                        }
                    ],
                    "Ipv6Addresses": [],
                    "MacAddress": "06:31:39:4b:07:38",
                    "NetworkInterfaceId": "eni-e98d92d5",
                    "OwnerId": "016753510962",
                    "PrivateDnsName": "ip-172-31-6-102.ap-northeast-1.compute.internal",
                    "PrivateIpAddress": "172.31.6.102",
                    "PrivateIpAddresses": [
                        {
                            "Primary": true,
                            "PrivateDnsName": "ip-172-31-6-102.ap-northeast-1.compute.internal",
                            "PrivateIpAddress": "172.31.6.102"
                        }
                    ],
                    "SourceDestCheck": true,
                    "Status": "in-use",
                    "SubnetId": "subnet-6bca3b1c",
                    "VpcId": "vpc-4c30c129"
                }
            ],
            "RootDeviceName": "/dev/xvda",
            "RootDeviceType": "ebs",
            "SecurityGroups": [
                {
                    "GroupName": "launch-wizard-1",
                    "GroupId": "sg-77d46f0e"
                }
            ],
            "SourceDestCheck": true,
            "StateReason": {
                "Code": "pending",
                "Message": "pending"
            },
            "VirtualizationType": "hvm"
        }
    ],
    "OwnerId": "016753510962",
    "ReservationId": "r-0d34db4dcac25c65f"
} 
```

然后，用 SSH 登录到生成的 EC2 实例。

### SSH 登录

无法在生成 EC2 实例的同时检索公共 IP 地址。
使用`InstanceId`的值获取公共 IP 地址。

```
aws ec2 describe-instances --instance-ids i-0f3efae9b9002b82e
{
    "Reservations": [
        {
            "Groups": [],
            "Instances": [
                {
                    "AmiLaunchIndex": 0,
                    "ImageId": "ami-2a34b64c",
                    "InstanceId": "i-0f3efae9b9002b82e",
                    "InstanceType": "t2.micro",
                    "KeyName": "chromium-on-amazon-linux2",
                    "LaunchTime": "2018-01-10T06:11:19.000Z",
                    "Monitoring": {
                        "State": "disabled"
                    },
                    "Placement": {
                        "AvailabilityZone": "ap-northeast-1a",
                        "GroupName": "",
                        "Tenancy": "default"
                    },
                    "PrivateDnsName": "ip-172-31-6-102.ap-northeast-1.compute.internal",
                    "PrivateIpAddress": "172.31.6.102",
                    "ProductCodes": [],
                    "PublicDnsName": "ec2-13-230-156-244.ap-northeast-1.compute.amazonaws.com",
                    "PublicIpAddress": "13.230.156.244",
                    "State": {
                        "Code": 16,
                        "Name": "running"
                    },
                    "StateTransitionReason": "",
                    "SubnetId": "subnet-6bca3b1c",
                    "VpcId": "vpc-4c30c129",
                    "Architecture": "x86_64",
                    "BlockDeviceMappings": [
                        {
                            "DeviceName": "/dev/xvda",
                            "Ebs": {
                                "AttachTime": "2018-01-10T06:11:20.000Z",
                                "DeleteOnTermination": true,
                                "Status": "attached",
                                "VolumeId": "vol-068a21bf8110a0b90"
                            }
                        }
                    ],
                    "ClientToken": "",
                    "EbsOptimized": false,
                    "EnaSupport": true,
                    "Hypervisor": "xen",
                    "NetworkInterfaces": [
                        {
                            "Association": {
                                "IpOwnerId": "amazon",
                                "PublicDnsName": "ec2-13-230-156-244.ap-northeast-1.compute.amazonaws.com",
                                "PublicIp": "13.230.156.244"
                            },
                            "Attachment": {
                                "AttachTime": "2018-01-10T06:11:19.000Z",
                                "AttachmentId": "eni-attach-a2db2748",
                                "DeleteOnTermination": true,
                                "DeviceIndex": 0,
                                "Status": "attached"
                            },
                            "Description": "",
                            "Groups": [
                                {
                                    "GroupName": "launch-wizard-1",
                                    "GroupId": "sg-77d46f0e"
                                }
                            ],
                            "Ipv6Addresses": [],
                            "MacAddress": "06:31:39:4b:07:38",
                            "NetworkInterfaceId": "eni-e98d92d5",
                            "OwnerId": "016753510962",
                            "PrivateDnsName": "ip-172-31-6-102.ap-northeast-1.compute.internal",
                            "PrivateIpAddress": "172.31.6.102",
                            "PrivateIpAddresses": [
                                {
                                    "Association": {
                                        "IpOwnerId": "amazon",
                                        "PublicDnsName": "ec2-13-230-156-244.ap-northeast-1.compute.amazonaws.com",
                                        "PublicIp": "13.230.156.244"
                                    },
                                    "Primary": true,
                                    "PrivateDnsName": "ip-172-31-6-102.ap-northeast-1.compute.internal",
                                    "PrivateIpAddress": "172.31.6.102"
                                }
                            ],
                            "SourceDestCheck": true,
                            "Status": "in-use",
                            "SubnetId": "subnet-6bca3b1c",
                            "VpcId": "vpc-4c30c129"
                        }
                    ],
                    "RootDeviceName": "/dev/xvda",
                    "RootDeviceType": "ebs",
                    "SecurityGroups": [
                        {
                            "GroupName": "launch-wizard-1",
                            "GroupId": "sg-77d46f0e"
                        }
                    ],
                    "SourceDestCheck": true,
                    "VirtualizationType": "hvm"
                }
            ],
            "OwnerId": "016753510962",
            "ReservationId": "r-0d34db4dcac25c65f"
        }
    ]
} 
```

```
ssh -i chromium-on-amazon-linux2.pem ec2-user@13.230.156.244 
```

## 依存软件包安装

更新 yum 包。

```
sudo yum update -y 
```

安装 Chromium 依赖的软件包。

```
sudo yum install pango.x86_64 libXcomposite.x86_64 libXcursor.x86_64 libXdamage.x86_64 libXext.x86_64 libXi.x86_64 libXtst.x86_64 cups-libs.x86_64 libXScrnSaver.x86_64 libXrandr.x86_64 GConf2.x86_64 alsa-lib.x86_64 atk.x86_64 gtk3.x86_64 ipa-gothic-fonts xorg-x11-fonts-100dpi xorg-x11-fonts-75dpi xorg-x11-utils xorg-x11-fonts-cyrillic xorg-x11-fonts-Type1 xorg-x11-fonts-misc -y 
```

要想在至今为止的 Amazon Linux 上实现这一部分，费了很大的工夫。 在 Amazon Linux 2 上，无需设置即可使用 yum 命令进行安装。
参考:[Installing Google Chrome On CentOS，Amazon Linux，or RHEL](https://intoli.com/blog/installing-google-chrome-on-centos/)

## Chromium 安装

从这里开始做一点作弊。
[Google chrome/puppeteer:headless chrome node API](https://github.com/GoogleChrome/puppeteer)在安装时自动下载 Chromium 二进制文件。
用这个。

### Node.js 安装

```
curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -
sudo yum -y install nodejs 
```

### puppeteer 安装

```
npm init -y
npm i puppeteer 
```

## Action confirmation

创建以下内容的 index.js :

```
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto('https://example.com');

  // Get the "viewport" of the page, as reported by the page.
  const dimensions = await page.evaluate(() => {
    return {
      width: document.documentElement.clientWidth,
      height: document.documentElement.clientHeight,
      deviceScaleFactor: window.devicePixelRatio
    };
  });

  console.log('Dimensions:', dimensions);

  await browser.close();
})(); 
```

```
node . 
```

在中，执行。

```
Dimensions: { width: 800, height: 600, deviceScaleFactor: 1 } 
```

如果显示成功！ ！

## The whole story of

删除用完的 EC2 实例。

```
aws ec2 terminate-instances --instance-ids i-0f3efae9b9002b82e 
```