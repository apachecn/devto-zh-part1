# reactjs-popup ç®ä»ðâæ¨¡æãå·¥å·æç¤ºåèåâå¨é¨å¨ä¸ä¸ªä¸­

> åæï¼<https://dev.to/elaziziyoussouf/introducing-reactjs-popup--modals-tooltips-and-menus-all-inone-4759>

> æ¬æå°åæ¨ç®è¦ä»ç»ä½¿ç¨ reactjs-popup å¯ä»¥åä»ä¹ï¼ä»¥åå¦ä½ææå°ä½¿ç¨å®ã

ä»å¤©ï¼æä»¬å¾é«å´å°å®£å¸ reactjs-popup 1.0ã

Reactjs-popup æ¯ä¸ä¸ªç®åä¸éå¸¸å°(3 kb)ç react popup ç»ä»¶ï¼æå¤ä¸ªç¨ä¾ã
æä»¬åå»ºäº reactjs-popup æ¥ä¸ºæä»¬çé¡¹ç®åå»ºä¸ä¸ªé¢è²éæ©å¨ [picsrush](https://picsrush.com/) ä¸ä¸ªæ°çå¨çº¿å¾åç¼è¾å¨ãè¿äºä¸æ®µæ¶é´ï¼æä»¬å³å®è®© GitHub å npm åºä¸­çæ¯ä¸ªäººé½å¯ä»¥ä½¿ç¨å®ã

### ä¸ºä»ä¹éè¦éæ© reactjs-popup èä¸æ¯å¶ä»ææå®ç°ï¼

*   ä½¿ç¨ react çæ®µæå»ºï¼è¿æå³çå¨æ¨çä»£ç æè§¦åå¨åç´ ä¸­æ²¡æé¢å¤çåè£ divãð®
*   ä¸è¦å¨åºç¨ç¨åºæ ¹ä¹å¤æ³¨å¥ HTMLãð¦
*   å½æ°ä½ä¸ºå­æ¨¡å¼æ¥æ§å¶ä»£ç ä¸­ä»»ä½å°æ¹çå¼¹åºçªå£ãðª
*   æ¨¡æï¼å·¥å·æç¤ºï¼èå:é½å¨ä¸ä¸ªðï¸
*   å¨é£æ ¼å®å¶ð
*   å¥½ç¨ãð
*   ææè¿äºæ¶éé½åç¼©äºå¤§çº¦ 3 kBãâ¡ï¸

è¦æ±ååº> = 16.0

### reactjs-popup å¦ä½å¨ä½ çä¸ä¸ä¸ª react é¡¹ç®ä¸­å¸®å©ä½ ï¼

å¦æä½ éè¦åå»ºä¸ä¸ªç®åçæ¨¡æï¼å·¥å·æç¤ºæèåµå¥èåï¼è¿ä¸ªç»ä»¶æ¯ä½ æå¥½çéæ©ãä½æ¯é¦åï¼è®©æä»¬ä»ç»ä»¶å¼å§ã

> å¨åè½æ¼ç¤º:æ¨¡æå·¥å·æç¤ºåèå

[![alt text](img/5ac0ade00fae12c8bb0f72f469e53c23.png)T2ã](https://res.cloudinary.com/practicaldev/image/fetch/s--_5t9apll--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8jx1f8jev90en8a695tg.gif)

### å¥é¨

æ­¤è½¯ä»¶åå¨ npm å­å¨åºä¸­ä»¥ reactjs-popup çå½¢å¼æä¾ãå®å¯ä»¥å¨æææµè¡çæææºä¸æ­£å¸¸å·¥ä½ã

```
npm install reactjs-popup --save
#using yarn
yarn add reactjs-popup -S 
```

Enter fullscreen mode Exit fullscreen mode

ç°å¨æ¨å¯ä»¥å¯¼å¥ç»ä»¶å¹¶å¼å§ä½¿ç¨å®:

```
import React from "react";
import Popup from "reactjs-popup";

export default () => (
  <Popup trigger={<button> Trigger</button>} position="right center">
    <div>Popup content here !!</div>
  </Popup>
); 
```

Enter fullscreen mode Exit fullscreen mode

æ¨ä¹å¯ä»¥å°å®ä¸ function as children æ¨¡å¼ä¸èµ·ä½¿ç¨ã

```
import React from "react";
import Popup from "reactjs-popup";

export default () => (
  <Popup trigger={<button>Trigger</button>} position="top left">
    {close => (
      <div>
        Content here
        <a className="close" onClick={close}>
          &times;
        </a>
      </div>
    )}
  </Popup>
); 
```

Enter fullscreen mode Exit fullscreen mode

ææ¡£åç¤ºä¾: [Reatjs å¼¹åºä¸»é¡µ](https://react-popup.netlify.com)

## ç°å¨è½®å°ä½ æ¥è¯è¯äºï¼ï¼ï¼

æè°¢éè¯»ï¼å¦æä½ è®¤ä¸ºå¶ä»äººåºè¯¥éè¯»è¿ç¯æç« å¹¶ä½¿ç¨è¿ä¸ªç»ä»¶ï¼åæ¨æå¹¶åäº«è¿ç¯æç« ï¼ç¶åå¯å¨[ç»ä»¶åè´­](https://github.com/yjose/reactjs-popup)ã

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[yjose](https://github.com/yjose)/[reactjs-popup](https://github.com/yjose/reactjs-popup)

### ååºå¼¹åºç»ä»¶-æ¨¡æï¼å·¥å·æç¤ºåèå-é½å¨ä¸ä¸ª

<article class="markdown-body entry-content container-lg" itemprop="text">

# Reactjs-popup

[![Build Status](img/0b447eb738eae9f1c7d5e209e3e97512.png)](https://travis-ci.org/yjose/reactjs-popup)[![npm bundle size](img/6855ff35043ff7f96905375861a776ac.png)](https://travis-ci.org/yjose/reactjs-popup)[![version](img/2d55df4541b618726a99f8224544f7e3.png)](https://www.npmjs.com/package/reactjs-popup)[![downloads](img/9ba68dfaf1a1765daeed25dbb2b3d706.png)T11ã](http://www.npmtrends.com/reactjs-popup)

[![MIT License](img/95895f2689ccee468f9b2a20816ad7a7.png)](https://github.com/yjose/reactjs-popup/blob/master/LICENSE)[![All Contributors](img/2bfa1ec94cf3e8088e337f21862aa561.png)](https://raw.githubusercontent.com/yjose/reactjs-popup/master/#contributors)[![PRs Welcome](img/2ac101a3f0a289ce69874c2c43ef53b7.png)](http://makeapullrequest.com)[![Code of Conduct](img/f07fbcdb558c501b5fe97062fe812084.png)T11ã](https://github.com/yjose/reactjs-popup/blob/master/CODE_OF_CONDUCT.md)

[![Watch on GitHub](img/53852a7430e932d1045f9799cadcdb38.png)](https://github.com/yjose/reactjs-popup/watchers)[![Star on GitHub](img/c23d58784747376ca996b50f51ab64fa.png)](https://github.com/yjose/reactjs-popup/stargazers)[![Tweet](img/b6bb86382eab9303de515f6f8b28336f.png)](https://twitter.com/intent/tweet?text=Check%20out%20reactjs-popup%20by%20%40ElaziziYoussouf%20https%3A%2F%2Fgithub.com%2Fyjose%2Freactjs-popup%20%F0%9F%91%8D)

reactjs-popup æ¯ä¸ä¸ªç®åç react popup ç»ä»¶ï¼å¸®å©ä½ ä¸ºä¸ä¸ä¸ª React åºç¨åå»ºç®ååå¤æçæ¨¡æãå·¥å·æç¤ºåèåã

åºäºä»¥ä¸å ä¸ªåå ï¼æ¨åºè¯¥èèä½¿ç¨ reactjs-popup:

*   **<g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> Modal, Tooltip, MenuÂ : All in one library <g-emoji class="g-emoji" alias="weight_lifting" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f3cb.png">ðï¸</g-emoji>**
*   **<g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">ãéå¸¸æ´æ´çå¾ä¹¦é¦(3kb)</g-emoji>**
*   **<g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> å®å¨å¯è®¿é®**
*   **<g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">âä½ä¸ºå­æ¨¡å¼æ¥æ§å¶ä½ ä»£ç ä¸­ä»»ä½å°æ¹çå¼¹åºçªå£<g-emoji class="g-emoji" alias="muscle" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4aa.png">ðª</g-emoji></g-emoji>**
*   **<g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> å¥½ç¨<g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">ð</g-emoji>**
*   **<g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> æå­ç¨¿æ¯æ<g-emoji class="g-emoji" alias="ok_hand" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f44c.png">ð</g-emoji>**
*   **<g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">ie æ¯æã<g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">ð</g-emoji></g-emoji>**
*   **<g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> å¨é£æ ¼å®å¶<g-emoji class="g-emoji" alias="ok_hand" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f44c.png">ð</g-emoji> (jsï¼CSSï¼styled-components)**
*   **<g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">å¯¹åæ§æ¨¡æçæ¯æ&å·¥å·æç¤º</g-emoji>**
*   **<g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">é»è®¤&èªå®ä¹å¨ç»</g-emoji>**

## æ¼ç¤º

è¿æ¯ä¸ä¸ªç®åçæ¼ç¤ºç¨åºï¼æ¼ç¤ºäºå¦ä½ä½¿ç¨`reactjs-popup`åå»ºæ¨¡æãå·¥å·æç¤ºåèåã

[**ç°åºè¯ç©**](https://react-popup.elazizi.com)

[![ reactjs popup demo](img/cb32eee78c8a22b09a2ff23beb6b577e.png)T2ã](https://user-images.githubusercontent.com/11137944/92184555-74556c00-ee49-11ea-81c5-eb2a0087e93a.gif)

## å®è£/å¥é¨

è¯¥è½¯ä»¶åå¨ NPM èµæºåºä¸­ä»¥ reactjs-popup çå½¢å¼æä¾ãå®å¯ä»¥å¨æææµè¡çæææºä¸æ­£å¸¸å·¥ä½ã

```
npm install reactjs-popup --save
```

Enter fullscreen mode Exit fullscreen mode

ä½¿ç¨çº±çº¿

â¦Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/yjose/reactjs-popup)

ä½ è§å¾æä¹æ ·ï¼æä»ä¹æ³æ³åðï¼