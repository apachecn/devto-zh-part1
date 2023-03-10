# 在 Xcode 中将钥匙串与 iOS ARC 应用程序集成

> 原文：<https://dev.to/christianpatrick/integrating-the-keychain-with-an-ios-arc-app-in-xcode-1665>

对于我最近开发的一个 iOS 应用程序，需要集成 Keychain 来本地保存登录凭证。

在网上搜索和摸索了一会儿后，我发现了一个很棒的库:SFHFKeychainUtils，最初由 Buzz Andersen 创建。当我知道我不用再手动学习另一个完整的框架时，我非常兴奋，直到我注意到我所学的教程实际上都不起作用！

这是我对集成 SFHFKeychainUtils 的看法。对于这个演示，如果您打算在 UIWebView 中使用它，我将简单地用 Javascript 演示一下。

首先，您需要下载 SFHFKeychainUtils 并将其导入 Xcode 项目。我使用的支持 ARC 的版本在这里:[https://github.com/stoneros/SFHFKeychainUtils](https://github.com/stoneros/SFHFKeychainUtils)

然后，您可以将 Security.framework 添加到项目中。只需转到您的目标应用程序并选择构建阶段。在“将二进制文件与库链接”下，添加 Security.framework 并确保它是必需的。

最后，在 YourViewController.m 中，向这些库添加依赖项:

```
#import <Security/Security.h>
#import "SFHFKeychainUtils.h" 
```

Enter fullscreen mode Exit fullscreen mode

现在是好东西！让我们让钥匙链开始工作吧！

通常，您只是要寻找一个表单提交来获取凭证，所以这里有一个简单的方法。

```
- (bool)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType; {

    //save form data
    if(navigationType == UIWebViewNavigationTypeFormSubmitted) {
        NSURL *requestURL = [self.webView.request URL];

        if ([requestURL.path isEqualToString:@"YOURINTERNALSUBMISSIONURL"]) {
            //grab the data from the page
            NSString *username = [self.webView stringByEvaluatingJavaScriptFromString: @"document.getElementById('username').value"];
            NSString *password = [self.webView stringByEvaluatingJavaScriptFromString: @"document.getElementById('password').value"];
            //store values locally
            [[NSUserDefaults standardUserDefaults] setObject:username forKey:@"username"];
            [SFHFKeychainUtils storeUsername:username andPassword:password forServiceName:@"YOURAPPNAMESPACE" updateExisting:YES error:nil];
        }
        else {
            return YES;
        }
    }
    return YES;
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，只要你的应用点击 SUBMISSIONURL，它就会将用户名和密码这两个 NSString 变量保存到应用名称下的钥匙串中。真正酷的是这些 NSStrings 是通过 Javascript 从文本输入中获取的。不客气；)

接下来，在您的 webViewDidFinishLoad 部分，我们需要添加一些侦听器，以便在下次您的用户需要登录时自动填充保存的 keychain。

```
- (void)webViewDidFinishLoad:(UIWebView *)webView{

    //verify view is on the login page of the site (simplified)
    NSURL *requestURL = [self.webView.request URL];
    if ([requestURL.path isEqualToString:@"YOURINTERNALSUBMISSIONURL"]) {

        //check for stored login credentials
        NSString *username = [[NSUserDefaults standardUserDefaults] objectForKey:@"username"];

        if (username.length != 0 ) {

        //create js strings
        NSString *loadUsernameJS = [NSString stringWithFormat:@"document.getElementById('username').value ='%@'", username];
        NSString *password = [SFHFKeychainUtils getPasswordForUsername: username andServiceName:@"YOURAPPNAMESPACE" error:nil];
        if (password.length == 0 ) password = @"";
        NSString *loadPasswordJS = [NSString stringWithFormat:@"document.getElementById('password').value ='%@'", password];

        //autofill the form
        [self.webView stringByEvaluatingJavaScriptFromString: loadUsernameJS];
        [self.webView stringByEvaluatingJavaScriptFromString: loadPasswordJS];

        }
    }   
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当应用程序登陆登录页面时，它会检查值是否存储在钥匙串中，然后使用 Javascript 预先填充它们。当然，您可以更进一步，自动提交表单等。

这只是给你总体的基础知识，如果对你有帮助或者你有任何问题，请在下面给我留言！