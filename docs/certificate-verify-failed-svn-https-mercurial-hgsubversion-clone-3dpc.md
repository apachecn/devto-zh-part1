# 证书验证 failedなsvn+httpsサーバからmercurial+hgsubversionでcloneする

> 原文：<https://dev.to/shiena/certificate-verify-failed-svn-https-mercurial-hgsubversion-clone-3dpc>

因为 certificate verify failed 的 svn+https 服务器和 mercurial 的合作很难统一，所以总结了步骤。
根据环境的不同，可能会跳过中途的步骤，相反，进行到最后也可能无法解决。

首先正常地试着克隆一下。

```
$ hg clone https://example.org/svn/
abort: error: _ssl.c:504: error:14090086:SSL routines:SSL3_GET_SERVER_CERTIFICATE:certificate verify failed 
```

clone 如果原始 SSL 证书是奥利奥证书或过期，则会出现错误。
因此为了省略验证，使用`--insecure`选项。

```
$ hg clone --insecure https://example.org/svn/
warning: example.org certificate with fingerprint xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx not verified (check hostfingerprints or web.cacerts config setting)
abort: Failed to open Subversion repository; please try running 'svn ls https://example.org/svn/' for details. 
```

这次会在 svn 库中发生错误，所以按照错误信息执行命令。

```
$ svn ls https://example.org/svn/
Error validating server certificate for 'https://example.org:443':
 - The certificate is not issued by a trusted authority. Use the
   fingerprint to validate the certificate manually!
Certificate information:
 - Hostname: example.org
 - Valid: from Fri, 16 Sep 2011 22:55:11 GMT until Mon, 17 Sep 2012 16:58:51 GMT
 - Issuer: Foo
 - Fingerprint: xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx
(R)eject, accept (t)emporarily or accept (p)ermanently? 
```

在此输入`p`以始终接受 SSL 证书。
还有`--insecure`选项会出现大量的警告，所以在`hostfingerprints`中追加上述的`Fingerprint`。
**实际的 Fingerprint 是用:分隔的十六进制数。**

*   Windows 时为`%USERPROFILE%\Mercurial.ini`
*   Mac、UNIX 系的情况`$HOME/.hgrc`

```
[hostfingerprints]
example.org = xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx 
```

这样就避免了 SSL 证书的错误，所以再次尝试 clone。

```
$ hg clone https://example.org/svn/
destination directory: svn
abort: Failed to open Subversion repository; please try running 'svn ls https://example.org/svn/' for details. 
```

这里错误信息不变的情况下，由于 svn 库使用的 http 库有问题，所以在
`servers`文件的`global`部分的`http-library`中指定`serf`或`neon`。

*   Windows 时为`%APPDATA%\Subversion\servers`
*   Mac、UNIX 系的情况`$HOME/.subversion/servers`

```
[global]
http-library = serf 
```

或

```
[global]
http-library = neon 
```

因为更改了 http 库，所以再次接受 SSL 证书。

```
$ svn ls https://example.org/svn/
Error validating server certificate for 'https://example.org:443':
 - The certificate is not issued by a trusted authority. Use the
   fingerprint to validate the certificate manually!
Certificate information:
 - Hostname: example.org
 - Valid: from Fri, 16 Sep 2011 22:55:11 GMT until Mon, 17 Sep 2012 16:58:51 GMT
 - Issuer: Foo
 - Fingerprint: xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx
(R)eject, accept (t)emporarily or accept (p)ermanently? 
```

这次终于避免了 SSL 证书的错误，所以试着 clone 一下。

```
$ hg clone https://example.org/svn/
destination directory: svn
abort: The OPTIONS response did not include the requested version-name value (subversion error: 1703) 
```

这次用 HTTP 的 OPTIONS 方法出来了。
这种情况下 svn 的 branch 和 tag 不会反映出来，但是做成 single 布局的话可以进行 clone。

```
$ hg clone --layout single https://example.org/svn/
destination directory: svn 
```

这样就可以顺利地从 svn+https 服务器上克隆了。