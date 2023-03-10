# 将您的 Angular 应用程序归档

> 原文：<https://dev.to/prestonjlamb/dockerizing-your-angular-app-j90>

使用 CLI 构建 Angular 应用程序从未如此简单。生成组件、服务和模块非常容易。对您的代码进行生产构建也非常容易。但对我来说不容易的是采取下一步，使用 Docker 实际部署应用程序。

大约一个月前，我为了工作需要这样做。我花了一些时间了解了更多关于 Docker 的知识，在此期间，我了解了 Docker 映像的多阶段构建。(旁注:他们太神奇了)。所以在这篇文章的剩余部分，我将解释为部署而封装你的应用程序的过程。

让我们从一个新项目开始。安装最新版本的 Angular CLI 后，运行以下命令:

```
ng new my-docker-app 
```

Enter fullscreen mode Exit fullscreen mode

移动到新目录，并运行

```
ng serve -o 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，您的应用程序是在本地构建和服务的。如果您进行了更改，浏览器将自动重新加载并反映您所做的更改。好吧，这没什么新鲜的，但现在我们达成共识了。所以让我们转到实际的码头工人工作。我们将从创建一个`Dockerfile`开始，在这里我们可以定义建立我们形象的步骤。我们也将使用我之前提到的多阶段构建。您的 docker 文件将如下所示:

```
FROM johnpapa/angular-cli as angular-built
WORKDIR /usr/src/app
COPY package.json package.json
RUN npm install --silent
COPY . .
RUN ng build --prod

FROM nginx:alpine
LABEL author="Preston Lamb"
COPY --from=angular-built /usr/src/app/dist /usr/share/nginx/html
EXPOSE 80 443
CMD ["nginx", "-g", "daemon off;"] 
```

Enter fullscreen mode Exit fullscreen mode

所以为了分解这里发生的事情，让我们看一下第一个指令块。第一部分使用了由[约翰·帕帕](https://twitter.com/John_Papa)制作的图像，其中融入了棱角分明的 CLI。它为映像设置工作目录，将 package.json 复制到中，安装所有依赖项，然后将其余的应用程序文件复制到映像中。然后它用`RUN ng build --prod`构建 app。但是多阶段构建的重要部分在第一行:`FROM johnpapa/angular-cli as angular-built`。

该部分将用于我们多阶段构建的下一步。所以让我们进入下一步。这一次我们使用 nginx 映像，当我们构建的映像在容器中运行时，它将服务于我们的应用程序。正如您在上面的步骤中看到的，还有另一个`COPY`命令。但是这一次，我们不是将文件从我们所在的目录复制到映像中，而是将它从`--from=angular-built /usr/src/app/dist`复制到`/usr/share/nginx/html`。显然，我们正在将第一步中构建的代码移动到基于 nginx 的映像中，这将是我们的最终映像。最后，我们公开一个端口，然后给出当图像在容器中运行时将运行的命令。

现在我们的 docker 文件已经完成，我们可以构建图像了。您可以使用下面的命令来完成这个任务:

```
docker build -t my-docker-app:latest . 
```

Enter fullscreen mode Exit fullscreen mode

这告诉 docker 构建一个映像，给它一个 my-docker-app:latest 标记，并在当前目录的上下文中构建它。完成后，您可以通过输入
看到您构建的图像

```
docker images 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备运行 docker 容器。您可以使用下面的命令来完成这个任务:

```
docker run -d --port 8080:80 --name my-docker-app my-docker-app:latest 
```

Enter fullscreen mode Exit fullscreen mode

这个命令将容器内部的端口 80 映射到主机上的 8080，将容器命名为 my-docker-app，并使用我们刚刚构建的 my-docker-app 映像。

现在，如果我们要将它部署在服务器上，我们真的希望将我们构建的映像推送到一个注册表中，我们可以从该注册表中提取我们服务器上的映像。此外，容器中的 nginx 服务器需要一个配置来重定向所有到达应用程序 index.html 文件的请求，因为 Angular 实际上在处理路由。

希望这对你有所帮助！我在这个过程中学到了很多，希望这对你和其他人来说是一个很好的资源。谢谢！