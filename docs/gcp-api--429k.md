# GCP API 身份验证设置

> 原文：<https://dev.to/seizans/gcp-api--429k>

用于使用 GCP (Google Cloud Platform )的 API 的认证认可为 OAuth 2.0，
在服务器上的使用方法为[using oauth 2.0 for server to server applications](https://developers.google.com/identity/protocols/OAuth2ServiceAccount)
结论用[Google application default credentials ( ADC )](https://developers.google.com/identity/protocols/application-default-credentials)，获取 OAuth 2.0 Access Token 后使用。

ADC は AWS での默认凭据提供者链にあたる。

## 什么是 ADC

通过以下逻辑获得的 credentials 被称为 ADC。

1.  如果指定了环境变量`GOOGLE_APPLICATION_CREDENTIALS`，则将该文件作为 service account 的 credentials 来读取使用
2.  执行`gcloud auth application-default login`并制作完成 well_known_file 的话，将其作为 authorized user 的 credentials 读取使用
3.  如果在 GAE (Google App Engine )上运行，则使用其构建服务帐户
4.  如果在 GCE (谷歌计算引擎)上运行，则使用其构建插件
5.  如果没有符合到此为止，则视为错误

## 知名 _ 文件 _について

```
$ gcloud auth application-default login 
```

Enter fullscreen mode Exit fullscreen mode

运行时创建。
如果是默认，则为`~/.config/gcloud/application_default_credentials.json`
如果指定了环境变量`CLOUDSDK_CONFIG`，则为`$CLOUDSDK_CONFIG/application_default_credentials.json`

## 获取访问令牌

使用 service account 或授权用户身份验证时，
创建相应的请求并在令牌端点上进行 POST。
GCE 的情况下获取至元数据服务器。

## 使用访问令牌敲击 API

将获取的访问令牌以 Bearer 格式插入授权标头中。
`Authorization: Bearer xxxx.yyyyyyyyyyyyy`

## 使用时

[谷歌优先度高的语言](https://developers.google.com/identity/protocols/OAuth2WebServer#libraries)备有官方库，看手册使用即可。
Elixir 是因为没有库而制作的。
[https://github.com/AK tsk/GCP _ auth](https://github.com/aktsk/gcp_auth)