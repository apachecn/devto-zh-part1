# 自动 Pi 查找器或任何设备真的

> 原文：<https://dev.to/nizarmah/auto-pi-finder-or-any-device-really>

这样做的目的是演示如何使用 Android 应用程序在网络上自动查找 Pi。

为了理解需要什么，我们应该讨论 ARP 表和 ip 地址的快速分段，因为为什么不呢。

### 什么是 ARP 表？

ARP 代表地址解析协议。它将网络地址链接到物理地址，换句话说，将每个 ip 地址链接到一个 mac 地址。

当两个设备在网络上交互时就会发生这种情况，在此期间，每个设备的 mac 地址在一个特定的 ip 地址下与另一个设备共享，并相应地链接起来。

但是当然，存储所有这些交互会堵塞设备的内存；因此，ARP 表会不时地被清除。
但是我还是没有解释什么是 ARP 表；它只是一个包含每个 ip 地址和伴随它的 mac 地址的文件。

正如我们所说的，ARP 表被清除，但是这在不同的设备和它们能处理多少之间是不同的。安卓手机，嗯，安卓手机，桌子经常被清理(大约 5 分钟)。没有`nmap`也无助于列出同一网络上的设备。

此外，谁不想在手机上查看哪些设备连接到了自己的互联网？嗯，我不知道你是怎么想的，但是开始我正在做的项目是至关重要的。

### 快速介绍 IP 地址

嗯，有多类 ip 地址拥有多台主机。假设 ip 地址的格式是`a.b.c.d`。嗯，`a`指定了类。

对于范围在[1，126]之间的`a`，主机约为 600 万。(a 类)
忽略当`a` = 127 时，它是本地网络地址。
对于`a`范围在【128，191】之间，宿主约 30 万。(b 类)
对于`a`范围在【192，223】之间，主机其实是 253。(c 类)

现在后者被称为 c 类。还有另外两个，但它们不用于私有 ip 地址。

为什么主持人会这样？嗯，`b`、`c`和`d`在恒定和交替之间变化。

对于 a 级，`b`、`c`和`d`是变化的。它们在 0 和 255 之间变化；不过`d`也有一些例外。
对于 b 级，`c`和`d`是变化的。
对于 c 类，`d`只是变化的。

### 那么我们开始吧！

> 请注意，这只适用于 c 类专用 ips 网络(192-223:IP 地址的第一部分)

但是，两个设备之间最快的交互方式和最简单的 ping 是什么呢？设置流程，从检查在线主机，到 ping 它们，然后收集 ARP 表。

#### 检查在线主机

为了检查在线主机，我对网络上所有可用的主机执行 ping 操作。这相当耗时，但我找不到任何替代方法。ping 网络的广播 ip 也不会更快。因此，我不得不将其限制在一个 C 类私有 ip 地址网络中。

你可以使用 Java，ping 从 1 到 254 的所有 ip 地址。相反，我选择使用 Android Shell 来做所有的事情。

我们需要检查可用的主机，然后重新排列它们，原因将在下一步中解释。为此，我们需要保存哪些给了我们响应。这是使用下面的终端命令完成的。

```
((ping -c 1 192.168.1.etc) > /dev/null) && (echo "up") || (echo "down") 
```

Enter fullscreen mode Exit fullscreen mode

但是等等我，Android Shell 没有 bash 自动，所以这个命令不行。因此，您必须在其中包含 sh 标签。现在，为了提高效率，我把它变成了几乎异步的。这样，所有能给我响应的设备都会同时给我响应。

> 由于异步，当两个响应同时返回时，我的计数器出错。为了避免这种情况，我制作了一个处理偶数和奇数的计数器，这样连续的响应就不会在相同的时间减少计数器，从而只减少一次。(通过在相同的时间调用计数器，我两次访问了变量的相同值，因此不是减少两次，而是只减少一次)。

```
/*
 Setting a Counter to keep track
 Of the stuff that have finished
 In order to prevent Async Calling for
 Counter, had to delay it without actually
 Delaying it, so divided the Counting
*/
class Counter {
    int i = 0, j = 0;
    public Counter(int m) {
        if (m % 2 > 0) {
            this.i = (int) Math.floor((double) m / 2) + 1;
            this.j = (int) Math.floor((double) m / 2);
        } else {
            this.i = (int) Math.floor((double) m / 2) + 1;
            this.j = (int) Math.floor((double) m / 2);
        }
    }
    public void resize(int i) {
        this.i = i;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 我一直有一个错误，一个主机根本没有返回，知道 255 不会及时返回，我从 1 到 255 循环，并在 254 响应时继续下一个函数。

```
// This is a counter to keep track of what is done pinging
final Counter pingerCounter;
/*
 Asyncing ( the easy way ) Pinging each IP Address in order to
 Collect active ones with their Mac Address in ARP Cache Tables
 When done pinging, ARP runs to get the IP Address
 */
final Thread pinger;
pingerCounter = new Counter(254);
class Pinger implements Runnable {
    int y = 0, tempY = 0;

    Pinger (int y) {
        this.y = y;
    }

    public void run() {
        StringBuffer output = new StringBuffer();
        Process ping;
        try {
            /*
             Commands there cause ADB Shell on Android
             Doesn't support god damn inline operands
             So had to do it with Bash
             */
            String[] command = { "sh", "-c", "((ping -c 1 " + ipString + this.y + ") > /dev/null) && (echo 'up') || (echo 'down')" };
            ping = Runtime.getRuntime().exec(command);

            /*
             Setting TempY to store the valid Y
             Instead of storing the one after it
             */
            this.tempY = this.y;

            /*
             Here we're making sure that we end
             Up running all pings almost async
             */
            this.y -= 1;
            if (this.y > 0) new Thread(new Pinger(this.y)).start();

            ping.waitFor();

            // Log.i("PiFinder", "Checked host on " + ipString + this.tempY);
            // Log.i("PiFinder", (pingerCounter.i - 1) + " hosts to be check");

            // Grabbing output to know whether the address is up
            // So that we can easily read it into ARP Tables
            InputStream inStream = ping.getInputStream();
            // Needed to observe errors, thus this is here
            // InputStream errorStream = ping.getErrorStream();
            BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inStream));
            String line = "";
            StringBuilder response = new StringBuilder();

            while ((line = bufferedReader.readLine()) != null) {
                response.append(line);

                if (line.equals("up")) {
                    Log.i("PiFinder", "Available host on " + ipString + this.tempY);
                    pingables.add(ipString + this.tempY);
                }
            }

            /*
             Here we're checking for how many pings
             Were completed, so that we proceed
             In order to prevent Async Calling for
             Counter, had to delay it without actually
             Delaying it, so divided the Counting
             */
            if ((this.tempY % 2) == 1)
                pingerCounter.i--;
            else pingerCounter.j--;
            if ((pingerCounter.i + pingerCounter.j) == 0)
                new Thread(new Pingable(pingables.size())).start();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
pinger = new Thread(new Pinger(255));
pinger.start(); 
```

Enter fullscreen mode Exit fullscreen mode

#### ping 在线主机

当我说我要谈论 ARP 表时，我并没有提到一切。令人惊讶的是，我不得不提到 ARP 表有特定的行数。
所以 ping 所有的主机会得到一些被遗忘和埋葬在他们的兄弟。我们不想那样！所以，我们必须修理它们。

现在请注意，我假设使用我的应用程序的人的网络上不会有超过 90 个在线主机，所以我没有采取措施来处理超过 93 个主机。
为什么是 93？因为，在收集了我附近的 android 设备的 ARP 表的长度后，我发现所有的设备都有 93 个。所以，我假设 ARP 表最多有 93 台设备。

因此，重新排列它们类似于使用`-c1`之前的过程。但是不需要`sh`，即使我包括了它。

```
// List of Pingable Addresses or Online Hosts
final ArrayList<String> pingables = new ArrayList<String>();

// This is a counter to keep track of what is done pinging
final Counter pingableCounter = new Counter(0);
/*
 Repinging the Available Hosts to refresh
 the ARP Table Cache to make sure it's a Pi
 */
class Pingable implements Runnable {
    int i = 0, tempI = 0;

    Pingable(int m) {
        if (m == pingables.size()) {
            this.i = m - 1;
            pingableCounter.resize(m - 1);
        } else this.i = m;
    }

    public void run() {
        try {
            if (pingables.size() == 0)
                arp.start();
            else {
                String[] command = { "sh", "-c", "ping -c 1 " + pingables.get(this.i) };
                Process ping = Runtime.getRuntime().exec(command);

                // Setting TempY to store the valid I
                // Instead of storing the one after it
                this.tempI = this.i;

                // Here we're making sure that we end
                // Up running all pings almost async
                this.i -= 1;
                if (this.i > -1) new Thread(new Pingable(this.i)).start();

                Log.i("PiFinder", "Pinged host on " + pingables.get(this.tempI));

                ping.waitFor();

                /*
                 Here we're checking for how many pings
                 Were completed, so that we proceed
                 In order to prevent Async Calling for
                 Counter, had to delay it without actually
                 Delaying it, so divided the Counting
                 */
                if ((this.tempI % 2) == 0)
                    pingableCounter.i--;
                else pingableCounter.j--;
                if ((pingableCounter.i + pingableCounter.j) == 0)
                    arp.start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 收集 ARP 表

在 Android 的根文件夹中有一个特定的文件，表被缓存在那里。
文件在`/proc/net/arp`里。

在我的例子中，我想在 ARP 表中找到一个特定的设备。我在找覆盆子酱。哦，好吧，该死的，我想现在你知道我在为树莓派工作了。

总之，我添加了一些操作数来使用`grep`并搜索一个 raspberry pi 开始的特定 mac 地址，即著名的`b8:27:eb`。

因此，我所做的是，如下。

```
/*
 After getting the online hosts use ARP to get mac addresses
 Knowing that Pis start with B8:27:EB, group them accordingly
 This runs after the bottom thread pinger runs
 */
class ARP implements Runnable {
    public void run() {

        try {
            /*
             Commands there cause ADB Shell on Android
             Doesn't support god damn inline operands
             So had to do it with Bash
             */
            String[] command = { "sh", "-c", "arp -vn | grep -i ' b8:27:eb'" };
            Process arpa = Runtime.getRuntime().exec(command);

            arpa.waitFor();

            Log.i("PiFinder", "Collected ARP Table");

            // Read out available Pi addresses
            InputStream inStream = arpa.getInputStream();
            // Needed to observe errors, thus this is here
            // InputStream errorStream = ping.getErrorStream();
            BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inStream));
            String line = "";
            StringBuilder response = new StringBuilder();

            ArrayList<ArrayList<String>> pis = new ArrayList<ArrayList<String>>();

            /*
             Since each line is an address, then
             Execute on each line read by buffer
             Then split to get the necessary fields
             */
            while ((line = bufferedReader.readLine()) != null) {
                response.append(line);

                String[] tempLine = line.split("\\s+");

                ArrayList<String> pi = new ArrayList<String>();
                pi.add(tempLine[1].substring(1, tempLine[1].length() - 1));
                pi.add(tempLine[3]);

                pis.add(pi);
            }
                if (pis.size() == 0) {
                    Log.i("PiFinder", "No Pis found");
                    // Use a callback function here (negative)
                } else { // Use a callback function here (positive) }
        } catch (IOException e){
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
final Thread arp = new Thread(new ARP()); 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你不想收集任何特定的东西，你可以用 Java 来读取这个文件。您也可以删除`arp -vn`之后的部分，然后将每一行的集合更改为具有 mac 和 ip 地址的特定设备。

##### 但是我们遗漏的 ip 字段呢？！

很抱歉，我忘了...在这里。

```
WifiManager manager = (WifiManager) getApplicationContext().getSystemService(getApplicationContext().WIFI_SERVICE);
WifiInfo info = manager.getConnectionInfo();

long infoIp = info.getIpAddress();

String address = (infoIp & 0xFF) +
        "." + ((infoIp >> 8) & 0xFF) +
        "." + ((infoIp >> 16) & 0xFF) +
        "." + ((infoIp >> 24) & 0xFF);

String[] ip = address.split("\\.");
ipAddress = new String[]{ ip[0], ip[1], ip[2] };

for (int i = 0; i < ipAddress.length; i++)
    ipString = ipString + ipAddress[i] + "."; 
```

Enter fullscreen mode Exit fullscreen mode

并在全球范围内宣布...

```
String[] ipAddress;
String ipString = ""; 
```

Enter fullscreen mode Exit fullscreen mode

这是我的旅程。我希望我的教程是清晰的，而不是抽象的。我已经测试了代码，非常确定它可以工作。
把它们都放在一个地方，用这个。

```
import android.net.wifi.WifiInfo;
import android.net.wifi.WifiManager;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {

    String[] ipAddress;
    String ipString = "";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_process);

        findMePi();
    }

    private void findMePi() {
        WifiManager manager = (WifiManager) getApplicationContext().getSystemService(getApplicationContext().WIFI_SERVICE);
        WifiInfo info = manager.getConnectionInfo();

        long infoIp = info.getIpAddress();

        String address = (infoIp & 0xFF) +
                "." + ((infoIp >> 8) & 0xFF) +
                "." + ((infoIp >> 16) & 0xFF) +
                "." + ((infoIp >> 24) & 0xFF);

        String[] ip = address.split("\\.");
        ipAddress = new String[]{ ip[0], ip[1], ip[2] };

        for (int i = 0; i < ipAddress.length; i++)
            ipString = ipString + ipAddress[i] + ".";

        class Counter {
            int i = 0, j = 0;
            public Counter(int m) {
                if (m % 2 > 0) {
                    this.i = (int) Math.floor((double) m / 2) + 1;
                    this.j = (int) Math.floor((double) m / 2);
                } else {
                    this.i = (int) Math.floor((double) m / 2) + 1;
                    this.j = (int) Math.floor((double) m / 2);
                }
            }
            public void resize(int i) {
                this.i = i;
            }
        }

        class ARP implements Runnable {
            public void run() {

                try {
                    String[] command = { "sh", "-c", "arp -vn | grep -i ' b8:27:eb'" };
                    Process arpa = Runtime.getRuntime().exec(command);

                    arpa.waitFor();

                    Log.i("PiFinder", "Collected ARP Table");

                    // Read out available Pi addresses
                    InputStream inStream = arpa.getInputStream();
                    // Needed to observe errors, thus this is here
                    // InputStream errorStream = ping.getErrorStream();
                    BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inStream));
                    String line = "";
                    StringBuilder response = new StringBuilder();

                    ArrayList<ArrayList<String>> pis = new ArrayList<ArrayList<String>>();

                    while ((line = bufferedReader.readLine()) != null) {
                        response.append(line);

                        String[] tempLine = line.split("\\s+");

                        ArrayList<String> pi = new ArrayList<String>();
                        pi.add(tempLine[1].substring(1, tempLine[1].length() - 1));
                        pi.add(tempLine[3]);

                        pis.add(pi);
                    }
                    if (pis.size() == 0) {
                        Log.i("PiFinder", "No Pis found");
                        // Use Callback Here ( Negative )
                    } else {
                        // Use Callback Here ( Positive )
                    }
                } catch (IOException e){
                    e.printStackTrace();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
        final Thread arp = new Thread(new ARP());

        final ArrayList<String> pingables = new ArrayList<String>();

        final Counter pingableCounter = new Counter(0);

        class Pingable implements Runnable {
            int i = 0, tempI = 0;

            Pingable(int m) {
                if (m == pingables.size()) {
                    this.i = m - 1;
                    pingableCounter.resize(m - 1);
                } else this.i = m;
            }

            public void run() {
                try {
                    if (pingables.size() == 0)
                        arp.start();
                    else {
                        String[] command = { "sh", "-c", "ping -c 1 " + pingables.get(this.i) };
                        Process ping = Runtime.getRuntime().exec(command);

                        this.tempI = this.i;

                        this.i -= 1;
                        if (this.i > -1) new Thread(new Pingable(this.i)).start();

                        Log.i("PiFinder", "Pinged host on " + pingables.get(this.tempI));

                        ping.waitFor();

                        if ((this.tempI % 2) == 0)
                            pingableCounter.i--;
                        else pingableCounter.j--;
                        if ((pingableCounter.i + pingableCounter.j) == 0)
                            arp.start();
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }

        final Counter pingerCounter;

        final Thread pinger;
        pingerCounter = new Counter(254);
        class Pinger implements Runnable {
            int y = 0, tempY = 0;

            Pinger (int y) {
                this.y = y;
            }

            public void run() {
                StringBuffer output = new StringBuffer();
                Process ping;
                try {
                    String[] command = { "sh", "-c", "((ping -c 1 " + ipString + this.y + ") > /dev/null) && (echo 'up') || (echo 'down')" };
                    ping = Runtime.getRuntime().exec(command);

                    this.tempY = this.y;

                    this.y -= 1;
                    if (this.y > 0) new Thread(new Pinger(this.y)).start();

                    ping.waitFor();
                    InputStream inStream = ping.getInputStream();
                    BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inStream));
                    String line = "";
                    StringBuilder response = new StringBuilder();

                    while ((line = bufferedReader.readLine()) != null) {
                        response.append(line);

                        if (line.toString().equals("up")) {
                            Log.i("PiFinder", "Available host on " + ipString + this.tempY);
                            pingables.add(ipString + this.tempY);
                        }
                    }

                    if ((this.tempY % 2) == 1)
                        pingerCounter.i--;
                    else pingerCounter.j--;
                    if ((pingerCounter.i + pingerCounter.j) == 0)
                        new Thread(new Pingable(pingables.size())).start();
                } catch (IOException e) {
                    e.printStackTrace();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
        pinger = new Thread(new Pinger(255));
        pinger.start();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我希望我的代码没有那么糟糕。