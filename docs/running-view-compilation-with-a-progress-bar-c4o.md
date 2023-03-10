# 使用进度条运行视图编译

> 原文：<https://dev.to/aggieben/running-view-compilation-with-a-progress-bar-c4o>

我遇到了一个在 CI 上视图编译失败的构建问题，但是我没有发现它，因为视图通常不会在开发人员构建中预编译。以下是我写的无需手动检查的内容:

```
$webdirs = gci -rec -Directory -Exclude "*node_modules*" `
  |? { ((Test-Path "$($_.FullName)\*.csproj") -eq $True) `
       -and ((Test-Path "$($_.FullName)\web.config") -eq $True) }

$webdirs |% {$idx = 0}{$pct = $idx/$projDirs.Length; `
  Write-Progress -Activity "Building Views" `
                 -Status ("{0:P}" -f $pct) `
                 -PercentComplete ($pct*100) `
                 -CurrentOperation "aspnet_compiler -v temp -p $($_.FullName)"; `
  aspnet_compiler -v temp -p $_.FullName; $idx++} 
```