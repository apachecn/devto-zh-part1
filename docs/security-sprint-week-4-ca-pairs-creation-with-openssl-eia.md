# 安全冲刺周 4:用 openssl 创建 CA 对

> 原文：<https://dev.to/terceranexus6/security-sprint-week-4-ca-pairs-creation-with-openssl-eia>

似乎上周我没有写任何与安全相关的东西，所以一天后我会在这里，但我发现了一个很好的主题来写:**CA pairs creation with OpenSSL in Linux**。数字证书告诉应用程序(例如，浏览器)公钥属于调用它的人。这允许依赖方依赖对该公钥的签名。OpenSSL 是一个免费的开源加密库，它提供了几个命令行工具来处理数字证书。所以，现在我们正在做`root`，所以首先我建议`sudo -i`，然后做剩下的。

在这种情况下，我们正在根目录下创建一个名为“ca”的目录，所以首先让我们使用`mkdir /root/ca`来创建它(记住我们已经创建了 sudo -i，所以要小心),在它里面，让我们创建一堆我们以后会发现有用的目录。此外，我们给所有者权限(读+写+执行)。如果你对这篇文章中的 chmod 权限有疑问，我建议检查一下我发现的这个神奇的 [chmod 计算器](https://chmod-calculator.com/)。最后，我还创建了一个 index.txt 来跟踪签名证书，这是一个普通的数据库。

```
cd /root/ca
mkdir certs crl newcerts private
chmod 700 private
touch index.txt
echo 1000 > serial 
```

Enter fullscreen mode Exit fullscreen mode

现在我们应该定义 CA 根配置文件，我们应该在其中定义不同的模块供 openssl 稍后读取。这是我们正在使用的号。在强制的[ca]上，我们指示遵循[CA_default]中的指示。在[CA_default]中，我们应该指向我们已经创建的目录(CA)以及我们在其中创建的其余目录。在[policy_strict]和[policy_loose]中，我们应该为根和中间(我们将在后面解释)根 CA 定义策略。像这样，还有其他模块需要注意，还有一些定制的扩展，我们可以在以后用 openssl 调用它们作为命令，在这种情况下是[v3_ca]和其他模块，如[ usr_cert ]和[ server_cert ],用于在签署在线证书状态协议(OCSP)证书时签署 un 客户端或服务器案例或 ocsp 扩展。

一旦我们的配置文件被完全定义，我们可以确保它在`/root/ca`上，并继续创建根密钥。您可以使用 RSA 或 DSA，许多示例都显示了 RSA，因此为了创建一个不同的选项，我将使用 DSA。为了使用 DSA，我们应该首先创建参数:

```
openssl dsaparam -out dsaparam.pem 4096 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 4096 是因为我们仍然能够签署较短长度的服务器和客户端证书。然后，我们可以创建密钥:

```
openssl gendsa -aes256 -out private/ca.key.pem dsaparam.pem 
```

Enter fullscreen mode Exit fullscreen mode

我们将被要求输入密码，一旦完成，让我们用`chmod 400 private/ca.key.pem`处理权限。

为了创建根证书，我们检查我们是否在`/root/ca`中，然后使用 openssl 和`req`，这是我们之前在配置文件中定义的。

```
openssl req -config openssl.cnf -key private/ca.key.pem -new -x509 -days 7300 -sha256 -extensions v3_ca -out certs/ca.cert.pem 
```

Enter fullscreen mode Exit fullscreen mode

它将询问我们一个密码短语和证书的一些细节(名称、状态等)。一旦完成，让我们再次使用`chmod 444 certs/ca.cert.pem`的权限。为了验证内容，我们可以使用`openssl x509 -noout -text -in certs/ca.cert.pem`，输出应该显示算法、日期、签署证书的实体以及证书本身。嘣！

为了更好的安全性，我们应该在`/root/ca`目录中创建一个中间证书。为此，让我们创建一个`/root/ca/intermediate`目录，然后在其中创建更多的目录，就像我们之前对`/root/ca`T3 所做的那样

```
cd /root/ca/intermediate
mkdir certs crl csr newcerts private
chmod 700 private
touch index.txt
echo 1000 > serial 
```

Enter fullscreen mode Exit fullscreen mode

接下来，就是用`echo 1000 > /root/ca/intermediate/crlnumber`来跟踪证书撤销列表。现在，正如我们之前所做的，我们应该关注配置文件，它很像我们已经定义的文件，但是将[CA_default]中的目录更改为`/root/ca/intermediate`中的目录。

```
[ CA_default ]
dir             = /root/ca/intermediate
private_key     = $dir/private/intermediate.key.pem
certificate     = $dir/certs/intermediate.cert.pem
crl             = $dir/crl/intermediate.crl.pem
policy          = policy_loose 
```

Enter fullscreen mode Exit fullscreen mode

也是 v3_intermediate_ca 的扩展名 v3。完成的修改版本是[这里是](https://github.com/terceranexus6/SPSI/tree/master/intermediate)。

一旦有了这个，我们就可以创建中间密钥了。为此，请注意返回到`/root/ca`。我们将使用与之前类似的命令，为`/intermediate/`中的键更改路径。

```
openssl gendsa -aes256 -out intermediate/private/ca.key.pem dsaparam.pem 
```

Enter fullscreen mode Exit fullscreen mode

并且一如既往的用`chmod 400 intermediate/private/intermediate.key.pem`打理权限。为了创建中间证书签名请求(CSR)，我们应该在`/root/ca`中，并注意指定`/intermediate/`的 CA 配置，如下:

```
openssl req -config intermediate/openssl.cnf -new -sha256 -key intermediate/private/intermediate.key.pem -out intermediate/csr/intermediate.csr.pem 
```

Enter fullscreen mode Exit fullscreen mode

询问详情时，不要忘记在*常用名*中添加“中间标签”，例如:“John Doe Intermediate CA”。为了创建证书，在`/root/ca` :

```
openssl ca -config openssl.cnf -extensions v3_intermediate_ca -days 3650 -notext -md sha256 -in intermediate/csr/intermediate.csr.pem -out intermediate/certs/intermediate.cert.pem 
```

Enter fullscreen mode Exit fullscreen mode

我们用`chmod 444 intermediate/certs/intermediate.cert.pem`给出许可，然后我们应该验证我们的工作。为了验证我们写的细节:

```
openssl x509 -noout -text -in intermediate/certs/intermediate.cert.pem 
```

Enter fullscreen mode Exit fullscreen mode

并根据根 CA 对其进行验证:

```
openssl verify -CAfile certs/ca.cert.pem intermediate/certs/intermediate.cert.pem 
```

Enter fullscreen mode Exit fullscreen mode

这应该会返回一个一切正常的 **OK** 。最后，我们创建一个证书链文件，让应用程序对照根来检查 CA。

```
cat intermediate/certs/intermediate.cert.pem \
      certs/ca.cert.pem > intermediate/certs/ca-chain.cert.pem
chmod 444 intermediate/certs/ca-chain.cert.pem 
```

Enter fullscreen mode Exit fullscreen mode

还有，tadaaah！我们完成了！我还在学习签署服务器和客户端证书，我希望这是下一步。希望你们觉得这部分很有趣！同样，你可以尝试用 RSA 代替 DSA，用其他算法代替 aes。例如， **Camellia** 应该和 AES 一样强，但是由于公司的默认安全设置，在欧洲使用较少。我建议尝试不同种类的算法，只是为了好玩！