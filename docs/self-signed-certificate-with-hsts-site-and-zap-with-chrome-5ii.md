# HSTS 站点的自签名证书和 Chrome 的 ZAP

> 原文：<https://dev.to/ech0server/self-signed-certificate-with-hsts-site-and-zap-with-chrome-5ii>

如果你碰巧在使用 Chrome 和访问支持 Windows HSTS 的网站时遇到 OWASP ZAP 的问题？只需遵循以下步骤:

1.  在 ZAP 中启用不安全的重新协商
2.  为您的 Java 版本安装 Java 加密扩展(JCE)。这里是 Java 8 的链接

    *   下载并解压缩文件。
    *   从归档文件中提取 **jce\local_policy.jar** 和 **jce\US_export_policy.jar** 到文件夹**% JAVA _ HOME % \ JRE \ lib \ security**，覆盖目录中已经存在的文件。
3.  在 Chrome 选项中禁用安全性。

    *   前往 chrome://设置
    *   点击`Protect you and your device from dangerous sites`将其禁用。