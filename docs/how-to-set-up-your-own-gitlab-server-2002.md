# 如何设置自己的 GitLab 服务器

> 原文：<https://dev.to/helyo-world/how-to-set-up-your-own-gitlab-server-2002>

如今开发者不能忽视的一点就是 Git。那么为什么不用自己的服务器呢？

**注**:这是本系列的第一篇文章，旨在帮助你用 GitLab、Docker 和其他一些工具建立一个完整的开发环境。

* * *

# 总结

*   但是等等…我为什么要用自己的 Git 服务器？
*   好吧，我被说服了。我需要什么？
*   所有支票都是绿色的。我如何安装它？
*   太好了。那么我如何使用 nginx 作为代理呢？
    *   配置套接字以使用 GitLab 的主要功能
    *   在 nginx 中配置站点
    *   配置 GitLab 在 nginx 后面工作
*   下一步是什么？

* * *

# 但是等等…为什么要用自己的 Git 服务器？

这可能是你看到这个标题时想到的第一个问题。你这样问自己是完全正确的。

有很多理由说明拥有自己的 Git 服务器是有用的，但是请记住，这完全取决于您:如果在读了这篇文章的几行之后，您觉得不需要它，那么您可能不需要它。没关系。

无论如何，以下是我安装我自己的专用 Git 服务器的一些原因，我现在与 ric 分享:

*   (几乎)免费。你只需要为服务器本身付费
*   是**私人**。没有人可以访问你的回购和数据
*   是**自管**。你不依赖外部提供商(也许除了你的托管公司)
*   这很简单。几乎没有什么需要定期管理的，而且设置过程相当容易
*   已经**完成**了。GitLab 上已经有很多选项，你可以在 GitHub 或 Bitbucket 等其他提供商那里找到，比如:
    *   问题跟踪
    *   Webhooks
    *   团队和用户访问
    *   持续发展

* * *

# 好吧，我卖了。我需要什么？

首先，你需要有自己的服务器来托管 GitLab。

你还应该确保它足够强大，能够让 GitLab CE 工作。查看[页面](https://docs.gitlab.com/ce/install/requirements.html)了解所有要求。

这种情况下，我一直用的是 Ubuntu 16.04 系统，4 核 4 GB RAM，一堆存储。CPU 容量是足够的，尽管如果你想将同一台服务器用于其他事情( <abbr title="Continuous Integration">CI</abbr> ，Docker 部署，常规托管等)，RAM 有点不足。

同一个服务器已经用于其他项目，由 nginx 提供服务，所以我将重用它。

* * *

# 所有检查都是绿色的。我如何安装它？

好吧，没有比遵循[文件](https://about.gitlab.com/downloads/#ubuntu1604) :
更好的方法了

```
sudo apt install curl openssh-server ca-certificates postfix
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
sudo apt install gitlab-ce 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经有了一个闪亮的 GitLab 服务器，可以开始使用了。但是，如果在同一台机器上已经有了一个 web 服务器，您可能需要将其配置为重定向到 GitLab。

如果您的机器只有 GitLab，您可以在这里停下来，因为默认情况下 il 将在端口 80 上侦听。只要执行`sudo gitlab-ctl reconfigure`命令，你就可以开始了。

* * *

# 伟大。那么我如何使用 nginx 作为代理呢？

首先，检查您是否安装了乘客。如果没有，你应该把它和 nginx 一起安装，真的很有帮助。

然后，您可以在 nginx 目录中为您的站点创建一个文件。姑且称之为`gitlab.helyo.world`。

```
sudo nano /etc/nginx/sites-availables/gitlab.helyo.world 
```

Enter fullscreen mode Exit fullscreen mode

是的，我用 nano。不是维姆。不是 emacs。好老纳米。处理好它。

## 配置套接字以使用 GitLab

```
upstream gitlab-workhorse {
    server unix://var/opt/gitlab/gitlab-workhorse/socket fail_timeout=0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 在 nginx 中配置站点

```
server {
    listen 80;
    # listen [::]:80; # Uncomment if you use IPv6

    server_name gitlab.helyo.world;

    root /opt/gitlab/embedded/service/gitlab-rails/public;

    access_log /var/log/nginx/gitlab.helyo.world.access.log;
    error_log /var/log/nginx/gitlab.helyo.world.error.log;

    client_max_body_size 500m;

    passenger_ruby /opt/gitlab/embedded/bin/ruby;
    passenger_env_var PATH "/opt/gitlab/bin:/opt/gitlab/embedded/bin:/usr/local/bin:/usr/bin:/bin";

    passenger_user git;
    passenger_group git;

    passenger_enabled on;
    passenger_min_instances 1;

    location ~ ^/[\w\.-]+/[\w\.-]+/(info/refs|git-upload-pack|git-receive-pack)$ {
        # 'Error' 418 is a hack to re-use the @gitlab-workhorse block
        error_page 418 = @gitlab-workhorse;
        return 418;
    }

    location ~ ^/[\w\.-]+/[\w\.-]+/repository/archive {
        # 'Error' 418 is a hack to re-use the @gitlab-workhorse block
        error_page 418 = @gitlab-workhorse;
        return 418;
    }

    location ~ ^/api/v3/projects/.*/repository/archive {
        # 'Error' 418 is a hack to re-use the @gitlab-workhorse block
        error_page 418 = @gitlab-workhorse;
        return 418;
    }

    # Build artifacts should be submitted to this location
    location ~ ^/[\w\.-]+/[\w\.-]+/builds/download {
        client_max_body_size 0;
        # 'Error' 418 is a hack to re-use the @gitlab-workhorse block
        error_page 418 = @gitlab-workhorse;
        return 418;
    }

    location @gitlab-workhorse {
        ## https://github.com/gitlabhq/gitlabhq/issues/694
        ## Some requests take more than 30 seconds.
        proxy_read_timeout 3600;
        proxy_connect_timeout   300;
        proxy_redirect off;

        # Do not buffer Git HTTP responses
        proxy_buffering off;

        proxy_set_header    Host $http_host;
        proxy_set_header    X-Real-IP $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    X-Forwarded-Proto $scheme;

        proxy_pass http://gitlab-workhorse;

        ## The following settings only work with NGINX 1.7.11 or newer
        #
        ## Pass chunked request bodies to gitlab-workhorse as-is
        # proxy_request_buffering off;
        # proxy_http_version 1.1;
    }

    ## Enable gzip compression as per rails guide:
    ## http://guides.rubyonrails.org/asset_pipeline.html#gzip-compression
    ## WARNING: If you are using relative urls remove the block below
    ## See config/application.rb under "Relative url support" for the list of
    ## other files that need to be changed for relative url support
    location ~ ^/(assets)/ {
        root /opt/gitlab/embedded/service/gitlab-rails/public;
        gzip_static on; # to serve pre-gzipped version
        expires max;
        add_header Cache-Control public;
    }

    error_page 404 /404.html;
    error_page 422 /422.html;
    error_page 500 /500.html;
    error_page 502 /502.html;
    error_page 503 /503.html;

    location ~ ^/(404|422|500|502|503)\.html$ {
        root /opt/gitlab/embedded/service/gitlab-rails/public;
        internal;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 配置 GitLab 在 nginx 后面工作

您需要打开`/etc/gitlab/gitlab.rb`文件来更改几行:

```
external_url 'http://gitlab.helyo.world'
gitlab_rails['internal_api_url'] = 'http://gitlab.helyo.world/' 
```

Enter fullscreen mode Exit fullscreen mode

```
unicorn['enable'] = true; 
```

Enter fullscreen mode Exit fullscreen mode

```
web_server['external_users'] = ['www-data'] 
```

Enter fullscreen mode Exit fullscreen mode

```
nginx['enable'] = false 
```

Enter fullscreen mode Exit fullscreen mode

然后告诉 GitLab 应用这些更改:

```
sudo gitlab-ctl reconfigure 
```

Enter fullscreen mode Exit fullscreen mode

你应该可以走了！您现在可以访问您的域名并开始使用。您将被要求在登录之前配置 *root* 帐户的密码。

* * *

# 接下来是什么？

嗯，如果你想有一个安全的环境，你应该考虑转到 HTTPS。所以我们很快就会看到如何做到这一点！