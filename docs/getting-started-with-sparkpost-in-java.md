# Java SparkPost 入门

> 原文：<https://dev.to/sparkpost/getting-started-with-sparkpost-in-java>

SparkPost Java 库是一种与 SparkPost 集成的流行方式。如果这描述了你当前的任务，这篇文章将帮助你在几分钟内发送电子邮件。如果你是视觉学习者，看看这个[入门视频](https://www.youtube.com/watch?v=KyBXABMe4mo)，我们为 SparkPost Java 客户端库准备的。

我假设你已经[创建了一个 SparkPost 账户](https://pages.sparkpost.com/bl-account-signup-generic.html?src=Blog&pc=bl-rfi-generic-2017&utm_source=blog&utm_medium=blog&utm_campaign=all&utm_content=signup)和一个 [API 密匙](https://www.sparkpost.com/docs/getting-started/create-api-keys/)。

### 设置新项目

作为一名 Java 开发人员，我相信您非常熟悉如何向项目添加依赖项。我将在下面的代码片段中演示如何使用 Maven (mvn)实现这一点。如果您没有使用 Maven，您将需要创建项目，并以正常方式向其中添加 SparkPost 库。

让我们创建项目。

```
> mvn archetype:generate -DgroupId=com.example -DartifactId=SparkPostDemo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building Maven Stub Project (No POM) 1
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] >>> maven-archetype-plugin:3.0.1:generate (default-cli) > generate-sources @ standalone-pom >>>
[INFO] 
[INFO] <<< maven-archetype-plugin:3.0.1:generate (default-cli) < generate-sources @ standalone-pom <<<
[INFO] 
[INFO] --- maven-archetype-plugin:3.0.1:generate (default-cli) @ standalone-pom ---
[INFO] Generating project in Batch mode
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Old (1.x) Archetype: maven-archetype-quickstart:1.0
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: basedir, Value: /private/tmp
[INFO] Parameter: package, Value: com.example
[INFO] Parameter: groupId, Value: com.example
[INFO] Parameter: artifactId, Value: SparkPostDemo
[INFO] Parameter: packageName, Value: com.example
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] project created from Old (1.x) Archetype in dir: /private/tmp/SparkPostDemo
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 3.888 s
[INFO] Finished at: 2017-08-11T10:52:54-05:00
[INFO] Final Memory: 18M/317M
[INFO] ------------------------------------------------------------------------ 
```

Enter fullscreen mode Exit fullscreen mode

使用上面的代码，我们刚刚创建了以下项目结构:。

```
> cd SparkPostDemo
> find .
.
./pom.xml
./src
./src/main
./src/main/java
./src/main/java/com
./src/main/java/com/example
./src/main/java/com/example/App.java
./src/test
./src/test/java
./src/test/java/com
./src/test/java/com/example
./src/test/java/com/example/AppTest.java 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们构建基础项目:

```
> mvn clean install

[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building SparkPostDemo 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
                 :
   [Snipped out lots of output]
                 :
 -------------------------------------------------------
 T E S T S
 -------------------------------------------------------
Running com.example.AppTest
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.003 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] 
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ SparkPostDemo ---
[INFO] Building jar: /private/tmp/SparkPostDemo/target/SparkPostDemo-1.0-SNAPSHOT.jar
[INFO] 
[INFO] --- maven-install-plugin:2.4:install (default-install) @ SparkPostDemo 
--------
[INFO] Installing 
/private/tmp/SparkPostDemo/target/SparkPostDemo-1.0-SNAPSHOT.jar to 
/Users/user/.m2/repository/com/example/SparkPostDemo/1.0-SNAPSHOT/SparkPostDem
o-1.0-SNAPSHOT.jar
[INFO] Installing /private/tmp/SparkPostDemo/pom.xml to 
/Users/user/.m2/repository/com/example/SparkPostDemo/1.0-SNAPSHOT/SparkPostDem
o-1.0-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 5.128 s
[INFO] Finished at: 2017-08-11T10:55:57-05:00
[INFO] Final Memory: 19M/286M
[INFO] ------------------------------------------------------------------------ 
```

Enter fullscreen mode Exit fullscreen mode

### 添加 SparkPost 库依赖

现在让我们编辑 Maven 项目对象模型`(pom.xml)`并添加 SparkPost 库依赖项。你可以在这里找到最新版本[的代码片段。将它添加到`“pom.xml”`文件的`“dependencies”`元素中。这里有一个截至本文写作时的最新版本的例子:](https://github.com/SparkPost/java-sparkpost#getting-started) 

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>SparkPostDemo</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>SparkPostDemo</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>

    <!-- SparkPost Dependency -->
    <dependency>
        <groupId>com.sparkpost</groupId>
        <artifactId>sparkpost-lib</artifactId>
        <version>0.19</version>
    </dependency>

  </dependencies>
</project> 
```

Enter fullscreen mode Exit fullscreen mode

一旦添加了依赖项，就像这样重新构建项目:

```
mvn clean install
[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building SparkPostDemo 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
                 :
   [Snipped out some output]
                 :
------------------------------------------------------------
 T E S T S
------------------------------------------------------------
Running com.example.AppTest
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.006 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] 
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ SparkPostDemo ---
[INFO] Building jar: /private/tmp/SparkPostDemo/target/SparkPostDemo-1.0-SNAPSHOT.jar
[INFO] 
[INFO] --- maven-install-plugin:2.4:install (default-install) @ SparkPostDemo ---
[INFO] Installing /private/tmp/SparkPostDemo/target/SparkPostDemo-1.0-SNAPSHOT.jar to /Users/user/.m2/repository/com/example/SparkPostDemo/1.0-SNAPSHOT/SparkPostDemo-1.0-SNAPSHOT.jar
[INFO] Installing /private/tmp/SparkPostDemo/pom.xml to /Users/user/.m2/repository/com/example/SparkPostDemo/1.0-SNAPSHOT/SparkPostDemo-1.0-SNAPSHOT.pom
[INFO] --------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] --------------------------------------------------------------------
[INFO] Total time: 2.793 s
[INFO] Finished
[INFO] Final Memory:
[INFO] -------------------------------------------------------------------- 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们就可以开始尝试了！

### 发送您的第一封邮件

发送电子邮件最简单的方法是使用`SendMessage`呼叫。下面是一个例子:

```
package com.example;

import com.sparkpost.Client;
import com.sparkpost.exception.SparkPostException;

/**
 * My First Email Example
 *
 */
public class App 
{
    public static void main(String[] args) throws SparkPostException {
        String API_KEY = "YOUR API KEY HERE";
        Client client = new Client(API_KEY);

        client.sendMessage(
                "from@example.com",
                "to@example.com",
                "The subject of the message",
                "The text part of the email",
                "<b>The HTML part of the email</b>");

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

要使此操作适用于您自己的 SparkPost 帐户，您需要进行以下更改:

1.  在上面的示例代码中，用你的 SparkPost [API 键](https://app.sparkpost.com/account/credentials)替换`YOUR API KEY HERE`。
2.  将`from@example.com`更改为您的[验证域名](https://app.sparkpost.com/account/sending-domains)的电子邮件地址。
3.  将`to@example.com`更改为您想要发送电子邮件的地址。
4.  编译并运行您的代码，您将看到电子邮件到达收件箱。

### 更高级的例子

`sendMessage(...)`功能有助于快速启动和运行。不过以后可能需要做更高级的操作。这里有一个例子可以帮助你利用 SparkPost API 的全部功能。

```
package com.example;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import com.sparkpost.Client;
import com.sparkpost.exception.SparkPostException;
import com.sparkpost.model.AddressAttributes;
import com.sparkpost.model.RecipientAttributes;
import com.sparkpost.model.TemplateContentAttributes;
import com.sparkpost.model.TransmissionWithRecipientArray;
import com.sparkpost.model.responses.TransmissionCreateResponse;
import com.sparkpost.resources.ResourceTransmissions;
import com.sparkpost.transport.IRestConnection;
import com.sparkpost.transport.RestConnection;

public class TemplateExample {
    private Client client;

    public static final void main(String[] args) throws SparkPostException {
        TemplateExample example = new TemplateExample();
        example.sendEmail();
    }

    public void sendEmail() throws SparkPostException {

        // Setup a SparkPost client
        client = new Client("YOUR API KEY HERE");
        client.setFromEmail("from@example.com");

        // Create our Transmission object that will be used to send the transmission
        TransmissionWithRecipientArray transmission = new TransmissionWithRecipientArray();

        // Populate Recipients
        String[] recipients = new String[] {
                "to1@example.com",
                "to2@example.com"
        };

        List<RecipientAttributes> recipientArray = new ArrayList<RecipientAttributes>();
        for (String recipient : recipients) {
            RecipientAttributes recipientAttribs = new RecipientAttributes();
            recipientAttribs.setAddress(new AddressAttributes(recipient));
            recipientArray.add(recipientAttribs);
        }
        transmission.setRecipientArray(recipientArray);

        // Populate Substitution Data. This will replace content in the template with the value defined here.
        Map<String, Object> substitutionData = new HashMap<String, Object>();
        substitutionData.put("extraSubjectContent", "More Subject Content");
        substitutionData.put("extraBodyContent", "You can add substitution data too.");
        transmission.setSubstitutionData(substitutionData);

        // Populate Email Body
        TemplateContentAttributes contentAttributes = new TemplateContentAttributes();
        contentAttributes.setFrom(new AddressAttributes(client.getFromEmail()));
        contentAttributes.setSubject("Your subject content here. {{extraSubjectContent}}");
        contentAttributes.setText("Your Text content here. {{extraBodyContent}}");
        contentAttributes.setHtml("<p>Your <b>HTML</b> content here. {{extraBodyContent}}</p>");
        transmission.setContentAttributes(contentAttributes);

        transmission.setContentAttributes(contentAttributes);

        IRestConnection connection = new RestConnection(this.client);
        TransmissionCreateResponse transmissionResponse = ResourceTransmissions.create(connection, 0, transmission);

        System.out.println(transmissionResponse);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

要使此操作适用于你的 SparkPost 帐户，你需要进行与之前相同的更改:

1.  在示例代码中，用你的 SparkPost [API 键](https://app.sparkpost.com/account/credentials)替换`YOUR API KEY HERE`。
2.  将`from@example.com`更改为您的[验证域名](https://app.sparkpost.com/account/sending-domains)的电子邮件地址。
3.  将`to1@example.com`和`to2@example.com`更改为您想要发送电子邮件的地址。
4.  编译并运行您的代码，您将看到电子邮件到达收件箱。

你可以在这里找到更多利用 SparkPost API [的例子。](https://github.com/SparkPost/java-sparkpost/tree/master/apps/sparkpost-samples-app/src/main/java/com/sparkpost/samples)

### 结论

我们试图让 SparkPost 和 Java 一起使用像编写其他 Java 代码一样简单和愉快。如果您遇到任何问题或有任何疑问，请随时在 Github [repo](https://github.com/SparkPost/java-sparkpost) 上提交问题，或者加入我们在#java channel 的社区 Slack 团队。我很乐意收到你的来信。

*本帖最初发表于[sparkpost.com](https://www.sparkpost.com/blog/sparkpost-in-java/)T3】*