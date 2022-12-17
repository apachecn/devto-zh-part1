# 维姆&拉勒韦尔公司

> 原文：<https://dev.to/matsleal/vim--laravel-28m1>

大家好，我最近在 laravel 中开发了几个项目，作为一个铁杆 Vim 用户，我更喜欢 MacVim 方法，当我第一次开始使用 laravel 时，我决定设置一些 Vim 映射，以便将我的 Laravel 工作流集成到 Vim 中，到目前为止，我有以下具体的映射:

"- Laravel 特定映射-"
" routes/web . PHP 文件的快捷方式
nmap lr:tabedit routes/web . PHP

# "Larvel Comands

### "迁移表格

nmap lmi:！php 工匠迁移

# “创造者

### "自动完成制作控制器

nmap lmc:！php 工匠制作:控制器

### "自动完成制作模型

nmap lmm:！php 工匠制作:模型

### "创建迁移表

nmap lmmt:！php 工匠制作:迁移

# “发现者

### “直接在 app/Http/Controllers 中搜索控制器

nmap LFC:ctrl app/Http/Controllers/

### "搜索迁移文件

nmap lfdm:ctrl p 数据库/迁移/

### “搜索 app/下的数据模型

nmap lfm :CtrlPapp/

### "在资源/视图/中搜索视图

nmap lfv:ctrl 资源/视图/

### “搜索数据库加速程序

nmap lfs :CtrlPdatabase/seeds/

我这样做是因为我还没有看到很多 laravel 功能插件，我很乐意看到有人发现这种可怜的映射很有用，Vim 用户欢呼吧！！