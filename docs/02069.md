# 使用引导程序包

> 原文：<https://dev.to/aggieben/using-bootstrapped-paket-d29>

我通过自举在我的几个项目中使用了 [Paket](https://fsprojects.github.io/Paket/) 包管理器，所以我在我的项目文件夹的根目录下找到了一个`.paket`目录，其中包含了我需要使用的`paket.exe`。我发现用像`../../../.paket/paket.exe`这样的相对路径调用它很麻烦，所以我写了一点 powershell 来确保引导的 paket 在我的路径中。

这是在 Cmder `$PrePrompt`函数中，但是如果你没有使用 Cmder，你可以在 Powershell 配置文件的`Prompt`函数中这样做(如果你还没有的话，你必须添加它)。更多信息见[关于提示](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_prompts?view=powershell-5.1)。

我为 Cmder 设置的`user-profile.ps1`的相关位:

```
Function Get-AncestorPath ($dir, $name) {
    $target = Get-ChildItem $dir |? { $_.Name -eq $name }
    if ($target -ne $Null) {
        return $target
    } elseif ($dir.Parent -ne $Null) {
        return Get-AncestorPath $dir.Parent, $name
    } else {
        return $Null
    }
}

[ScriptBlock]$PrePrompt = {
    $paketDir = Get-AncestorPath $pwd ".paket" | Select-Object -ExpandProperty FullName
    if ($paketDir -eq $Null) {
        if ($Env:Path -like "*.paket*") {
            Write-Verbose "Removing .paket path from `$Env:Path"
            $Env:Path = $Env:Path -split ';' `
                |? { $_ -notlike "*.paket*" } `
                | Join-String -Separator ';'
        }
        return
    }

    Write-Verbose "Paket path: $paketDir"

    if (($Env:Path -split ';') -notcontains $paketDir) {
        Write-Verbose "Adding '$paketDir' to path..."
        $Env:Path = "$paketDir;$($Env:Path)"
    }
} 
```