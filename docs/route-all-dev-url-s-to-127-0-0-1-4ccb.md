# 路由所有。到 127.0.0.1 的开发 URL

> 原文：<https://dev.to/scottrobertson/route-all-dev-url-s-to-127-0-0-1-4ccb>

如果您像我一样，当您想要查看本地站点时，您总是忘记编辑/etc/hosts 文件。

为了解决这个问题，您可以使用下面的脚本来路由您的所有。将 URL 开发到 127.0.0.1(或任何其他 IP)。所以你可以去“romhut.dev ”,它会从 127.0.0.1 加载。

```
brew install dnsmasq
mkdir -pv $(brew --prefix)/etc/
echo 'address=/.dev/127.0.0.1' > $(brew --prefix)/etc/dnsmasq.conf
sudo cp -v $(brew --prefix dnsmasq)/homebrew.mxcl.dnsmasq.plist /Library/LaunchDaemons
sudo launchctl load -w /Library/LaunchDaemons/homebrew.mxcl.dnsmasq.plist
sudo mkdir -v /etc/resolver
sudo bash -c 'echo "nameserver 127.0.0.1" > /etc/resolver/dev' 
```

Enter fullscreen mode Exit fullscreen mode

如果您希望使用另一个 IP，只需更改第一个 127.0.0.1，否则它会在主机上查找名称服务器。