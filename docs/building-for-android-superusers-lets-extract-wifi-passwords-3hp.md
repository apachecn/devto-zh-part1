# 为 Android 超级用户构建:让我们提取 WiFi 密码

> 原文：<https://dev.to/idoko/building-for-android-superusers-lets-extract-wifi-passwords-3hp>

开始是一个 Manifest 权限入口，大 G 标记了`android.permission.ACCESS_SUPERUSER`，还可以。但是后来 Android Lollipop 来了，简单的片段被去掉了，Chainfire 的超级用户库来救场了。

#### 什么是 lib 超级用户？

Libsuperuser 是一个 android 库，它使得在你的应用程序中请求和维护 root 访问权限变得如你所能想象的那样简单。我们将仅仅在这个演示应用程序中实现它，但是如果你想更深入地挖掘(当然你应该)，你可以查看完整的 [HOW-to SU 帖子](http://su.chainfire.eu/)

#### 我们在建造什么？

我们将创建一个名为 Fi.Sniff 的应用程序。Android 将其无线网络数据- SSID，Key 等-存储在一个名为`wpa_supplicant.conf`的文件中，该文件位于`/data/misc/wifi/`目录中，只有根用户才能访问。一旦用户在 Fi 中给了我们 root 访问权限，这就是我们将要探索和解析的文件。嗅嗅 app。
在我们开始之前，让我们确保我们已经安装了 android studio 和 SDK，并且可以像一部根深蒂固的 android 手机一样工作。模拟器在这里不起作用。

#### 让我们开始编码吧！

打开 android studio，创建一个新的 android 项目。一旦项目完成，android studio 准备就绪，我们需要添加两个依赖项，第一个是 lib 超级用户，然后是 android 的 recyclerview 库来处理我们的列表。所以打开你的`app/build.gradle`，在`dependencies`块中添加以下内容:

```
implementation 'eu.chainfire:libsuperuser:1.0.0.+'
implementation 'com.android.support:recyclerview-v7:26.0.0' 
```

Enter fullscreen mode Exit fullscreen mode

现在进入应用程序的核心部分，让我们编辑与我们的主活动相关联的布局文件(我假设这里是`activity_main.xml`),并用下面的内容替换它:

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.okmichaels.fisniff.MainActivity">

    <android.support.v7.widget.RecyclerView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/network_list_recycler_view">

    </android.support.v7.widget.RecyclerView>

</android.support.constraint.ConstraintLayout> 
```

Enter fullscreen mode Exit fullscreen mode

对于上面的代码块以及所有后续代码块，请确保用您的应用程序替换包名。完成后，让我们撒一些回收视图适配器及其各种成分。但是在我们深入适配器之前，让我们创建一个布局，它将在用户的屏幕上反映我们的每个 wifi 项目。创建一个新的布局文件(network_item_layout)并添加下面的代码:

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <TextView
        android:id="@+id/network_ssid_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="14sp"
        android:textStyle="bold"/>

    <TextView
        android:id="@+id/network_psk_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

</LinearLayout> 
```

Enter fullscreen mode Exit fullscreen mode

对于适配器，我们将创建一个名为`NetworkListAdapter`的新类，以及一个关联的内部视图持有者类(`NetworkViewHolder`)。完成后，我们的适配器类应该是这样的:

```
package com.okmichaels.fisniff;

import android.content.Context;
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

import java.util.List;

public class NetworkListAdapter extends RecyclerView.Adapter<NetworkListAdapter.NetworkViewHolder> {

    private List<Network> networkList;
    private Context context;

    public NetworkListAdapter(Context ctx, List<Network> list) {
        networkList = list;
        context = ctx;
    }

    public int getItemCount() {
        return networkList.size();
    }

    public NetworkViewHolder onCreateViewHolder(ViewGroup parent, int viewtype) {
        final View view = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.network_item_layout, parent, false);
        return new NetworkViewHolder(view);
    }

    public void onBindViewHolder(NetworkViewHolder holder, int position) {
        Network network = networkList.get(position);
        holder.ssidView.setText(network.getSsid());
        holder.pskView.setText(network.getPsk());
    }

    class NetworkViewHolder extends RecyclerView.ViewHolder
    {
        TextView ssidView;
        TextView pskView;

        public NetworkViewHolder(View itemView) {
            super(itemView);
            ssidView = itemView.findViewById(R.id.network_ssid_view);
            pskView = itemView.findViewById(R.id.network_psk_view);
        }
    }

    public void refreshList(List<Network> networks) {
        this.networkList.clear();
        this.networkList.addAll(networks);
        this.notifyDataSetChanged();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

PS:如果整个 RecyclerView 和 ViewHolder 模式对你来说似乎很陌生，试着在这里查找官方文档或另一个人写的这个很棒的教程 PS:如果你在上面的 Network 类下面有一条细细的红线，Farabale——我们很快就会看到。

接下来，我们需要创建一个数据类(android 行话中的 ViewModel)。基本上，这个类反映了要在适配器中显示的项目，因此创建一个新的`Network`类并添加下面的代码:

```
package com.okmichaels.fisniff;

public class Network {
    private String ssid;
    private String psk;

    public Network() {
        this.ssid = null;
        this.psk = null;
    }

    public void setSsid(String ssid) {
        this.ssid = ssid;
    }

    public void setPsk(String psk) {
        this.psk = psk;
    }

    public Network(String ssid, String key) {
        this.ssid = ssid;
        this.psk = key;
    }

    public String getSsid() {
        return this.ssid;
    }

    public String getPsk() {
        return this.psk;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的主要活动。这就是事情变得有趣的地方。你还记得你添加的超级用户库吗？这就是你成功的地方，但是坚持住！首先修改您的主活动文件，使其看起来像这样:Android Studio 可能会抱怨未使用的导入等等等等，请它喝水并继续。

```
package com.okmichaels.fisniff;

import android.os.AsyncTask;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import eu.chainfire.libsuperuser.Shell;

public class MainActivity extends AppCompatActivity {

    public RecyclerView networkListView;
    NetworkListAdapter adapter;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        networkListView = findViewById(R.id.network_list_recycler_view);
        networkListView.setLayoutManager(new LinearLayoutManager(getApplicationContext()));
        adapter = new NetworkListAdapter(getApplicationContext(),
                new ArrayList<Network>());
        networkListView.setAdapter(adapter);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在回到外壳。好消息是这可能是最简单的部分。坏消息是你不能在主线程上运行 SU 命令，而且它会返回一个字符串数组列表，其中每个元素都是命令返回值中的一行。不管怎样，让我们把它加起来，然后就完事了，对吗？在您的主活动中添加一个新的 AsyncTask 子活动，并让它镜像如下所示的代码:(我的子活动名为 NetworkFetcherTask，我在必要的地方添加了注释，以阐明我们要做的事情)

```
class NetworkFetcherTask extends AsyncTask<Void, Void, List<Network>>
    {
        private boolean suEh;
        @Override
        protected List<Network> doInBackground(Void... voids) {
            List<Network> networks = new ArrayList<>();
            List<String> supplicant;
            /*this is what triggers the SuperUser permission on the device, if the phone is not rooted or the permissioin is denied, it returns false.*/
            suEh = Shell.SU.available();
            if (suEh) {
            /*android stores wifi data in the wpa_supplicant.conf file in the location below, let's try printing it ðŸ™ðŸ™*/
                supplicant = Shell.SU.run("cat /data/misc/wifi/wpa_supplicant.conf");
            /*the return value of SU.run() is an array list, let's flatten it into a string to ease parsing with regex*/
                StringBuilder flattener = new StringBuilder();
                if (supplicant != null) {
                    for (String line: supplicant) {
                        flattener.append(line);
                    }
                }
                String flattened = flattener.toString();

                /*okay, let's find every network block(they exist within curly brackets hence the expression*/
                String exp = "\\{(.*?)\\}";
                Pattern pattern = Pattern.compile(exp);
                Matcher matcher = pattern.matcher(flattened);

                while (matcher.find()) {
                    Network network = new Network();
                    String el = matcher.group();
                    //List<String> lineAsItem = Arrays.asList(el.split(" "));
                    String[] lineAsItem = el.split("\\s+");
                    for(String line: lineAsItem) {

                        if (line.contains("=")) {
                            String[] t = line.split("=");
                            if (t[0].equals("ssid")) {
                                network.setSsid(t[1]);
                            }
                            if (t[0].equals("psk")) {
                                network.setPsk(t[1]);
                            }
                        }
                    }
                    networks.add(network);
                }
            }
            return networks;
        }

        protected void onPostExecute(List<Network> result) {
            adapter.refreshList(result);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

还有一件事。我们需要请求我们的 MainActivity(很好地)执行这个异步任务，所以在 MainActivity 的 onCreate()方法中添加以下内容，以便在活动设置完成后调用任务:

```
 new NetworkFetcherTask().execute(); 
```

Enter fullscreen mode Exit fullscreen mode

运行您的应用程序，感受荣耀！