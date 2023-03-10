# 使用 FtpWebRequest 快速轻松地上传文件

> 原文：<https://dev.to/adamkdean/quickly-and-easily-upload-files-with-ftpwebrequest-1a21>

我刚刚把工作中的*系统*移植到一个新的主机上，这几乎是。NET 2.0 的东西，其中很多是一些上传图像到服务器的代码，由于一些奇怪的原因，3 年后决定停止工作，开始上传天书。

我决定绕过旧的笨重的 FTP 类，只使用`FtpWebRequest`代替，并认为我应该在这里张贴它是多么容易(和未来的参考！哈)。

这里有四个输入变量:`requestUriString`、`filePath`、`username`和`password`。

`requestUriString`应该采用`ftp://10.10.10.10/path/filename.jpg`的格式，其中 IP 是您的 FTP 主机名。此 FTP 请求的根目录将是您的 FTP 用户帐户的根目录。如果您想将图像上传到一个目录，请在 Uri 中包含该路径。最后，将所需的文件名放在请求 Uri 的末尾。

`filePath`应该是你本地文件的路径，这是文件所使用的。ReadAllBytes(...)，并且仅用于从本地机器获取数据。

`username`和`password`是您的 FTP 登录凭证。

下面是一个如何使用`FtpWebRequest`快速轻松上传文件的例子。

```
public static bool UploadExample(string requestUriString, string filePath,
    string username, string password)
{
    bool success = true;

    try
    {
        var request = (FtpWebRequest)WebRequest.Create(requestUriString);
        request.Method = WebRequestMethods.Ftp.UploadFile;
        request.Credentials = new NetworkCredential(username, password);

        // we read the bytes directly, not with a StreamReader
        var fileContents = File.ReadAllBytes(filePath);
        request.ContentLength = fileContents.Length;

        var requestStream = request.GetRequestStream();
        requestStream.Write(fileContents, 0, fileContents.Length);
        requestStream.Close();

        // we don't need to use the return type
        // unless we want to make checks
        request.GetResponse();
    }
    catch (WebException)
    {
        success = false;
    }

    return success;
} 
```

Enter fullscreen mode Exit fullscreen mode

比自己使用/编写一个笨重的 FTP 类容易多了，不是吗？