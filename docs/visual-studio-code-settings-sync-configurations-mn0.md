# 代码设置同步配置

> 原文：<https://dev.to/shanalikhan/visual-studio-code-settings-sync-configurations-mn0>

就范围而言，设置同步有两种类型的配置。

*   要点设置
*   全局设置

设置同步全局设置是应用于所有 Visual Studio 代码环境的设置，而 Gist 设置的范围仅限于 Visual Studio 当前环境。

例如:GitHub 令牌是全局设置的一部分有两个原因，首先，它是用户的敏感信息，我们不允许将 GitHub 令牌和 Gist 中的代码设置一起上传。第二，它适用于所有要点，它需要下载任何要点。

#### 要点设置

```
{  "sync.gist":  "",  "sync.autoDownload":  false,  "sync.autoUpload":  false,  "sync.forceDownload":  false,  "sync.removeExtensions":  true,  "sync.syncExtensions":  true,  "sync.forceUpload":  false,  "sync.quietSync":  false  } 
```

Gist 设置以 sync 前缀存储在 Visual Studio 代码`settings.json`中，可能会因不同的 Gist ( Visual Studio 环境)而异，并将被新下载的设置替换。

*   *要点* : Github 要点 ID
*   *自动下载* : `false`默认。如果您将其设置为`true`，它将在代码启动时自动下载设置。
*   *自动上传* : `false`默认。设置为`true`，`false`不允许扩展在启动时自动下载设置。
*   *quietSync* : `false`默认。当下载或上传过程完成时，它将显示摘要页面，显示更改的文件和添加或删除的扩展名。设置为 false 将允许后台安静进程，并且只通过编辑器状态栏通知。
*   *强制下载* : `false`默认。如果您将其设置为`true`，它将在每次下载过程手动启动或启动时覆盖现有设置。
*   *syncExtensions* : `true`默认。它允许设置同步来同步 gist 中的扩展列表，所以当你下载它时，它会自动安装代码中的扩展列表。
*   *默认移除扩展* : `true`。当 syncExtensions 为`true`时，下载扩展列表。它允许设置同步删除那些不属于下载的扩展列表的扩展。如果你想同步新的扩展，但不想删除额外的扩展，请将此配置保留为`false`设置，同步不会删除扩展。

##### 全局设置

全局设置保存在文件名为`syncLocalSettings.json`的`User`文件夹中。

```
{  "ignoreUploadFiles":  [  "state.*",  "syncLocalSettings.json",  ".DS_Store",  "sync.lock",  "projects.json",  "projects_cache_vscode.json",  "projects_cache_git.json",  "projects_cache_svn.json",  "gpm_projects.json",  "gpm-recentItems.json"  ],  "ignoreUploadFolders":  [  "workspaceStorage"],  "ignoreExtensions":  [],  "gistDescription":  "Visual Studio Code Settings Sync Gist",  "version":  341,  "token":  "",  "downloadPublicGist":  false,  "supportedFileExtensions":  [  "json","code-snippets"],  "disableUpdateMessage":  false,  "lastUpload":  null,  "lastDownload":  null,  "githubEnterpriseUrl":  null,  "askGistName":  false,  "customFiles":  {},  "hostName":  null,  "universalKeybindings":  false,  "autoUploadDelay":  20  } 
```

*   *ignoreUploadFiles* :该键内的所有文件将停止扩展上传文件。你只需要写文件的文件名。在`User`文件夹或子文件夹中找到的任何同名文件都不会被上传。和上面的例子一样，projects.json 和 projects_cache_git.json 不会上传到 Github Gist。

*   *ignoreUploadFolders* :其中定义的所有文件夹名称都不会上传到 Gist 中，文件夹可以是用户文件夹的子文件夹，也可以是任何子文件夹的子文件夹。和上面的例子一样，`workspaceStorage`文件夹文件不会上传到 Github Gist。

*   *gistDescription* :这是您将要创建的 gist 的名称。当你有多种环境(例如家庭环境、工作环境)时，你可以说出这些要点并通过识别下载它们，这非常有用。

*   *token*:GitHub 用户秘密标识符，以允许设置同步上传和下载 gist。当您通过 GitHub 登录时，设置同步 UI 会自动生成令牌。您也可以从 Github 手动生成令牌并粘贴到这里。

*   *supportedFileExtensions* :允许设置同步只上传定义了扩展名的文件。默认设置下，同步仅上传`json`和`code-snippets`。

*   *downloadPublicGist* : `false`默认。如果您希望设置仅同步下载模式，请将其设置为`true`。例如，团队成员期待团队环境。设置为`true`，设置同步将只下载要点，永远不会要求令牌才能下载。

*   *ignoreExtensions* :添加任何扩展名以便从上传/下载过程中忽略。比如:想忽略`beautify`扩展名从 Gist 上传下载就添加`beautify`。

*   *禁用更新消息* : `false`默认。当您希望禁用扩展更新消息时，将其设置为`true`。

*   *githubEnterpriseUrl* 和*主机名*:当你想使用 Github enterprise 进行设置同步时，设置企业 Url 和主机名。

*   *askGistName*:-*askGistName*:`false`默认。当设置为`true`时，允许用户在创建新名称时设置要点名称。当你有多种环境(例如家庭环境、工作环境)时，你可以说出这些要点并通过识别下载它们，这非常有用。

*   *universalKeybindings*:`false`默认。如果你想要 MacOS、Linux 和 Windows 的单个`keybindings.json`,把它设置为`true`。

*   *自动上传延迟*:默认为`20`秒。更改秒间隔以允许设置同步在更改时上传设置。

*   *自定义文件*:允许你同步`User`文件夹外的文件。详情请阅读维基帖子[这里](https://github.com/shanalikhan/code-settings-sync/wiki/Custom-Sync)

*lastDownload* 和 *lastUpload* 只是记录下载和上传的状态。您可以将它设置为空来命中手动下载过程。

如果你有任何问题让我知道。