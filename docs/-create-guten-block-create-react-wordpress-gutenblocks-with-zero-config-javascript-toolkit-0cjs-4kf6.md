# ð¦åå»º Guten å:ä½¿ç¨é¶éç½® JavaScript å·¥å·å#0CJS åå»º React WordPress # Guten å

> åæï¼<https://dev.to/ahmadawais/-create-guten-block-create-react-wordpress-gutenblocks-with-zero-config-javascript-toolkit-0cjs-4kf6>

[![create-guten-block](img/8f933fd99cf60ccc6290456502c50201.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--bRWcwjdu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/p6XB/c)

ðæççå¾å´å¥åå¸ææå¥½çå¼æºè½¯ä»¶åä¹ä¸ï¼å«å`create-guten-block`ï¼ç¨å®ä½ å¯ä»¥å¼å§ä¸ºæ°ç WordPress ç¼è¾å¨(è®¡åå¨ WordPress 5.0 ä¸­åå¸)åå»ºæ¨¡åã

æ¶²ä½éè¯¯:åé¨

`create-guten-block`æ¯ä¸ä¸ªåºæ§å·±è§çé¶éç½® JavaScript å·¥å·åï¼ç¨å®ä½ ä¸éè¦éç½®ä»»ä½ä¸ ReactãwebpackãESLint ç­ç¸å³çä¸è¥¿ã

æä»¥ï¼å¼å§äº...

[![CGB Create Guten Block by Ahmad Awais](img/1d76228cb55ceca81ec9aaa6fd6282ca.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--nGCYmrgq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/osSb/c)

[![Create Guten Block](img/eb4b1e67fd32beff268215efaa1071d9.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--nm2iyoNx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/orxb/c)

| **`create-guten-block`**
ä¸ä¸ªç¨äºæå»º WordPress Gutenberg block æä»¶çé¶éç½®å¼åèå·¥å·åã |
| ç±[è¾åè¿å¾·Â·é¿ç¦æ¯](https://github.com/ahmadawais)å¼åçä¸ä¸ª FOSS(èªç±&å¼æºè½¯ä»¶)é¡¹ç®ã |
| <sup>å³æ³¨ Ahmad å¨ GitHub ä¸ç#FOSS ä½å[@ Ahmad awais](https://github.com/ahmadawais)ââè¯´ðå¨æ¨ç¹ä¸[@ MrAhmadAwais](https://twitter.com/mrahmadawais/)T5ã</sup> |

# ð¦`create-guten-block`

> `create-guten-block`æ¯*zero configuration dev-toolkit*(# 0CJS)å¨å åéåå¼å WordPress Gutenberg blocksï¼æ ééç½®`React`ã`Webpack`ã`ES6/7/8/Next`ã`ESLint`ã`Babel`ç­ã

åå»º Guten åä¸åäºå¶ä»ç[åå­¦èå·¥å·å](https://github.com/ahmadawais/wpgulp)æ[æ ·æ¿](https://github.com/ahmadawais/Gutenberg-boilerplate)ãè¿æ¯ä¸ä¸ªä¸æ­æ´æ°çå¼åèå·¥å·ç®±ãå ä¸ºå®æ¯é¶éç½®çï¼æä»¥æ¨å¯ä»¥éæ¶æ´æ°å®ï¼èæ éå¯¹æ¨çä»£ç è¿è¡ä»»ä½æ´æ¹ã

æ³è±¡ä¸ä¸ï¼ä½ çé¡¹ç®ä¸­åªæä¸ä¸ªä¾èµé¡¹ï¼èä¸æ¯ 50 ä¸ªã

[![create-guten-block](img/15140d4ed1cf9d1e16fec23393ee5a12.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--08LJx8qF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/p6h2/c)

`create-guten-block`æ¯:

*   ð¥çæ¬å
*   ð¤ å¯æ´æ°
*   ðçä¸å¥çæº-é»è®¤
*   ðä¸ä¸ªåä¸ç`cgb-scripts`ä¾èµå³ç³»

[![Start](img/209060fb60f39fd95e08326dc3f35e10.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--8XfWN3pQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/osd3/c)

## å¥é¨ï¼

ç¨`create-guten-block`ççå¾å®¹æä¸æãåªéå°å®ä½ä¸ºä¸ä¸ªå¨å±æ¨¡åå®è£ï¼ç¶åè¿è¡å®æ¥ä¸º WordPress åå»ºä¸ä¸ä»£ Gutenberg block æä»¶ã

è®©æä»¬å¼å§å§ï¼

**ç¬¬ 0 æ­¥**ââå¦æä½ æ²¡æå®è£`Node.js` + `npm`ï¼é£ä¹è¯»ä¸ä¸è¿ä¸ªã(ç¹å»å±å¼ï¼)

å¦æä½ å¯¹`Node.js`ãJavaScript å`npm`åçä¸çå®å¨æ¯åå­¦èï¼ä½ éè¦åçå°±æ¯å»èç¹çç½ç«[ä¸è½½å¹¶å¨ä½ çç³»ç»ä¸å®è£](https://nodejs.org/en/download/)èç¹ãè¿å°åæ¶å®è£`Node.js`å`npm`ï¼å³èç¹åç®¡çå¨âânode . js çå½ä»¤è¡çé¢

æ¨å¯ä»¥éè¿æå¼ç»ç«¯åºç¨ç¨åºå¹¶é®å¥ä»¥ä¸åå®¹æ¥éªè¯å®è£...

```
node -v
# Results into v9.1.0 â make sure you have Node &gt;= 8 installed.

npm -v
# Results into 5.6.0 â make sure you have npm &gt;= 5.2 installed. 
```

Enter fullscreen mode Exit fullscreen mode

### âæ­¥éª¤#1

éè¿`npx`å®è£`create-guten-block`ï¼å¹¶ç¨å®åå»ºä½ çæä»¶ã

æ¨éè¦å¨æ¬å°å¼åæºå¨ä¸å®è£èç¹> = 8 å npm >= 5.3(ä½å¨æå¡å¨ä¸ä¸éè¦)ãä½ å¯ä»¥ä½¿ç¨ [nvm](https://github.com/creationix/nvm#installation) (macOS/Linux)æè [nvm-windows](https://github.com/coreybutler/nvm-windows#node-version-manager-nvm-for-windows) å¨ä¸åé¡¹ç®ä¹é´è½»æ¾åæ¢èç¹çæ¬ã

ç°å¨ï¼ä½ æè¦åçå°±æ¯åå»ºä¸ä¸ªå¤è¾å ¡åï¼å¹¶å¼å§å»ºè®¾ãè¿æ¯éè¿è¿è¡`create-guten-block`å½ä»¤å¹¶ä¸ºå°è¦åå»ºç WordPress æä»¶æä¾ä¸ä¸ªå¯ä¸çåç§°æ¥å®æçã

> â ï¸ç¡®ä¿å¨ä½ æ¬å° WordPress å®è£ç¨åºçæä»¶æä»¶å¤¹ä¸­è¿è¡è¿ä¸ªå½ä»¤ï¼å³`/local_dev_site.tld/wp-content/plugins/`æä»¶å¤¹ââå ä¸ºè¿ä¸ªå½ä»¤ä¼äº§çä¸ä¸ª WordPress æä»¶ï¼ä½ å¯ä»¥å»`WP-Admin` â¶ï¸ `Plugins`æ¿æ´»å®ã

```
npx create-guten-block my-block 
```

Enter fullscreen mode Exit fullscreen mode

( [npx](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b) éå¸¦ npm 5.2+åæ´é«çæ¬ï¼è¯·åè§æ§çæ¬ npm ç[è¯´æ](https://gist.github.com/ahmadawais/99cd74a5593a9295192adf118182b509)

[![cgb block](img/fa41702acc0d979d4567c2dd07d8cf70.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--nPCp7sSb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/p89T/c)

å®å°å¨å½åæä»¶å¤¹ä¸­åå»ºä¸ä¸ªåä¸º`my-block`çç®å½ãå¨è¯¥ç®å½ä¸­ï¼å®å°çæåå§çé¡¹ç®ç»æå¹¶å®è£å¯ä¼ éçä¾èµé¡¹:

```
INSIDE: /local_dev_site.tld/wp-content/plugins/my-block

âââ plugin.php
âââ package.json
âââ README.md
|
âââ dist
|  âââ blocks.build.js
|  âââ blocks.editor.build.css
|  âââ blocks.style.build.css
|
âââ src
   âââ block
   |  âââ block.js
   |  âââ editor.scss
   |  âââ style.scss
   |
   âââ blocks.js
   âââ common.scss
   âââ init.php 
```

Enter fullscreen mode Exit fullscreen mode

æ ééç½®æå¤æçæä»¶å¤¹ç»æï¼åªéæå»ºåºç¨ç¨åºæéçæä»¶ã

### âæ­¥éª¤#3

å®è£å®æåï¼æ¨å¯ä»¥æå¼é¡¹ç®æä»¶å¤¹å¹¶è¿è¡å¯å¨èæ¬ã

è®©æä»¬å»åå§ã

```
cd my-block
npm start 
```

Enter fullscreen mode Exit fullscreen mode

*ä½ ä¹å¯ä»¥ç¨`yarn start`å¦æé£æ¯ä½ çæé±*

[![cgb-npm-start](img/1a9a5c23a34e3483ea49394cdf9ff9cd.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--ehY2c1Vc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/orrh/c)

è¿å°å¨å¼åæ¨¡å¼ä¸è¿è¡æä»¶ãäº§ççäº§ä»£ç è¿è¡`npm run build`ã
æ¨å°å¨æ§å¶å°ä¸­çå°æå»ºæ¶æ¯ãéè¯¯å lint è­¦åã

> å°±è¿æ ·ï¼ä½ æ­£å¨ç¨ Gutenbergï¼React.jsï¼ES 6/7/8/Next æå»ºä½ çä¸ä¸ä¸ª WordPress æä»¶ï¼ç¨ Babel ç¼è¯ï¼å®ä¹æ ESLint éç½®ä¾ä½ çä»£ç ç¼è¾å¨èªå¨éæ©åä½¿ç¨ã

[![More](img/27b7122a57274a69302c157198ac8d46.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--VeTmFOXj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/orsm/c)

### å·¥ä½æµç¨ï¼

å¨æ¨ç`create-guten-block`å·¥ä½æµç¨ä¸­ï¼æ¨å¯ä»¥ä½¿ç¨ä¸ä¸ªèæ¬ãä½¿ç¨è¿ä¸ä¸ªèæ¬ï¼æ¨å¯ä»¥å¼åãæå»ºåå¼¹åºæ¨çæä»¶ã

#### ð`npm start`

*   ç¨äºå¨å¼åæ¨¡å¼ä¸ç¼è¯åè¿è¡è¯¥åã
*   çè§ä»£ç ä¸­çä»»ä½æ´æ¹å¹¶æ¥åä»»ä½éè¯¯ã

#### ð`npm run build`

*   ç¨äºå¨`dist`æä»¶å¤¹ä¸­ä¸ºæ¨çåæå»ºçäº§ä»£ç ã
*   è¿è¡ä¸æ¬¡å¹¶æ¥åçæä»£ç ç gzip æä»¶å¤§å°ã

#### ð`npm run eject`

*   ç¨äºå°æ¨çæä»¶å¼¹åº`create-guten-block`ã
*   æä¾ææéç½®ï¼ä»¥ä¾¿æ¨å¯ä»¥æ ¹æ®éè¦èªå®ä¹é¡¹ç®ã
*   è¿æ¯ä¸æ¡åè¡éï¼ä½ å¿é¡»èªå·±ç»´æ¤ä¸åã
*   ä½ éå¸¸ä¸éè¦`eject`ä¸ä¸ªé¡¹ç®ï¼å ä¸ºéè¿éåºï¼ä½ å¤±å»äºä¸`create-guten-block`çèç³»ï¼ä»é£ä»¥åä½ å¿é¡»èªå·±æ´æ°åç»´æ¤ææçä¾èµã

*å¤§æ¦å°±æ¯è¿æ ·ã*

[![included](img/de779e300fd68143b863f19d0b1a1036.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--SWCxSk6l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/orOe/c)

## åå«åªäºåå®¹ï¼

ä½ çç¯å¢å°æ¥æä½ éè¦çä¸åæ¥æå»ºä¸ä¸ªç°ä»£çä¸ä¸ä»£ WordPress Gutenberg æä»¶:

*   ååºï¼JSX å ES6 è¯­æ³æ¯æã
*   å¹åç Webpack å¼å/çäº§æå»ºæµç¨ã
*   ES6 ä¹å¤çé¢å¤è¯­è¨ï¼æ¯å¦å¯¹è±¡æ©å±æä½ç¬¦ã
*   èªå¨å åç¼ç CSSï¼æä»¥ä¸éè¦`-webkit`æèå¶ä»åç¼ã
*   ä¸ä¸ªæå»ºèæ¬ï¼å° JSãCSS åå¾åä¸æºå°å¾æç»å¨ä¸èµ·ç¨äºçäº§ã
*   éè¿åä¸ä¾èµå³ç³»å¯¹ä¸è¿°å·¥å·è¿è¡æ éç¢æ´æ°`cgb-scripts`ã

ä»£ä»·æ¯**è¿äºå·¥å·è¢«é¢åéç½®ä¸ºä»¥ç¹å®çæ¹å¼å·¥ä½**ãå¦ææ¨çé¡¹ç®éè¦æ´å¤çå®å¶ï¼æ¨å¯ä»¥[ãå¼¹åºã](https://github.com/ahmadawais/create-guten-block#--npm-run-eject)å¹¶å®å¶å®ï¼ä½æ¯ä¹åæ¨å°éè¦ç»´æ¤è¿ä¸ªéç½®ã

[![Philosophy](img/0cf0fce6d136f80fa1cd9b3bfb40f3a6.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--onSGyRJ0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/orq5/c)

## å²å­¦

*   ä¸ä¸ªä¾èµå³ç³»:åªæä¸ä¸ªæå»ºä¾èµå³ç³»ãå®ä½¿ç¨äº WebpackãBabelãESLint åå¶ä»ä»¤äººæå¹çé¡¹ç®ï¼ä½å¨å®ä»¬ä¹ä¸æä¾äºä¸ç§æåèåçç­åä½éªã

*   **ä¸éè¦éç½®:**ä¸éè¦éç½®ä»»ä½ä¸è¥¿ãå¼ååçäº§çæ¬çåçéç½®å·²ç»ä¸ºæ¨å¤çå¥½äºï¼å æ­¤æ¨å¯ä»¥ä¸æ³¨äºç¼åä»£ç ã

*   **æ éå®:**ä½ å¯ä»¥`eject`éæ¶è¿è¡èªå®ä¹è®¾ç½®ãè¿è¡ä¸ä¸ªå½ä»¤ï¼ææçéç½®åæå»ºä¾èµé¡¹å°è¢«ç´æ¥ç§»å¨å°æ¨çé¡¹ç®ä¸­ï¼å æ­¤æ¨å¯ä»¥ä»æ¨ç¦»å¼çå°æ¹éæ°å¼å§ã

[![Why](img/3c8336ce2e3f207e740a650c835a5981.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--w9rmLR1o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/orvy/c)

## ä¸ºä»ä¹æ¯`create-guten-block`ï¼

å¯ï¼å Webpackï¼Reactï¼ES 6/7/8/Nextï¼ESLintï¼Babel ç­ç­ï¼ççå¾é¾éç½®ãå¨ä½ å¼å§å Hello World gutenberg åä¹åãäºå®ä¸ï¼éç JavaScript ç¤¾åºä¸­æææ°å·¥å·çåºç°ååå±ï¼æ¨å¿é¡»ç»´æ¤å¹¶ä¸æ­æ´æ°æ¨çéç½®ã

`create-guten-block`å°ææè¿äºéç½®éèå¨ä¸ä¸ªæä»¬ç§°ä¹ä¸º`cgb-scripts`çä¼ååä¸­ãè¿ä¸ªåæ¯é¡¹ç®ä¸­å¯ä¸çä¾èµé¡¹ãå½ä½ ç»§ç»­åé ä¸ä¸ä¸ªæå¥½ç WordPress ä¸»é¢åæä»¶æ¶ï¼æä»¬è®©`cgb-scripts`ä¿æææ°ã

[![tldr](img/88e21dce95b263a24d398f843c3c4d28.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--nU7qxcZE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/osPO/c)

## TLï¼éåº¦ä¸è§å½¢å®ä½æ³(dead reckoning)

> å¤ªä¹äºï¼æ²¡çï¼è¿éæä¸ä¸ªæ´ç­ççæ¬ã

æå¼ç»ç«¯åºç¨ç¨åºå¹¶è¿è¡ä»¥ä¸å½ä»¤ã

*   â **Install/Update** : `npm install create-guten-block --global`
*   ð°**åå»º** : `create-guten-block my-block` âå¨æ¬å° WP å®è£ç®å½ä¸è¿è¡ï¼ä¾å¦`/wp.local/wp-content/plugins/`ç®å½ã
*   ð**æµè§** : `cd my-block` âæå¼æ°åå»ºçæä»¶ç®å½ã
*   â»ï¸ **è¿è¡** : `npm start` âè¿è¡å¼åã
*   ð¦**è¿è¡** : `npm run build` âç¨äºçäº§æå»ºã
*   â **è¿è¡**:`npm run eject`ââèªè¡å®å¶ãæ´æ°ãç»´æ¤ã

Create-Guten-Block å·²ç»è¿æµè¯ï¼å¯ä»¥å¨ macOS ä¸è¿è¡ï¼ä½ä¹å¿é¡»å¨ Windows å Linux ä¸è¿è¡ã
å¦ææé®é¢ï¼è¯·æäº¤[é®é¢â](https://github.com/ahmadawais/create-guten-block/issues/new)

[![update](img/edb42600e639069cbf14173922eb4558.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--fsvOthuJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/osO7/c)

## æ´æ°å°æ°çæ¬

åå»º Guten ååä¸ºä¸¤ä¸ªå:

1.  **`create-guten-block`** æ¯ä¸ä¸ªå½ä»¤è¡å®ç¨ç¨åºï¼ä½ å¯ä»¥ç¨å®æ¥åå»ºæ°ç WP å¤è¾å ¡æä»¶ââåªè¦è¿è¡`npx create-guten-block your-block-name`ï¼ä½ å°±å¯ä»¥ä¸ç´ä½¿ç¨ææ°ç create-guten-block å·¥å·åã
2.  **`cgb-scripts`** æ¯çææä»¶é¡¹ç®ä¸­çä¸ä¸ªå¼åä¾èµã

ä½ å ä¹ä¸éè¦æ´æ°`create-guten-block`æ¬èº«:å®å°ææçè®¾ç½®å§æç»`cgb-scripts`ã

å½ä½ è¿è¡`create-guten-block`æ¶ï¼å®æ»æ¯ç¨ææ°çæ¬ç`cgb-scripts`åå»ºé¡¹ç®ï¼æä»¥ä½ ä¼èªå¨è·å¾æ°åå»ºæä»¶çæææ°ç¹æ§åæ¹è¿ã

è¦å°ç°æé¡¹ç®æ´æ°å°æ°çæ¬ç`cgb-scripts`ï¼è¯·æå¼[åæ´æ¥å¿](https://github.com/ahmadawais/create-guten-block#changelog)ï¼æ¾å°æ¨å½åççæ¬(å¦ææ¨ä¸ç¡®å®ï¼è¯·æ¥çæä»¶æä»¶å¤¹ä¸­ç package.json)ï¼å¹¶åºç¨æ°çæ¬çè¿ç§»è¯´æã

å¨å¤§å¤æ°æåµä¸ï¼å¨ package.json ä¸­ä¿®æ¹`cgb-scripts`çæ¬å¹¶å¨è¿ä¸ªæä»¶å¤¹ä¸­è¿è¡`npm install`å°±è¶³å¤äºï¼ä½æ¯æå¥½åèä¸ä¸[çåæ´æ¥å¿](https://github.com/ahmadawais/create-guten-block#changelog)ä»¥äºè§£æ½å¨çéå¤§åæ´ã

æä»¬è´åäºä¿æçªç ´æ§çæ¹åæå°åï¼è¿æ ·ä½ å°±å¯ä»¥è½»æ¾å°åçº§`cgb-scripts`ã

[![Log](img/5750525f0fcbd3ef161327a532db514a.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--xzy2mpo7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/osUz/c)

## åæ´æ¥å¿

éè¯»ä»ä¹ð¦æ°çï¼ðæ¹è¿äºï¼ðåºå®ï¼å¦æðæä»¶æ´æ°äºã

ðç¹å»æ­¤é¾æ¥éè¯»æ´ä¸ªåæ´æ¥å¿â [CGB åæ´æ¥å¿â](https://github.com/ahmadawais/create-guten-block/blob/master/CHANGELOG.md)

æ²¡æä»ä¹æ¯å®æ´çï¼æä»¥è¯·å®¹å¿æä»¬ï¼è®©æä»¬ä¸æ­è¿­ä»£ï¼èµ°åæ´ç¾å¥½çæªæ¥ã

> ```
> 'Coz every night I lie in bed
> The brightest colors fill my head
> A million dreams are keeping me awake
> I think of what the world could be
> A vision of the one I see
> A million dreams is all it's gonna take
> A million dreams for the world we're gonna make ... 
> ```
> 
> ...*å¬â [ä¸ç¾ä¸ä¸ªæ¢¦æ³ï¼](https://www.youtube.com/watch?v=pSQk-4fddDI)T3ã*

[![Hello](img/1cbbd570e01a44d47e5ec3b29f8247cc.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--ygAQeunb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/os6O/c)

#### **ä½ å¥½ï¼æä»¬æ¯ [WordPress å¤«å¦](https://WPCouple.com)** ï¼

æ( [Ahmad Awais](https://twitter.com/mrahmadawais/) )æ¯ä¸åå®æ´çç½ç»å¼åäººåï¼ä¹æ¯ WordPress çå®ææ ¸å¿è´¡ç®èãæçå¦ä¸å([æ¢è¾¾åÂ·å·´å¾å°](https://twitter.com/MaedahBatool/))æ¯ä¸åææ¯é¡¹ç®ç»çï¼å¥¹ä¹æ¯ WordPress çæ ¸å¿è´¡ç®èãæä»¬åæä»¬ç[å¢é](https://WPCouple.com/team)ä¸èµ·è¿è¥[WPCouple.com](https://WPCouple.com/)ã

å¦æä½ æ³æ·±å¥äºè§£æä»¬å¯¹å¼æºè½¯ä»¶ãä¸ä¸å¨æ å¼åãWordPress ç¤¾åºãJavaScript çåå±ææå®¶ãåä¸çç­ç±ï¼é£ä¹å°±è®¢éæä»¬åä¸ºâ£ [The WordPress Takeaway](https://WPTakeaway.club) çä¼è´¨ç®è®¯å§ï¼

#### [**æ¯ææä»¬çå¼æºé¡¹ç®ï¼**](https://pay.paddle.com/checkout/515568) ð©

å¦æä½ å¸ææä»¬ç»§ç»­çäº§ä¸ä¸çèªç±åå¼æºè½¯ä»¶(FOSS)ãèèä¸ºæä¸å°æ¶çå¼åæ¶é´ä»è´¹ãæä»¬å°è±ä¸¤ä¸ªå°æ¶å¨æ¯ä¸ªè´¡ç®çå¼æºä¸ãæ¯çï¼æ²¡éï¼ä½ ä»äºä¸ä¸ªå°æ¶çé±ï¼ç¶åè®©æä»¬ä¿©è±ä¸ä¸ªå°æ¶ä½ä¸ºæè°¢ã

[![Partners](img/6907ad554191b101cfa57b34d1b6393f.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--Ld_PQnlS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/osEJ/c)

### é¡¹ç®æ¯ææ¹& [WPCouple åä½æ¹](https://WPCouple.com/partners) â¡ï¸

è¿ä¸ª FOSS(èªç±åå¼æºè½¯ä»¶)é¡¹ç®æ¯å¨ä¸é¢ååºçä¼ç§ä¼ä¸çå¸®å©ä¸æ´æ°åç»´æ¤çãæ²¡æè¿äºäºä¸èµ·çå¬å¸/ä¸ªäººçæ¯æï¼è¿ä¸ªé¡¹ç®æ¯ä¸å¯è½çã

ââ*ä»ä¹/ææ ·ï¼[äºè§£è¯¦æâ](https://WPCouple.com/partners)T3ã*

| [![](img/b9cafdeac6a61cde9e42b45eece3b9eb.png)T2ã](https://www.gravityforms.com/?utm_source=WPCouple&utm_medium=Partner) | [![](img/17ad73b2eec95e787dda6766939b4aba.png)T2ã](https://kinsta.com/?utm_source=WPCouple&utm_medium=Partner) | [![](img/8014e46d93054bb5a02678b8a15530b4.png)T2ã](https://wpengine.com/?utm_source=WPCouple&utm_medium=Partner) |
| [![](img/08755f07033549525ecc607769eca5ab.png)T2ã](https://www.sitelock.com/?utm_source=WPCouple&utm_medium=Partner) | [![](img/d87423ee9cbf5431cc12b47e4ab5db80.png)T2ã](https://wp-rocket.me/?utm_source=WPCouple&utm_medium=Partner) | [![](img/80b5c0f18515df4c19522e0a7ce3287b.png)T2ã](https://blogvault.net/?utm_source=WPCouple&utm_medium=Partner) |
| [![](img/7f329be28828399f8b8d62dd23dfe23d.png)T2ã](http://cridio.com/?utm_source=WPCouple&utm_medium=Partner) | [![](img/c48d3609845a863cafad1fe786fb745d.png)T2ã](http://wecobble.com/?utm_source=WPCouple&utm_medium=Partner) | [![](img/dbcb9c95b7de24efa708f62ce0aca8fc.png)T2ã](https://www.cloudways.com/?utm_source=WPCouple&utm_medium=Partner) |
| [![](img/76674c7bf1044b28336782ddf47ddb4a.png)T2ã](https://www.cozmoslabs.com/?utm_source=WPCouple&utm_medium=Partner) | [![](img/f6ea86c6ea3f9ce17de562b70b12c5ec.png)T2ã](https://wpgeodirectory.com/?utm_source=WPCouple&utm_medium=Partner) | [![](img/fb176a9738ce0cc205e4cefd570797ac.png)T2ã](https://www.wpsecurityauditlog.com/?utm_source=WPCouple&utm_medium=Partner) |
| [![](img/b274d2dbc4896a1cd57a1dcac6d574ea.png)T2ã](https://mythemeshop.com/?utm_source=WPCouple&utm_medium=Partner) | [![](img/2e0d3e69d4f66ed636efaf7ed9477e0b.png)T2ã](https://www.liquidweb.com/?utm_source=WPCouple&utm_medium=Partner) | [![](img/25c39b0c39b7e4e78136a230ae39b706.png)T2ã](https://WPCouple.com/contact?utm_source=WPCouple&utm_medium=Partner) |

[![Thanks](img/411ce3934b623ca35b03d7f13768b5d6.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--7OCtTDXU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/orkW/c)

## æ§ç§&å½å±

è¾åè¿å¾·Â·é¿ç¦æ¯ã

è¿ä¸ªé¡¹ç®ççµææ¥èªäºæ¯æå¨è¿éè½æå°çæ´å¤çäººçå·¥ä½ãä¸è¿ï¼è¿æ¯è¦æè°¢åå»º React App ç[ä¸¹Â·é¿å¸æè«å¤«](https://twitter.com/dan_abramov)ãReact.js å¢éç[å®å¾·é²Â·åæå](https://twitter.com/acdlite)å[åéæ¯å¤å¤«Â·åå¤](https://twitter.com/vjeux)ã[ç´¢è²Â·é¿å°çç¹](https://twitter.com/sophiebits)ãReact[React](https://ReactForBeginners.com/friend/AHMADAWAIS)ã [ES6](https://ES6.io/friend/AHMADAWAIS) å [Node](https://LearnNode.com/friend/AHMADAWAIS) åå­¦èç[é¦æ¯Â·åæ¯](https://twitter.com/wesbos)çç²¾å½©è¯¾ç¨ã [Kent C. Dodds](https://twitter.com/kentcdodds) ä¸ºä»çå¼æºå¸éï¼WordPress æ ¸å¿è´¡ç®èï¼ [Gary](https://twitter.com/GaryPendergast) ä¸ºä¿ææ¯ä¸ªäººççæºï¼ [Gutenberg](http://github.com/wordpress/gutenberg) å¼åè [Matias](https://twitter.com/matias_ventura) ï¼ [Riad](https://github.com/youknowriad) ï¼ [Andrew](https://github.com/aduth) ï¼ [Ella](https://twitter.com/ellaiseulde) ï¼[é»æºä¸­](https://github.com/jasmussen)ï¼ [Tammie](https://twitter.com/karmatosed) ï¼ [Greg](https://twitter.com/gziolo) åè´¡ç®èï¼ä»¥åå¶ä»

[![Thanks](img/a6fdfb0ccd09288c3a8f92b8a51ae8ce.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--TEuNNLFD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://on.ahmda.ws/oy5y/c)

### æ´æ°

*   ð [`create-guten-block`](https://github.com/ahmadawais/create-guten-block) å·²ç»ç«äº~ 500[GitHub ä¸çè§æè](https://github.com/ahmadawais/create-guten-block/stargazers)
*   ðåï¼åï¼ä»å¤©ï¼[é¡¹ç®æ­£å¨ GitHub JavaScript repos ä¸æä¸º](http://on.ahmda.ws/oytg)çç­é¨è¯é¢
*   ðä»å¤©å¾è£å¹¸è¢«åä¸º GitHub ä¸ç[è¶å¿å¼åèââè¿å¤ªç¯çäºï¼](http://on.ahmda.ws/oz9O)
*   ð¥Holly Molly â [`create-guten-block`](https://github.com/ahmadawais/create-guten-block) ç°å¨æ¯ GitHub ä¸ææè¯­è¨ç[è¶å¿ï¼](http://on.ahmda.ws/oy9x)
*   ð£create-guten-block å¨ ProductHunt çä¸»é¡µä¸æååäºï¼è¿å¤ªæ£äº
*   ðæ¥èª a8c ç Gary éè¿[åä¸è¿æ¡æä¹éå¤§çæ¨æ](https://wptavern.com/gutenberg-boilerplate-demonstrates-how-to-build-custom-blocks),ä»¥ä»çæ¹å¼æ¥æ¬£èµ create-guten-block å·¥å·å
*   ð©é¦æ¯Â·åæ¯æ¯çï¼å°±æ¯é£ä¸ªï¼å¨æ¨ç¹ä¸è¯´ä»è®¡åå¦ä½[è¯ç¨ create-guten-block](https://twitter.com/wesbos/status/955426735532724224) åæ°ç WordPress Gutenberg ç¼è¾å¨
*   ð²é©¬ç¹Â·åä¼¦å¨å°ææå[å¤è¾å ¡æ ·æ¿](https://ahmadawais.com/gutenberg-boilerplate/)å [`create-guten-block`](https://github.com/ahmadawais/create-guten-block) é¡¹ç®ä¸èµ·åå¥äº[ä»ç 2018 å¹´è·è¿åå](https://www.mattcromwell.com/who-what-ill-follow-in-2018/)
*   âæä¸ºæ­¤åäºä¸ç¯åæ:[ä»ç» Create Guten Block](https://ahmadawais.com/create-guten-block-toolkit/)

æ¶²ä½éè¯¯:åé¨

> ð¥å¦æä½ [å¨æ¨ç¹ä¸åå¸å³äºå®çæ¶æ¯](https://twitter.com/home?status=%F0%9F%94%A5%20Configuring%20and%20maintaining%20React,%20Webpack,%20ES6/7/8/Next,%20ESLint,%20Babel,%20etc.%20is%20not%20what%20I%20do%20while%20creating%20%40WordPress%20%23Gutenberg%20blocks.%0A%0AI%20use%20a%20%230CJS%20dev%20%23toolkit%20called%20%60create-guten-block%60%20by%20%40MrAhmadAwais%20%F0%9F%9B%A0%F0%9F%9A%80%0A%0A%F0%9F%91%89%20CHECK%20IT%20OUT%3A%20https%3A//AhmdA.ws/CreateGutenBlock_%20%20%0A)ï¼å°è¯ä¸ä¸ï¼å¹¶ååºè´¡ç®ï¼é£å¯¹ææ¥è¯´å°æå³çæ´ä¸ªä¸çã