# ðTinder çæ ç¨ä»£ç ð

> åæï¼<https://dev.to/0xkoji/super-useless-snippets-for-tinder-k4e>

æ­£å¦ä½ ä»¬ä¸­çä¸äºäººæç¥éçï¼ææ¶ççéè¦ä¸ºå®å¨æ ç¨çä¸è¥¿ç¼åä»£ç ð

### 1ãæå¼è°·æ­æµè§å¨å`Developer Tools`

### 2ãç¹å»`Toggle device toolbar`æé®(æ¨å¯ä»¥éæ©ä»»ä½æ³è¦ä½¿ç¨çè®¾å¤)

### 3ãå» https://tinder.com/app/recs[ç](https://tinder.com/app/recs)

### 4ãå¤å¶å¹¶ç²è´´ä»¥ä¸åå®¹ä¹ä¸

å¯¹äº`not like`æé®

```
setInterval(
     () => {
        const ele = document.getElementsByClassName("recsGamepad__button");
        ele[1].click();
    }, 2000) 
```

Enter fullscreen mode Exit fullscreen mode

å¯¹äº`like`æé®

```
setInterval(
     () => {
        const ele = document.getElementsByClassName("recsGamepad__button");
        ele[3].click();
    }, 2000) 
```

Enter fullscreen mode Exit fullscreen mode

#### éæºçæ¬

```
setInterval(
     () => {
        const ele = document.getElementsByClassName("recsGamepad__button");
        const rand = Math.random();
        if(rand >= 0.5) {
          ele[1].click();
        } else {
          ele[3].click();
        }
    }, 2000) 
```

Enter fullscreen mode Exit fullscreen mode