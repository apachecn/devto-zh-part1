# 用 PowerShell 升级 Razor 文件中的 C#

> 原文：<https://dev.to/aggieben/upgrading-c-in-your-razor-files-with-powershell-9ga>

```
git ls-files | sls web.config ` # get a list of web.config files here, but not with Get-ChildItem
  |% { (gc $_) -replace "/langversion:default","/langversion:latest" ` 
  | sc -Path $_ -Encoding UTF8 } 
```