# TIL: JSON.stringify()可以做格式化输出。

> 原文：<https://dev.to/david_j_eddy/til-jsonstringify-can-do-formatted-output-1f44>

TIL JSON.stringify 可以做格式化和缩进输出。`JSON.stringify(yourJSObjectHere, null, 1)`。当您想要不是一起运行的 JSON 格式的输出时，这是非常棒的。如:

```
"impressions": [
   {
    "name": " + Holiday Bundle: Dragon Ball Z - Seasons One - Season Nine ",
    "id": "FUN-33201",
    "price": "159.99",
    "category": "SALE",
    "list": "Home: New Shop Arrivals",
    "position": 1
   },
   {
    "name": "Attack on Titan + Funko Pop - 6&#39;&#39; Colossal Titan",
    "id": "FUN-00183",
    "price": "17.99",
    "category": "Figures & Collectibles",
    "list": "Home: New Shop Arrivals",
    "position": 2
   },
   {
    "name": "Fairy Tail + Collection Five",
    "id": "BLD-00199",
    "price": "49.99",
    "category": "Home Video",
    "list": "Home: New Shop Arrivals",
    "position": 3
   },
   {
    "name": "High School DxD + Asia &amp; Koneko Drawstring Bag",
    "id": "FUN-25211",
    "price": "17.99",
    "category": "Accessories",
    "list": "Home: New Shop Arrivals",
    "position": 4
   },
   {
    "name": "Case Closed + Countdown to Heaven",
    "id": "DVD-00058",
    "price": "11.24",
    "category": "Home Video",
    "list": "Home: New Shop Arrivals",
    "position": 5
   },
   {
    "name": "Attack on Titan",
    "id": "17847",
    "price": "",
    "category": "shows",
    "list": "Home: Home Simuldubs",
    "position": 1
   },
   {
    "name": "My Hero Academia",
    "id": "124389",
    "price": "",
    "category": "shows",
    "list": "Home: Home Simuldubs",
    "position": 2
   },
   {
    "name": "Monster Hunter Stories Ride On",
    "id": "155585",
    "price": "",
    "category": "shows",
    "list": "Home: Home Simuldubs",
    "position": 3
   },
   {
    "name": "One Piece",
    "id": "20224",
    "price": "",
    "category": "shows",
    "list": "Home: Home Simuldubs",
    "position": 4
   }
  ], 
```

Enter fullscreen mode Exit fullscreen mode

就在浏览器控制台中！#令人震惊