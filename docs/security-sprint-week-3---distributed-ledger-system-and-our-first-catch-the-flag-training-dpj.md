# 安全冲刺:第 3 周-分布式分类帐系统和我们的第一次抓旗培训

> 原文：<https://dev.to/terceranexus6/security-sprint-week-3---distributed-ledger-system-and-our-first-catch-the-flag-training-dpj>

关于安全，星期五是如此紧张的一天。首先，我开始调查一个关于**分布式账本**(如[比特币](https://dev.to/terceranexus6/bitcoins-d00)系统)应用于其他事物的课堂作业，如能源行业、通信和类似的。分布式分类帐是由大型网络中的每个参与者(或节点)独立持有和更新的数据库。中央授权的缺乏使得各个节点处理每笔交易，并再次更新分类帐(对于每个节点)。

[![](img/9df1796b654d260a15d53a85f1d63c5b.png)T2】](https://i.giphy.com/media/HMHG96CgEbYXu/giphy.gif)

我发现这非常有趣，并开始调查关于*以太坊*的情况，因为它的[文档](https://www.ethereum.org/)提供了一个编码指南，可以让[创建自己的加密货币](https://www.ethereum.org/token)或在区块链上建立[民主，我发现这真的很酷，我非常兴奋，并决定尝试所有这些。特别是，民主系统可以帮助我开发一些有趣的东西，在课堂上使用，并展示我自己对分布式账本系统(gasp)的想法。例如，这是以太坊合约的基本模板:](https://www.ethereum.org/dao) 

```
contract MyToken {
    /* This creates an array with all balances */
    mapping (address => uint256) public balanceOf;

    /* Initializes contract with initial supply tokens to the creator of the contract */
    function MyToken(
        uint256 initialSupply
        ) {
        balanceOf[msg.sender] = initialSupply;              // Give the creator all initial tokens
    }

    /* Send coins */
    function transfer(address _to, uint256 _value) {
        require(balanceOf[msg.sender] >= _value);           // Check if the sender has enough
        require(balanceOf[_to] + _value >= balanceOf[_to]); // Check for overflows
        balanceOf[msg.sender] -= _value;                    // Subtract from the sender
        balanceOf[_to] += _value;                           // Add the same to the recipient
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

无论如何，我有一个月左右的时间来开发我的项目，我希望我能在明年之前分享一些有趣的东西。除了分布式分类账调查之外，我还在本周会见了网络和取证研究小组，我们开始了解我们可以在不同的安全难题中使用的工具以及捕捉标志事件。我们(和其他黑客组织一起)解决了几个有趣的例子。

[![](img/3884e4335d227b1152b45676ebbd07ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0I29GOiS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/iVHfwLc.gif)

我们开始解决[这个你可能也有兴趣解决的谜题](http://forensicscontest.com/puzzles)。在[第一个](http://forensicscontest.com/2009/09/25/puzzle-1-anns-bad-aim)谜题“安的坏目标”中，我们是安全专家，试图抓住一家公司的*间谍*。在这种情况下，我们使用一个. pcap 文件获得分析后的网络，我们使用 [Wireshark](https://www.wireshark.org/) 打开该文件(我个人也使用 [tcpdump](https://dev.to/terceranexus6/security-sprint-week-2---choosing-a-nice-point-to-sniff-and-using-tcpdump-for-packet-analysis--e9) 将其保存在一个日志中，并在我自己的终端上阅读，我觉得这很舒服)，这非常有趣，即使我们不得不在那天中途停下来。如果你足够勇敢，甚至有 Perl 选项来解决这个问题。例如，我们的一个成员发现了一个“*这是秘方……我刚刚从文件服务器下载了它。只需复制到一个拇指驱动器，你就可以开始了> :-)* ”消息在 pcap 文件中，使用一些过滤器，我们了解了文件的神奇数字。[幻数](https://en.wikipedia.org/wiki/List_of_file_signatures)是十六进制签名，用于识别或验证文件内容。这对于网络系统监控非常有用。对于进一步的十六进制分析，我建议使用 **GHEX** (在 linux 中)。为了更好地管理我们的工作，我们将使用 **docker** 来部署我们的公共资源。

我们的主要选择是使用[这个框架](https://github.com/CTFd/CTFd)，docker 使用`docker-compose up -d`，这已经提供了一个简单的部署选项。在 docker [这里](https://medium.com/@rkkautsar/how-docker-helps-me-manage-a-capture-the-flag-competition-7af303c1de2)已经有一个非常完整的关于使用这个框架的指南，我个人也会尝试没有框架的自制 docker 部署(因为我喜欢控制我自己的东西)，但是现在这是一个很好的选择。

总而言之，接下来的几周我有令人兴奋的事情要做，我迫不及待地想分享一切的发展。你们有没有试过解 CTF 的益智游戏？你有没有尝试过加密货币相关开发的实验？有什么建议吗？