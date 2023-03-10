# 实用 Golang:用 Hashicorp Serf 构建一个简单的分布式单值数据库

> 原文：<https://dev.to/cube2222/practical-golang-building-a-simple-distributed-one-value-database-with-hashicorp-serf>

*这是我在[jacobmartins.com](https://jacobmartins.com)T3 的博客中的交叉帖子*

## 简介

随着*分布式应用*的出现，我们看到新的存储解决方案不断涌现。
他们包括但不限于，[卡珊德拉](https://cassandra.apache.org/)，[雷迪斯](https://redis.io/)，[蟑螂 DB](https://www.cockroachlabs.com/) ，[领事](https://www.consul.io/)或 [RethinkDB](https://www.rethinkdb.com/) 。你们中的大多数人可能会使用其中的一种或多种。

它们看起来是非常复杂的系统，因为它们确实是。这个不能否认。但是编写一个简单的单值数据库是很容易的，其特点是高可用性和 T2。
你可能不会在生产中使用任何类似的东西，但这对你来说应该是一次富有成效的学习经历。
如果你感兴趣，请继续读下去！

## 依赖关系

你需要

```
go get github.com/hashicorp/serf/serf 
```

Enter fullscreen mode Exit fullscreen mode

作为键依赖项。

为了方便起见，我们也将使用它们:

```
"github.com/gorilla/mux"
"github.com/pkg/errors"
"golang.org/x/sync/errgroup" 
```

Enter fullscreen mode Exit fullscreen mode

## 小概览

我们将建造什么？我们将构建一个单值*集群*数据库。这意味着，我们应用程序的许多实例将能够协同工作。
您将能够使用 REST 接口设置或获取值。该值将很快使用 Gossip 协议在集群中传播。
这意味着，每个节点在设定的时间间隔内将变量的当前状态告知集群的一部分。但是因为后来每个都告诉了集群的一部分关于状态的信息，所以整个集群很快就被通知了。

它将使用 Serf 实现简单的集群成员资格，这使用了 SWIM under the hood。SWIM 是一种更高级的类似八卦的算法，你可以在这里阅读关于[的内容。](https://www.cs.cornell.edu/~asdas/research/dsn02-swim.pdf)

现在让我们开始实现...

## 入门

首先，我们当然必须输入所有的进口商品

```
import (
    "context"
    "fmt"
    "log"
    "math/rand"
    "net/http"
    "os"
    "strconv"
    "sync"
    "time"
    "github.com/gorilla/mux"
    "github.com/hashicorp/serf/serf"
    "github.com/pkg/errors"
    "golang.org/x/sync/errgroup"
) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，是时候编写一个简单的线程安全的单值存储了。
一个重要的事情是，数据库还将保存*代*的变量。这样，当一个实例收到关于新值的通知时，它可以检查传入的通知实际上是否具有更高的生成计数。只有这样，它才会改变当前的本地值。
所以我们的数据库结构将确切地保存这个:数字、世代和互斥体。

```
type oneAndOnlyNumber struct {
    num        int
    generation int
    numMutex   sync.RWMutex
}

func InitTheNumber(val int) *oneAndOnlyNumber {
    return &oneAndOnlyNumber{
        num: val,
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要一种设置和获取值的方法。设置该值还会增加代计数，因此当我们通知该集群的其余部分时，我们将覆盖它们的值和代计数。

```
func (n *oneAndOnlyNumber) setValue(newVal int) {
    n.numMutex.Lock()
    defer n.numMutex.Unlock()
    n.num = newVal
    n.generation = n.generation + 1
}

func (n *oneAndOnlyNumber) getValue() (int, int) {
    n.numMutex.RLock()
    defer n.numMutex.RUnlock()
    return n.num, n.generation
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要一种方法来通知数据库其他地方发生的变化，如果它们有更高的代计数。为此，我们有一个小的 notify 方法，如果有任何改变，它将返回 true:

```
func (n *oneAndOnlyNumber) notifyValue(curVal int, curGeneration int) bool {
    if curGeneration > n.generation {
        n.numMutex.Lock()
        defer n.numMutex.Unlock()
        n.generation = curGeneration
        n.num = curVal
        return true
    }
    return false
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还将创建一个 const，描述每次我们将通知多少个节点新值。

```
const MembersToNotify = 2 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们来看看应用程序的实际功能。首先，我们必须使用两个变量启动一个 serf 实例。我们的实例在网络中的地址和-可选-要加入的集群的地址。

```
func main() {
    cluster, err := setupCluster(
        os.Getenv("ADVERTISE_ADDR"),
        os.Getenv("CLUSTER_ADDR"))
    if err != nil {
        log.Fatal(err)
    }
    defer cluster.Leave() 
```

Enter fullscreen mode Exit fullscreen mode

你可能会问，setupCluster 函数是如何工作的？就是这里:

```
func setupCluster(advertiseAddr string, clusterAddr string) (*serf.Serf, error) {
    conf := serf.DefaultConfig()
    conf.Init()
    conf.MemberlistConfig.AdvertiseAddr = advertiseAddr

    cluster, err := serf.Create(conf)
    if err != nil {
        return nil, errors.Wrap(err, "Couldn't create cluster")
    }

    _, err = cluster.Join([]string{clusterAddr}, true)
    if err != nil {
        log.Printf("Couldn't join cluster, starting own: %v\n", err)
    }

    return cluster, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

如我们所见，我们正在创建群集，只是更改了广告地址。

如果创建失败，我们当然会返回错误。
如果加入失败，这意味着我们要么没有获得集群地址，
要么集群不存在(忽略网络故障)，这意味着我们可以安全地忽略它，只记录它。

继续，我们初始化数据库和 REST API:
(我真的是随机选择的数字...真的！)

```
 theOneAndOnlyNumber := InitTheNumber(42)
    launchHTTPAPI(theOneAndOnlyNumber) 
```

Enter fullscreen mode Exit fullscreen mode

这就是 API 创建的样子:

```
func launchHTTPAPI(db *oneAndOnlyNumber) {
    go func() {
        m := mux.NewRouter() 
```

Enter fullscreen mode Exit fullscreen mode

我们首先异步启动我们的服务器。然后我们声明我们的 getter:

```
 m.HandleFunc("/get", func(w http.ResponseWriter, r *http.Request) {
            val, _ := db.getValue()
            fmt.Fprintf(w, "%v", val)
        }) 
```

Enter fullscreen mode Exit fullscreen mode

我们的二传手:

```
m.HandleFunc("/set/{newVal}", func(w http.ResponseWriter, r *http.Request) {
            vars := mux.Vars(r)
            newVal, err := strconv.Atoi(vars["newVal"])
            if err != nil {
                w.WriteHeader(http.StatusBadRequest)
                fmt.Fprintf(w, "%v", err)
                return
            }

            db.setValue(newVal)

            fmt.Fprintf(w, "%v", newVal)
        }) 
```

Enter fullscreen mode Exit fullscreen mode

最后是 API 端点，它允许其他*节点*通知这个实例的变化:

```
 m.HandleFunc("/notify/{curVal}/{curGeneration}", func(w http.ResponseWriter, r *http.Request) {
            vars := mux.Vars(r)
            curVal, err := strconv.Atoi(vars["curVal"])
            if err != nil {
                w.WriteHeader(http.StatusBadRequest)
                fmt.Fprintf(w, "%v", err)
                return
            }
            curGeneration, err := strconv.Atoi(vars["curGeneration"])
            if err != nil {
                w.WriteHeader(http.StatusBadRequest)
                fmt.Fprintf(w, "%v", err)
                return
            }

            if changed := db.notifyValue(curVal, curGeneration); changed {
                log.Printf(
                    "NewVal: %v Gen: %v Notifier: %v",
                    curVal,
                    curGeneration,
                    r.URL.Query().Get("notifier"))
            }
            w.WriteHeader(http.StatusOK)
        })
        log.Fatal(http.ListenAndServe(":8080", m))
    }()
} 
```

Enter fullscreen mode Exit fullscreen mode

也是在这里，我们启动我们的服务器，并在我们的集群的其他*成员*通知我们新值时打印一些调试信息。

太好了，我们现在有了一种与我们的服务对话的方式。是时候让它传播所有的信息了。我们也会定期打印调试信息。

首先，让我们初始化我们的*上下文*(这在主函数中总是一个好主意)。
我们还将为它输入一个值，我们主机的名称，只是为了调试日志。
这是一件很好的事情，因为它对我们的程序的运行并不是至关重要的，
而且上下文无论如何都会被传递下去。

```
 ctx := context.Background()
    if name, err := os.Hostname(); err == nil {
        ctx = context.WithValue(ctx, "name", name)
    } 
```

Enter fullscreen mode Exit fullscreen mode

完成这些之后，我们就可以设置我们的主循环了，包括我们向对等体发送状态更新和打印调试信息的时间间隔。

```
 debugDataPrinterTicker := time.Tick(time.Second * 5)
    numberBroadcastTicker := time.Tick(time.Second * 2)
    for {
        select {
        case <-numberBroadcastTicker:
        // Notification code goes here...
        case <-debugDataPrinterTicker:
            log.Printf("Members: %v\n", cluster.Members())

            curVal, curGen := theOneAndOnlyNumber.getValue()
            log.Printf("State: Val: %v Gen: %v\n", curVal, curGen)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

好了，好像就是这样。

开玩笑的。是时候用通知代码结束我们的服务了。
我们现在将获得集群中的**个其他**成员的列表，设置一个超时，并异步通知这些其他成员中的一部分。

```
 case <-numberBroadcastTicker:
            members := getOtherMembers(cluster)

            ctx, _ := context.WithTimeout(ctx, time.Second*2)
            go notifyOthers(ctx, members, theOneAndOnlyNumber) 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们看看 *getOtherMembers* 函数。它实际上只是一个扫描成员列表的函数，删除我们自己和其他此刻不存在的节点。

```
func getOtherMembers(cluster *serf.Serf) []serf.Member {
    members := cluster.Members()
    for i := 0; i < len(members); {
        if members[i].Name == cluster.LocalMember().Name || members[i].Status != serf.StatusAlive {
            if i < len(members)-1 {
                members = append(members[:i], members[i + 1:]...)
            } else {
                members = members[:i]
            }
        } else {
            i++
        }
    }
    return members
} 
```

Enter fullscreen mode Exit fullscreen mode

我想这没什么大不了的。它使用切片来删除不符合我们谓词的成员。

最后我们用来通知别人的函数:

```
func notifyOthers(ctx context.Context, otherMembers []serf.Member, db *oneAndOnlyNumber) {
    g, ctx := errgroup.WithContext(ctx)

    if len(otherMembers) <= MembersToNotify {
        for _, member := range otherMembers {
            curMember := member
            g.Go(func() error {
                return notifyMember(ctx, curMember.Addr.String(), db)
            })
        }
    } else {
        randIndex := rand.Int() % len(otherMembers)
        for i := 0; i < MembersToNotify; i++ {
            g.Go(func() error {
                return notifyMember(
                    ctx,
                    otherMembers[(randIndex + i) % len(otherMembers)].Addr.String(),
                    db)
            })
        }
    }

    err := g.Wait()
    if err != nil {
        log.Printf("Error when notifying other members: %v", err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果只有两个成员，那么它向他们发送通知，否则它在 members 数组中选择一个随机索引，并从那里选择后续成员。errgroup 是如何工作的？这是一个很棒的图书馆，Brian Ketelsen 写了一篇很棒的文章《T2》。它基本上是一个等待组，收集错误并在错误发生时中止。

现在来完成我们的代码， *notifyMember* 函数:

```
func notifyMember(ctx context.Context, addr string, db *oneAndOnlyNumber) error {
    val, gen := db.getValue()
    req, err := http.NewRequest("POST", fmt.Sprintf("http://%v:8080/notify/%v/%v?notifier=%v", addr, val, gen, ctx.Value("name")), nil)
    if err != nil {
        return errors.Wrap(err, "Couldn't create request")
    }
    req = req.WithContext(ctx)

    _, err = http.DefaultClient.Do(req)
    if err != nil {
        return errors.Wrap(err, "Couldn't make request")
    }
    return nil
} 
```

Enter fullscreen mode Exit fullscreen mode

我们用公式*{ node address }:8080/notify/{ curVal }/{ curGen }创建一条路径？notifier={selfHostName}*
我们将上下文添加到请求中，因此我们获得了超时功能，并最终发出请求。

这就是代码的全部内容。

## 测试我们的数据库

我们将使用 docker 测试我们的数据库。放入项目目录的必要 docker 文件如下所示:

```
FROM alpine
WORKDIR /app
COPY distApp /app/
ENTRYPOINT ["./distApp"] 
```

Enter fullscreen mode Exit fullscreen mode

现在，首先构建您的应用程序(如果您不在 linux 上，您必须设置环境变量 GOOS=linux 和 GOARCH=amd64)
稍后构建 docker 映像:

```
docker build -t distapp . 
```

Enter fullscreen mode Exit fullscreen mode

我们终于可以发射了。为了提供必要的环境变量，我们需要知道容器将获得什么 ip 地址。
第一次运行:

```
docker network inspect bridge 
```

Enter fullscreen mode Exit fullscreen mode

网桥是默认的网络容器被分配到。您应该会得到这样的结果:

```
[
    {
        "Name": "bridge",
        "Id": "b56a19697ed9d30488f189d5517fd79f04a4df70c8bbc07d8f3c49a491f10433",
        "Created": "2017-01-29T10:48:05.1592086Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1" <-- this is what we need
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Containers": {},
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
] 
```

Enter fullscreen mode Exit fullscreen mode

对我们来说重要的是入口。在这种情况下，我们的容器将使用来自 172.17.0.2 的 IP 地址

所以现在我们可以启动几个容器:

```
docker run -e ADVERTISE_ADDR=172.17.0.2 -p 8080:8080 distapp
docker run -e ADVERTISE_ADDR=172.17.0.3 -e CLUSTER_ADDR=172.17.0.2 -p 8081:8080 distapp
docker run -e ADVERTISE_ADDR=172.17.0.4 -e CLUSTER_ADDR=172.17.0.3 -p 8082:8080 distapp
docker run -e ADVERTISE_ADDR=172.17.0.5 -e CLUSTER_ADDR=172.17.0.4 -p 8083:8080 distapp 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您可以通过停止和启动容器来测试您的部署，并在:
设置/获取变量

```
localhost:8080/set/5
localhost:8082/get/5
etc... 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

重要的是，这是一个真正基本的分布式系统，它可能会变得不一致(如果您同时在两台不同的机器上更新该值，集群将根据机器的不同而有两个值)。如果你想了解更多，可以看看 CAP、consensus、Paxos、RAFT、gossip 和数据复制，它们都是非常有趣的话题(至少在我看来是这样)。

不管怎样，我希望你在创建一个小型分布式系统的过程中获得了乐趣，并鼓励你构建自己的、更高级的系统，这肯定会是一次很棒的学习经历！

完整的代码可以在我的 Github 上找到。