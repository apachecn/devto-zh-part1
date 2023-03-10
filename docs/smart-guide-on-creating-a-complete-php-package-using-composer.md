# 使用 Composer 创建完整 PHP 包的智能指南

> 原文：<https://dev.to/ahmedkhan/smart-guide-on-creating-a-complete-php-package-using-composer>

Composer 是一个 PHP 依赖管理器，由 Nils Adermann、T2 和其他社区贡献者创建。使用 Composer，开发人员可以开发包含来自几个库和包的组件的代码。Composer 会自动安装所有必需的库和包，因此代码不会因为缺少必需的代码组件而中断。

Composer 的另一个重要用途是创建一个自包含的包，其中包含所有必需的代码组件。因此，开发人员不必从几个位置和资源中提取代码，而是必须包含一个包，并且只关注逻辑的实现，而不必担心外部代码资源。

Composer 最常见的用法是以下安装特定软件包的单一命令:

`$ composer install 'packagename:version'`

## 使用 Composer 创建包

Composer 还用于从开源包和/或[私有包](https://packagist.com/)创建定制包。然后可以使用上面提到的命令安装这些定制包。在本文中，我将通过为 Twitter 搜索 API 创建一个包来演示 Composer 的包创建功能。然后我会[注册它](http://packagist.org)，然后安装在我的服务器上。

开始之前，请确保 Composer 安装在本地系统上。

### Git 回购创建

第一步是创建一个公共 Git repo。转到 GitHub 帐户，用。gitignore 文件。接下来，将以下文件夹和文件添加到。gitignore 文件。(在某些情况下，这些项目是自动生成的)

```
composer.phar
/vendor/ 
```

创建存储库后，将其克隆到本地系统。下一步是创建一个基于 Twitter API 的 **TwitterSearch** 类。

## Twitter 搜索类

我将把 Twitter PHP-SDK 基于 PSR-4 命名空间。在开始创建类之前，在本地 Git repository 文件夹中运行以下命令来安装 Guzzle HTTP 客户端，以便处理 Twitter 请求和响应。

`$composer require "guzzlehttp/guzzle:^6.2";`

在本地 Git 文件夹中创建一个新文件夹，并将其命名为 **Twitter** 。接下来，在这个文件夹中，创建一个新文件，命名为 Base.php**。在代码编辑器中打开该文件，并向其中添加以下代码:**

```
namespace Twitter;
use GuzzleHttp\Client;
use GuzzleHttp\Exception\RequestException;
use GuzzleHttp\Psr7\Request;

/**
*
*/
class Base
{

    const API_URL = "https://api.twitter.com/1.1";

    protected $client;
    protected $token;
    protected $tokensecret;
    protected $accesstoken;
    public function __construct()
    {
        $this->client = new \GuzzleHttp\Client();
    }

    public function setToken($token,$secret)
    {
        $this->token = $token;
        $this->tokensecret = $secret;
    }

    protected function prepareAccessToken()
    {
        try{
            $url = "https://api.twitter.com/oauth2/token";
            $value = ['grant_type' => "client_credentials"
            ];
            $header = array('Authorization'=>'Basic ' .base64_encode($this->token.":".$this->tokensecret),
            "Content-Type"=>"application/x-www-form-urlencoded;charset=UTF-8");
            $response = $this->client->post($url, ['query' => $value,'headers' => $header]);
            $result = json_decode($response->getBody()->getContents());

            $this->accesstoken = $result->access_token;
        }
        catch (RequestException $e) {
            $response = $this->statusCodeHandling($e);
            return $response;
        }
    }
    protected function callTwitteAPIr($method,$request,$post = [])
    {
        try{
            $this->prepareAccessToken();
            $url = self::API_URL . $request;
            $header = array('Authorization'=>'Bearer ' . $this->accesstoken);
            $response = $this->client->request($method,$url, array('query' => $post,'headers' => $header));
            return json_decode($response->getBody()->getContents());
        } catch (RequestException $e) {
            $response = $this->StatusCodeHandling($e);
            return $response;
        }
    }
    protected function statusCodeHandling($e)
    {
        $response = array("statuscode" => $e->getResponse()->getStatusCode(),
        "error" => json_decode($e->getResponse()->getBody(true)->getContents()));
        return $response;
    }
} 
```

这是负责为 Twitter API 和 API 相关请求生成访问令牌的基类。

我现在将解释这个类的一些方面。

首先我初始化了名称空间，然后是 Guzzle 类。接下来，我为 Twitter API URL 创建了一个常量、各种变量和一个调用 **Guzzle\Client** 的构造函数。

```
const API_URL = "https://api.twitter.com/1.1";

    protected $client;
    protected $token;
    protected $tokensecret;
    protected $accesstoken;
    public function __construct()
    {
        $this->client = new \GuzzleHttp\Client();
    } 
```

接下来，我创建了准备访问令牌和调用用户生成的 Twitter API 请求的函数。

```
public function setToken($token,$secret)
    {
        $this->token = $token;
        $this->tokensecret = $secret;
    } 
```

接下来，我创建了准备访问令牌和调用用户生成的 Twitter API 请求的函数。

```
 protected function prepareAccessToken()
    {
        try{
            $url = "https://api.twitter.com/oauth2/token";
            $value = ['grant_type' => "client_credentials"
            ];
            $header = array('Authorization'=>'Basic ' .base64_encode($this->token.":".$this->tokensecret),
            "Content-Type"=>"application/x-www-form-urlencoded;charset=UTF-8");
            $response = $this->client->post($url, ['query' => $value,'headers' => $header]);
            $result = json_decode($response->getBody()->getContents());

            $this->accesstoken = $result->access_token;
        }
        catch (RequestException $e) {
            $response = $this->statusCodeHandling($e);
            return $response;
        }
    }
    protected function callTwitter($method,$request,$post = [])
    {
        try{
            $this->prepareAccessToken();
            $url = self::API_URL . $request;
            $header = array('Authorization'=>'Bearer ' . $this->accesstoken);
            $response = $this->client->request($method,$url, array('query' => $post,'headers' => $header));
            return json_decode($response->getBody()->getContents());
        } catch (RequestException $e) {
            $response = $this->StatusCodeHandling($e);
            return $response;
        }
    }
    protected function statusCodeHandling($e)
    {
        $response = array("statuscode" => $e->getResponse()->getStatusCode(),
        "error" => json_decode($e->getResponse()->getBody(true)->getContents()));
        return $response;
    } 
```

此外，我还创建了一个处理错误代码的函数。

现在在本地 Git repo 文件夹中创建一个新文件夹，并将其命名为 **Search** 。在这个文件夹中创建一个新文件，命名为【Search.php**并粘贴以下代码:**

```
namespace Twitter\Search;
/**
 * 
 */
class Search extends \Twitter\Base
{

    public function search($value)
    {
        try{
            $url = "/search/tweets.json";
            $response = $this->callTwitter("get",$url,$value);
            return $response;
        } catch (RequestException $e) {
            $response = $this->StatusCodeHandling($e);
            return $response;
        }
    }    
} 
```

让我详细解释一下代码。

首先，我给了这个类一个名称空间，然后将它扩展到基类**。接下来，我创建了一个函数 **Search()** ，通过 [Twitter 搜索 API](https://dev.twitter.com/rest/public/search) 执行搜索。 **Search()** 接受变量 **$value** ，该变量包含 Twitter API 中定义的搜索查询的参数。**

```
public function search($value)
    {
        try{
            $url = "/search/tweets.json";
            $response = $this->callTwitter("get",$url,$value);
            return $response;
        } catch (RequestException $e) {
            $response = $this->StatusCodeHandling($e);
            return $response;
        }
    } 
```

接下来，我将在 **composer.json** 文件中添加名称空间，它将包含在【autoload.php】的**中**。打开 **composer.json** 文件，添加以下几行:

```
,  "autoload":  {  "psr-4":  {  "Twitter\\"  :  "Twitter/"  }  } 
```

新的 **composer.json** 文件将如下所示:

```
{  "require":  {  "guzzlehttp/guzzle":  "^6.2"  },  "autoload":  {  "psr-4":  {  "Twitter\\"  :  "Twitter/"  }  }  } 
```

现在运行下面的命令。

`$ composer dump-autoload`

### 获取钥匙

下一步是获取 Twitter API 的消费者密钥和消费者秘密密钥。

要获取密钥，请登录[https://apps.twitter.com/](https://apps.twitter.com/)。创建一个新的应用程序以获取消费者密钥。

### 测试项目

现在我已经拥有了项目的所有必要组件，我现在可以测试它们以确保一切正常工作。

在 Git 文件夹的根目录下，创建一个名为**index.php、**的新文件，并在其中粘贴以下代码:

```
include "vendor/autoload.php";

use Twitter\Search\Search;

$search = new Search();
$search->setTokken("afmowdkHQTZVZWMxx0dh5Bz4r","fuJ4HNNTkI27qZEFdv3g4MEMXdVoZHUIVXCwmDbqXBRWMrEQAU");
$value = ["q" => "ahmed khan"];
echo "<pre>";
print_r($search->search($value));
echo "</pre>"; 
```

现在在本地主机上运行**index.php**。如果你得到了结果数组，事情就正常了！

现在这个项目已经完成并且运行良好，我现在将把它转换成一个 Composer 包。

## 创建作曲家包

打开 **composer.json** ，将代码替换为以下内容:

```
{  "name":  "ahmed.khan/twittersdkphp",  //naming  our  package  "description":  "A PHP-SDK for Twitter Search API",  //  Description  about  package  "keywords":  ["twitter search",  "search"],  //  Keywords  "type":  "package",  //Since  it's  a  package  we  define  its  type  package  "require":  {  "guzzlehttp/guzzle":  "^6.2"  },  "license":  "MIT",  "authors":  [{  //Your  information  "name":  "Ahmed Khan",  "email":  "ahmedkhan_847@hotmail.com"  }],  "autoload":  {  "psr-4":  {  "Twitter\\":  "Twitter/"  }  },  "version":  "1.0.0",  //Version  of  the  package  "minimum-stability":  "dev"  //Stability  } 
```

所有的模式都可以在这里找到。现在将所有代码推送到 Git 存储库。完成后，去 https://packagist.org/的。登录 GitHub，然后点击顶部菜单中的**提交**。粘贴 Git 存储库的 URL 并点击 **Check** 按钮。

等待验证过程完成，然后点击**提交**按钮。

完成后，将出现以下消息:

***【打包程序不自动更新】***

为了解决这个问题，请访问 GitHub 帐户，并访问相关存储库的设置页面。点击**集成&服务>添加服务，**找到并点击**包装商。**

可能会提示您输入 GitHub 密码。在下一个窗口中，添加您的 Packagist 用户名和令牌。勾选**活动**框，然后点击**添加服务**按钮。

一旦服务被添加，点击**测试服务**按钮。

现在刷新页面，注意错误已经消失了！

既然已经创建了包，现在我将测试它！

## 测试作曲包

现在，在单独的机器或服务器上，运行以下命令通过 Composer 安装软件包。

`$ composer require "ahmedkhan847/twittersdkphp:dev-master"`

您会注意到软件包正在安装。

## 结论

在本教程中，我基于 Twitter 搜索 API 创建了一个 Composer 包。我还将这个包提交给 Packagist，然后通过 Composer 安装这个包来测试它。你可以在 [Github](https://github.com/ahmedkhan847/twittersearchphpsdk/) 和[包装商](https://packagist.org/packages/ahmedkhan847/twittersdkphp)上找到这个完整的包。如果您在此过程中遇到任何问题，请在下面留下您的评论。****