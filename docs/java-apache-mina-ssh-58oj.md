# 用 Java 和 Apache MINA 轻松创建 SSH 服务器

> 原文：<https://dev.to/shiena/java-apache-mina-ssh-58oj>

# 前言

[使用 Apache MINA](https://mina.apache.org/) 可以用 Java 简单地制作 SSH 服务器。
以下是以[嵌入式 SSHd in5minutes](https://mina.apache.org/sshd-project/embedding_ssh.html)为基础的 ssh 服务器的来源。

# 酱

```
package org.example.ssh;

import org.apache.sshd.SshServer;
import org.apache.sshd.common.NamedFactory;
import org.apache.sshd.common.io.mina.MinaServiceFactory;
import org.apache.sshd.common.util.OsUtils;
import org.apache.sshd.server.Command;
import org.apache.sshd.server.PasswordAuthenticator;
import org.apache.sshd.server.command.ScpCommandFactory;
import org.apache.sshd.server.keyprovider.SimpleGeneratorHostKeyProvider;
import org.apache.sshd.server.session.ServerSession;
import org.apache.sshd.server.shell.ProcessShellFactory;
import org.apache.sshd.sftp.subsystem.SftpSubsystem;

import java.io.IOException;
import java.util.ArrayList;
import java.util.EnumSet;

import static org.apache.sshd.server.shell.ProcessShellFactory.TtyOptions;

public class Server {
    public static void main(String[] args) {
        SshServer sshd = SshServer.setUpDefaultServer();
        sshd.setPort(10022);

        sshd.setKeyPairProvider(new SimpleGeneratorHostKeyProvider("hostkey.ser"));

        sshd.setPasswordAuthenticator(new PasswordAuthenticator() {
            @Override
            public boolean authenticate(
                    String username, String password, ServerSession session) {
                // ここでユーザ認証を行う
                return true;
            }
        });

        // sshのログインシェルを設定
        EnumSet<TtyOptions> options;
        String[] command;
        if (OsUtils.isWin32()) {
            options = EnumSet.of(TtyOptions.ONlCr, TtyOptions.Echo, TtyOptions.ICrNl);
            command = new String[]{"cmd"};
        } else {
            options = EnumSet.of(TtyOptions.ONlCr);
            command = new String[]{"/bin/sh", "-i", "-l"};
        }
        sshd.setShellFactory(new ProcessShellFactory(command, options));

        // scpの設定
        sshd.setCommandFactory(new ScpCommandFactory());

        // sftpの設定
        sshd.setSubsystemFactories(new ArrayList<NamedFactory<Command>>(1) {
            {
                add(new SftpSubsystem.Factory());
            }
        });

        sshd.setIoServiceFactory(new MinaServiceFactory());

        try {
            sshd.start();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
} 
```

```
apply plugin: 'application'

compileJava.options*.encoding = 'UTF-8'

repositories {
    mavenCentral()
}

mainClassName = 'org.example.ssh.Server'

dependencies {
    compile (
        'org.apache.sshd:apache-sshd:0.9.0',
    )
}

// IntelliJ IDEA 用の設定
import org.gradle.plugins.ide.idea.model.*
apply plugin: 'idea'
idea {
    pathVariables 'GRADLE_USER_HOME': gradle.gradleUserHomeDir
    project {
        jdkName = '1.7'
        languageLevel = '1.7'
    }
}

// Eclipse 用の設定
apply plugin: 'eclipse'
eclipse {
    pathVariables 'GRADLE_USER_HOME': gradle.gradleUserHomeDir
} 
```

```
build.gradle
src/
  main/
    java/
      org/
        example/
          ssh/
            Server.java 
```

# 构建和执行

可以在 Gradle 中运行构建和服务器。
在`Building 75%`时停止，但由于服务器已经启动，所以可以通过 ssh 连接到 10022 端口。
因为没有检查用户和密码，所以什么都会通过。
scp 和 sftp 也可以同样连接。

```
$ gradle build
:compileJava
Download http://repo1.maven.org/maven2/org/apache/sshd/apache-sshd/0.9.0/apache-sshd-0.9.0.pom
Download http://repo1.maven.org/maven2/org/apache/sshd/sshd/0.9.0/sshd-0.9.0.pom
Download http://repo1.maven.org/maven2/org/apache/sshd/sshd-core/0.9.0/sshd-core-0.9.0.pom
Download http://repo1.maven.org/maven2/org/apache/sshd/sshd-sftp/0.9.0/sshd-sftp-0.9.0.pom
Download http://repo1.maven.org/maven2/org/apache/sshd/sshd-pam/0.9.0/sshd-pam-0.9.0.pom
Download http://repo1.maven.org/maven2/org/slf4j/slf4j-simple/1.6.4/slf4j-simple-1.6.4.pom
Download http://repo1.maven.org/maven2/org/slf4j/slf4j-parent/1.6.4/slf4j-parent-1.6.4.pom
Download http://repo1.maven.org/maven2/bouncycastle/bcprov-jdk15/140/bcprov-jdk15-140.pom
Download http://repo1.maven.org/maven2/tomcat/tomcat-apr/5.5.23/tomcat-apr-5.5.23.pom
Download http://repo1.maven.org/maven2/tomcat/tomcat-parent/5.5.23/tomcat-parent-5.5.23.pom
Download http://repo1.maven.org/maven2/org/apache/mina/mina-core/2.0.7/mina-core-2.0.7.pom
Download http://repo1.maven.org/maven2/org/apache/mina/mina-parent/2.0.7/mina-parent-2.0.7.pom
Download http://repo1.maven.org/maven2/org/slf4j/slf4j-api/1.6.4/slf4j-api-1.6.4.pom
Download http://repo1.maven.org/maven2/org/slf4j/slf4j-api/1.6.6/slf4j-api-1.6.6.pom
Download http://repo1.maven.org/maven2/org/slf4j/slf4j-parent/1.6.6/slf4j-parent-1.6.6.pom
Download http://repo1.maven.org/maven2/org/apache/sshd/sshd-core/0.9.0/sshd-core-0.9.0.jar
Download http://repo1.maven.org/maven2/org/apache/sshd/sshd-sftp/0.9.0/sshd-sftp-0.9.0.jar
Download http://repo1.maven.org/maven2/org/apache/sshd/sshd-pam/0.9.0/sshd-pam-0.9.0.jar
Download http://repo1.maven.org/maven2/org/slf4j/slf4j-simple/1.6.4/slf4j-simple-1.6.4.jar
Download http://repo1.maven.org/maven2/bouncycastle/bcprov-jdk15/140/bcprov-jdk15-140.jar
Download http://repo1.maven.org/maven2/tomcat/tomcat-apr/5.5.23/tomcat-apr-5.5.23.jar
Download http://repo1.maven.org/maven2/org/apache/mina/mina-core/2.0.7/mina-core-2.0.7.jar
Download http://repo1.maven.org/maven2/org/slf4j/slf4j-api/1.6.6/slf4j-api-1.6.6.jar
:compileJava UP-TO-DATE
:processResources UP-TO-DATE
:classes UP-TO-DATE
:jar
:assemble
:compileTestJava UP-TO-DATE
:processTestResources UP-TO-DATE
:testClasses UP-TO-DATE
:test UP-TO-DATE
:check UP-TO-DATE
:build

BUILD SUCCESSFUL

Total time: 30.948 secs 
```

```
$ gradle run
:compileJava UP-TO-DATE
:processResources UP-TO-DATE
:classes UP-TO-DATE
:run
216 [main] INFO org.apache.sshd.common.util.SecurityUtils - Trying to register BouncyCastle as a JCE provider
704 [main] INFO org.apache.sshd.common.util.SecurityUtils - Registration succeeded
> Building 75% > :run 
```

嗯，很简单吧？