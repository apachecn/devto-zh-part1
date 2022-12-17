# IE 11 中的 YouTube API 有问题

> 原文：<https://dev.to/livestrong2109/having-trouble-with-the-youtube-api-in-ie-11-7fh>

我希望你们中的一个人可能知道为什么我的脚本在 IE 11 中不工作。一切都可以在 Chrome 和 Firefox 上运行，部分可以在 IE 上运行。不过问题是 IE 似乎不喜欢我调用的一些函数。IE 控制台-(“对象不支持此属性或方法‘pauseVideo’”)。我无法逃避这种感觉，我没有看到一些语法错误。

页面显示错误-[http://go forward . Harper college . edu/dev-foundation/index-3 . PHP](http://goforward.harpercollege.edu/dev-foundation/index-3.php)

```
<script type="text/javascript" src="https://www.youtube.com/iframe_api"></script>

<script type="text/javascript">
window.onYouTubeIframeAPIReady = function() {
for (item in players_list) {
players[players_list[item]] = new YT.Player(players_list[item], {});
}
}
var tag = document.createElement('script');
tag.src = "https://www.youtube.com/iframe_api";
var firstScriptTag = document.getElementsByTagName('script')[0];
firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);
var players = new Array();
var players_list = [
"popup-youtube-player-1",
"popup-youtube-player-2",
"popup-youtube-player-3",
"popup-youtube-player-4",
"popup-youtube-player-5",
"popup-youtube-player-6"
];

function pauseVideo() {
for (item in players_list) {
players[players_list[item]].pauseVideo();
}
}

function swapvid(id) {
pauseVideo();
$('.video-slider').css('display', 'none');
$('#' + id).closest('.video-slider').css('display', 'inline');  
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode