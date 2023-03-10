# 同一台机器上的以太坊 Mainnet 和 Kovan 节点

> 原文：<https://dev.to/thorstenhirsch/ethereum-mainnet--kovan-nodes-on-same-machine-369l>

# 简介

在我的开发机器上安装以太坊节点后，最初的同步导致它失控，将风扇推到了极限，最终我在我的小 SSD 上有了几千兆字节的区块链数据。听起来很熟悉？那么这本指南是给你的。

您不需要直接在开发机器上运行节点，网络中的任何计算机都可以完成这项工作。所以我选择了我的 NAS，它运行 Ubuntu 17.10，硬盘上有足够的空间。

因为我们是开发人员，所以不仅可以访问 mainnet，还可以访问其中一个测试网，这是一个好主意。因此，我将为 kovan 链设置一个额外的奇偶校验实例。

# 安装

请告诉我，我太笨了，昨天没有找到平价的 PPA 回购协议。我不相信它不存在。无论如何，奇偶校验安装说明告诉你下载一个. deb 文件，所以这里我们用测试版，在撰写本文时是 1.8 . x:

```
wget "https://d1h4xl4cr1h0mo.cloudfront.net/beta-release/x86_64-unknown-linux-gnu/parity" -O parity.deb
sudo dpkg -i parity.deb 
```

Enter fullscreen mode Exit fullscreen mode

curl/bash 脚本也有一个下载选项，但是因为你永远不知道它是做什么的，所以我更喜欢下载并安装一个. deb 文件。

# 以太坊设置

我们将需要一个以太坊帐户在每个实例与合同等互动。我不希望这些帐户(以及它们的密钥)出现在系统目录中，因为它们可能与我的用户相关联(在本文中我将把他称为 *hans* )。我还想保持开放的选择，增加更多的实例，可能会使用其他用户的帐户。所以让我们在我的用户的主目录中创建一些目录:

```
mkdir ~/ethereum
mkdir ~/ethereum/mainnet
mkdir ~/ethereum/kovan 
```

Enter fullscreen mode Exit fullscreen mode

哇，是时候在每个链中创建一个帐户了:

```
parity account new -d ~/ethereum/mainnet
parity account new -d ~/ethereum/kovan 
```

Enter fullscreen mode Exit fullscreen mode

如果你想导入现有账户，你可以按照官方 [wiki](https://github.com/ethereum/go-ethereum/wiki/Managing-your-accounts) 中的说明进行操作。

# 系统设置

让我们从配置文件和日志文件的目录开始。

```
sudo mkdir /etc/ethereum
sudo touch /var/log/parity-mainnet.log
sudo touch /var/log/parity-kovan.log
sudo chown hans /var/log/parity-*.log 
```

Enter fullscreen mode Exit fullscreen mode

现在将我们的链的配置文件放到/etc/systemd 中。你需要 sudo 权限来访问这个文件和本段中的其他 3 个文件。先从**/etc/以太坊/config-mainnet.toml** 说起。

```
[parity]
chain = "mainnet"
base_path = "/home/hans/ethereum/mainnet"
identity = ""

[account]
unlock = ["0x6e6dbb24dd98dd37c41a9964a766e5eb64352d9c"]
password = ["/home/hans/ethereum/mainnet/password"]

[ui]
disable = false
port = 8180
interface = "all"

[network]
port = 30303
reserved_only = false
#reserved_peers = "/etc/parity/mainnetpeers.txt"

[rpc]
disable = false
port = 8545
interface = "all"
apis = ["web3", "eth", "net", "parity", "traces", "rpc", "secretstore"]

[websockets]
disable = false
port = 8546
interface = "all"
apis = ["web3", "eth", "net", "parity", "traces", "rpc", "secretstore"]

[ipc]
disable = false
apis = ["web3", "eth", "net", "parity", "parity_accounts", "personal", "traces", "rpc", "secretstore"]

[dapps]
disable = false

[secretstore]
http_interface = "local"
http_port = 8082
interface = "local"
port = 8083

[ipfs]
enable = false
port = 5001
interface = "all"

[misc]
log_file = "/var/log/parity-mainnet.log" 
```

Enter fullscreen mode Exit fullscreen mode

还有这里的**/etc/以太坊/config-kovan.toml** 。

```
[parity]
chain = "kovan"
base_path = "/home/hans/ethereum/kovan"
identity = ""

[account]
unlock = ["0xaa125f607bb23f41c0c45c27eca820f2b133d1b6"]
password = ["/home/hans/ethereum/kovan/password"]

[ui]
disable = false
port = 18180
interface = "all"

[network]
port = 30304
reserved_only = false
#reserved_peers = "/etc/parity/kovanpeers.txt"

[rpc]
disable = false
port = 18545
interface = "all"
apis = ["web3", "eth", "net", "parity", "traces", "rpc", "secretstore"]

[websockets]
disable = false
port = 18546
interface = "all"
apis = ["web3", "eth", "net", "parity", "traces", "rpc", "secretstore"]

[ipc]
disable = false
apis = ["web3", "eth", "net", "parity", "parity_accounts", "personal", "traces", "rpc", "secretstore"]

[dapps]
disable = false

[secretstore]
http_interface = "local"
http_port = 18082
interface = "local"
port = 18083

[ipfs]
enable = false
port = 15001
interface = "all"

[misc]
log_file = "/var/log/parity-kovan.log" 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们需要为每个实例创建一个 systemd 配置文件，这样系统将在引导时启动奇偶校验(但是不用担心，这些进程将在您的用户帐户下运行)。我们再从 mainnet 开始，文件名是**/etc/systemd/system/parity-mainnet . service**。

```
[Unit]
Description=Parity Mainnet Daemon
After=network.target

[Service]
# run as root, set base_path in config.toml
ExecStart=/usr/bin/parity --config /etc/parity/config-mainnet.toml
# To run as user, comment out above and uncomment below, fill in user and group
# picks up users default config.toml in $HOME/.local/share/io.parity.ethereum/
User=hans
# Group=groupname
# ExecStart=/usr/bin/parity
Restart=on-failure
Nice=15

# Specifies which signal to use when killing a service. Defaults to SIGTERM.
# SIGHUP gives parity time to exit cleanly before SIGKILL (default 90s)
KillSignal=SIGHUP

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

而这里来了**/etc/systemd/system/parity-kovan . service**。

```
[Unit]
Description=Parity Kovan Daemon
After=network.target

[Service]
# run as root, set base_path in config.toml
ExecStart=/usr/bin/parity --config /etc/parity/config-kovan.toml
# To run as user, comment out above and uncomment below, fill in user and group
# picks up users default config.toml in $HOME/.local/share/io.parity.ethereum/
User=hans
# Group=groupname
# ExecStart=/usr/bin/parity
Restart=on-failure
Nice=15

# Specifies which signal to use when killing a service. Defaults to SIGTERM.
# SIGHUP gives parity time to exit cleanly before SIGKILL (default 90s)
KillSignal=SIGHUP

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用下面的命令检查 systemd 是否找到了您的新服务:

```
systemctl list-units parity* 
```

Enter fullscreen mode Exit fullscreen mode

# 试运行

我们准备好试驾了。只需在您的 shell 中用我们刚刚创建的配置文件之一启动一个实例:

```
parity --config /etc/parity/config-mainnet.toml 
```

Enter fullscreen mode Exit fullscreen mode

您应该看到一些信息，几秒钟后同步过程将开始，每隔几秒钟打印一个新的状态行。如果一切正常，按 Ctrl+c 停止测试运行。

# 开始&启用服务

首先，我们启动服务，然后在引导时启用自动启动:

```
sudo systemctl start parity-mainnet
sudo systemctl start parity-kovan
sudo systemctl enable parity-mainnet
sudo systemctl enable parity-kovan 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，您可能会看到您的 shell 响应速度变慢。我试图通过将 nice 级别设置为 15 来降低奇偶校验进程的优先级，从而最小化这种影响。我对 nice 级别的总体建议是:因为你总是希望系统响应迅速，所以永远不要给你的用户进程更高的优先级(例如负数)。15 的好水平是相当低的。它可以低至 20 英镑。然而，将 nice 级别设置为 20 可能没有多大帮助，因为链的初始(扭曲)同步导致的负载会产生大量线程。在这个任务中，我的双核机器上的负载上升到 8 或 9。[这里](https://github.com/paritytech/parity/issues/4940)是对这个问题的更详细的解释，这个问题正在解决中，但是 1.8.x 奇偶校验仍然带有 RocksDB，所以现在我们不得不忍受这个负载和 shell 响应能力的下降。初始同步结束后会变好。

# 端口信息

你可能想知道我选择的港口。以下是我的推理:

*   30303 是默认的以太坊端口，所以 mainnet 将使用它
*   30304 没有被任何其他模块使用，所以 kovan 将使用它

rpc 和 http 端口(8xxx)不能遵循相同的逻辑，因为一些默认值是连续的。所以这里我们将使用...

*   mainnet 的 8xxx 的 8180，rpc 的 8545，websockets 的 8546，...)
*   kovan 的 18xxx 的 18180，rpc 的 18545，websockets 的 18546，...)

**更新 2018-01-20**
systemd:wanted by = multi-user . target 而不是 default.target