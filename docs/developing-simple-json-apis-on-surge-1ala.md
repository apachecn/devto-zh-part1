# 在 Surge 上开发简单的 JSON APIs

> 原文：<https://dev.to/brandonb927/developing-simple-json-apis-on-surge-1ala>

今天早上早些时候，我阅读了另一位 Surge 用户 Raymond Camden 撰写的一篇文章，内容是关于如何利用 Jekyll 构建 JSON 文件来构建基本的只读 JSON APIs。我突然想到，我最近在自己的网站上用[签到地图页面](https://brandonb.io/checkins)实现了这一点，这激发了我开始写这方面的东西！

让我先声明一下，这篇文章的标题也有点复杂，我说的`API`只是指`CRUD (Create Read Update Delete)`模型的`Read`部分。这个站点托管在 [Surge](https://surge.sh) 上，这是一个静态站点托管平台。由于 Surge 和静态站点的性质，没有“后端”来利用脚本语言，所以我们只能想办法创作只读 JSON APIs。这就是 Surge 在强大的 CDN 上大规模分发的地方，它在提供缓存和易于使用的 JSON 文件方面大放异彩。考虑到所有这些，从理论上讲，我们可以构建一个由自动化部署脚本支持的静态 API，并且它在某种程度上是相当可伸缩的。

Raymond 谈到用 Jekyll 中的`{% for … %}`循环或者用`_data`文件夹中的 JSON 文件来构建 JSON。这些是 Jekyll 特有的方法，而且无论如何都是一种完全合法的方法。当实现我的“API”时，我从一个稍微不同的角度开始，因为我涉及到了*另一个*第三方 API 来支持我自己的 API。我不可避免地做出的决定是将我的“API”构建为一个吞咽任务，这将是我构建/部署过程的一部分，然后利用 Foursquare API 来支持我自己的“API”预部署。

最初，当我决定开始这个项目时，我想在浏览器的前端做所有的工作。这意味着地图数据获取将在浏览器中异步进行，并且没有后端，主要是因为 Surge 没有提供后端。然而，我遇到的问题是 Foursquare API 不像 MapBox 那样支持前端可发布的 API 键。为了从服务中获取数据，您需要一个 OAuth 令牌，任何在互联网上呆过一段时间的人都知道基于前端的 OAuth 是不存在的。如果我走上了在因特网上公布我的秘密密钥的道路，这将会造成一个安全漏洞。

面对这个障碍，我不得不重新思考从 Foursquare API 获取数据的实现，以及它将如何在前端发生。后来我意识到，可以只提供一个预构建的 JSON 文件，其中包含支持地图标记位置所需的所有数据。答对了。

[![Bringo!](img/1711bf0887ff3f2dcc1cc504bcde5ee5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vZRag6Vw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i.imgur.com/MjlyUSt.gif)

T6】

吞咽任务开始时很痛苦，但随着时间的推移，我做对了。事实证明，用分页请求同步调用 HTTP 端点，然后异步写入文件是一件非常痛苦的事情。下面是我最终使用的代码，为了更好的可读性做了注释:

```
// gulp_tasks/tasks/foursquare.js

import {
    writeFile,
    closeSync,
    openSync,
    mkdirSync,
    existsSync
} from 'fs'
import gulp from 'gulp'
import gutil from 'gulp-util'
import request from 'sync-request'

// Get the Foursquare OAuth token that we've previously acquired from the API
const FOURSQUARE_OAUTH_TOKEN = process.env.FOURSQUARE_OAUTH_TOKEN

// Set the API Base URL, straight forward
const API_BASE_URL = "https://api.foursquare.com/v2/users/self/checkins"

// Give the API a unique URL (the docs demonstrate this, though I'm not sure why)
// https://developer.foursquare.com/overview/auth
const API_ENDPOINT = `${API_BASE_URL}?oauth_token=${FOURSQUARE_OAUTH_TOKEN}&v=${Date.now()}`

const buildJSON = (buildPath, callback) => {
  let checkinItems = {}
  let folderPath = `${buildPath}/api`
  let filePath = `${folderPath}/foursquare.json`
  let errorMsg = '✗ Request to Foursquare failed'
  let successMsg = '✓ Hitting Foursquare API:'

  // Get Swarm checkins synchronously
  let res = request('GET', API_ENDPOINT)
  let data = JSON.parse(res.getBody('utf8'))
  if (data.meta.code !== 200) {
    gutil.log(gutil.colors.red(errorMsg))
    return
  }

  // Create the `api` directory if it doesn't already exist
  if (!existsSync(folderPath)) {
    mkdirSync(folderPath)
  }

  // Foursquare says you can only retrieve a max of 250 items per response, so we go with that
  let maxApiCheckins = 250

  // We want to use the total checkin count to use with paginatation math
  let totalApiCheckins = data.response.checkins.count

  // Get the total (ceiling) pages we can call to the api
  let totalApiCheckinPages = Math.ceil(totalApiCheckins / maxApiCheckins)

  // Start the paging offset at 0
  let checkinOffset = 0

  // Get the checkins and page through them
  let i = 0
  while (i < totalApiCheckinPages) {
    // Build the paging URL to use with the API call
    let API_URL = `${API_ENDPOINT}&limit=${maxApiCheckins}&offset=${checkinOffset}`
    gutil.log(gutil.colors.green(successMsg, API_URL))
    let result = request('GET', API_URL)
    let checkinData = JSON.parse(result.getBody('utf8'))
    if (checkinData.meta.code !== 200) {
      gutil.log(gutil.colors.red(errorMsg))
      return
    }

    // Build a giant object of Venue ID => Venue + Checkins Array
    for (let checkin of checkinData.response.checkins.items) {
      let venue = checkin.venue

      if (!checkinItems.hasOwnProperty(venue.id)) {
        checkinItems[venue.id] = {
          'venue': venue,
          'checkins': []
        }
      }

      checkinItems[venue.id]['checkins'].push(checkin)
    }

    // Sort dates of checkins, descending by date
    for (let venueId in checkinItems) {
      checkinItems[venueId]['checkins'].sort(function(a, b) {
        a = new Date(a.createdAt);
        b = new Date(b.createdAt);
        return a>b ? -1 : a<b ? 1 : 0;
      })
    }

    // Bump the offset to use for paging the API
    checkinOffset += maxApiCheckins
    i++
  }

  // Write the JSON data to the file finally
  let jsonData = JSON.stringify(checkinItems)
  writeFile(filePath, jsonData, 'UTF-8', callback)
}

// Tasks to generate the Foursquare JSON data file
gulp.task('foursquare:dev', (cb) => {
  buildJSON('./build_dev', cb)
})

gulp.task('foursquare:prod', (cb) => {
  buildJSON('./build_prod', cb)
}) 
```

Enter fullscreen mode Exit fullscreen mode

当任务运行时，我的终端中的输出如下所示:

```
[11:30:08] Starting 'foursquare:dev'…
[11:30:09] ✓ Hitting Foursquare API: https://api.foursquare.com/v2/users/self/checkins?oauth_token=OAUTHTOKEN&v=1456947006694&limit=250&offset=0
[11:30:11] ✓ Hitting Foursquare API: https://api.foursquare.com/v2/users/self/checkins?oauth_token=OAUTHTOKEN&v=1456947006694&limit=250&offset=250
[11:30:14] ✓ Hitting Foursquare API: https://api.foursquare.com/v2/users/self/checkins?oauth_token=OAUTHTOKEN&v=1456947006694&limit=250&offset=500
[11:30:16] ✓ Hitting Foursquare API: https://api.foursquare.com/v2/users/self/checkins?oauth_token=OAUTHTOKEN&v=1456947006694&limit=250&offset=750
[11:30:18] ✓ Hitting Foursquare API: https://api.foursquare.com/v2/users/self/checkins?oauth_token=OAUTHTOKEN&v=1456947006694&limit=250&offset=1000
[11:30:19] Finished 'foursquare:dev' after 10 s 
```

Enter fullscreen mode Exit fullscreen mode

吞咽任务的要点基本上是:

1.  创建一个临时文件来写入 JSON 数据
2.  获取签入的总数，以便我们可以在后续请求中对 API 进行分页
3.  页面，构建一个类似于

```
…
{
  "Venue ID": {
    "venue:" "Venue object",
    "checkins:" ["Array of check-in objects for this venue"]
  }
}
… 
```

Enter fullscreen mode Exit fullscreen mode

1.  按照日期，按时间降序排列会场签到
2.  将最终编译的对象数据写入 JSON 文件

一旦我能够构建 JSON 文件并将其输出到我的本地构建文件夹，我就开始构建前端地图页面。我不会详细说明我是如何实现这一部分的，因为它是在 Github 的页面中用普通的非混淆 JS 内嵌的。你可以在这里查看源代码和我网站上的 [JSON 文件](https://brandonb.io/api/foursquare.json)(预先警告，JSON 文件相当大，你的浏览器可能会像我的一样崩溃)。

我一直在更新签到页面的外观，所以请定期查看更新😁。