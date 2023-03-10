# ðå®£å¸é¸é¨:React çâå è½½æ´å¤âç»ä»¶ð

> åæï¼<https://dev.to/geoff/announcing-floodgate-a-load-more-component-for-react-3llp>

æå¾é«å´å°å®£å¸ï¼ç»è¿ 2 å¹´å¤çå¼åï¼ [**React æ°´é¸**](https://github.com/geoffdavis92/react-floodgate) å·²ç»åå¸äº 1.0.0 çæ¬ï¼

Floodgate æ¯ [React.js](https://reactjs.org) çâå è½½æ´å¤âç»ä»¶ï¼å®å°ç®¡çåæ´æ°æéæ°æ®çå¤ææ§è½¬åä¸ºç´è§çä½éªãå®éç¨äºä¸ç§æç®çéç½®æ¹æ³:åªéè¦ä¸ä¸ªéå·ï¼å¹¶ä½¿ç¨[æ¸²æéå·æ¨¡å¼](https://reactjs.org/docs/render-props.html)ï¼å¼åäººåå¯ä»¥ç²¾ç¡®æ§å¶æ¸²æå¤å°å¯ç¨æ°æ®ï¼ä»¥åæ¸²æçæ¹å¼ã

è¦å¿«éäºè§£ Floodgate çåºæ¬å®ç°ï¼è¯·æ¥çä¸é¢çç¤ºä¾:

```
import React from "react";
import Floodgate from "react-floodgate";

export default function LoadMore() {
  const albums = ["For Emma, Forever Ago", "Bon Iver, Bon Iver", "22 a million", "i,i"];
  return (
    <Floodgate data={albums} initial={1} increment={1}>
      {({ items, loadNext, loadComplete }) => (
        <React.Fragment>
          <h1>Bon Iver Albums</h1>
          <ol>
            {items.map(album => <li>{album}</li>)}
          </ol>
          <button onClick={loadNext} disabled={loadComplete}>Add Album</button>
        </React.Fragment>
      )}
    </Floodgate>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

è¿å°±æ¯åå»ºä¸ä¸ªå¯å·¥ä½çâå è½½æ´å¤âç»ä»¶æéè¦çå¨é¨åå®¹ï¼

è¦ç«å³å¼å§ä½¿ç¨ Floodgateï¼è¯·å°å¶å®è£å°æ¨ç React é¡¹ç®ä¸­:

```
# using npm
npm i react-floodgate

# using yarn
yarn add react-floodgate 
```

Enter fullscreen mode Exit fullscreen mode

### ç¹æ§

è½ç¶ Floodgate å¾ç®åï¼ä½å®å¯è½éå¸¸å¼ºå¤§ï¼è¿åå³äºå®çç¹æ§å¦ä½ä¸å¶ä»ç»ä»¶åæ¨¡å¼ç¸ç»åãä¸é¢ç®åä»ç»ä¸ä¸ Floodgate å¯ä»¥åä»ä¹ï¼æ¥ç[èªè¿°æä»¶](https://github.com/geoffdavis92/react-floodgate/blob/master/README.md)ä»¥è·å¾æ´æ·±å¥ççè§£åææ¯ç»èã

#### ðæ¶è´¹ä»»ä½ç±»åçæ°æ®

Floodgate ç`data` prop åªè¦æ±ä¼ éä¸ä¸ªæ°ç»ç»å®ï¼æ°ç»å¯ä»¥æ¯ä»»ä½ä¸è¥¿ï¼åæ¬ç©ºçï¼å­ç¬¦ä¸²ãè§£æç JSON å¯¹è±¡ãReact ç»ä»¶ï¼çè³ JavaScript å½æ°é½å¯ä»¥ä¼ å¥ï¼è¯·è®°ä½ï¼Floodgate å°è¿äºé¡¹ç®çåç°åå®ç°çç»äºå¼åäººåã

å`data={["hello", "world"]}`ã`data={[<li>Eggs</li>, <li>cereal</li>, <li>paper towels</li>]}`ã`data={[]}`è¿æ ·çå¼é½æ¯ææçï¼å¯ä»¥ä¼ éç»æ°´é¸ãæ³¨æï¼è½ç¶ Floodgate ä¸å³å¿ä»ä¹ç±»åçé¡¹ç®ç»ææ°ç»ï¼ä½å»ºè®®ç¡®ä¿æææ°ç»åç´ çç±»åæ¯ä¸è´çã

#### ð¢ç¡®å®è¦æ¸²æçé¡¹ç®æ°é

åè¯ Floodgate åºè¯¥ç¨`initial`éå·å¨åå§æ¸²æä¸­å è½½å¤å°æ¥èª`data`æ°ç»çé¡¹ç®ã`increment`éå·å¤çå¨æ¸²æéå·å½æ°ä¸­å¯¹`loadNext()`çåç»­è°ç¨ä¸­å è½½å¤å°é¡¹ç®ã

#### ðä»ç¶ç»ä»¶ç®¡çéå·

éè¿å©ç¨ React ççå½å¨ææ¹æ³åèªå®ä¹åè°å±æ§ï¼Floodgate çå±æ§å¯ä»¥å®å¨ç±ç¶ç»ä»¶çç¶æç®¡çï¼åè®¸å®ä¾çæ°æ®å¼æ­¥æ´æ°ææç»ç¨æ·å¯¹å è½½å¤å°é¡¹ææ´å¤çæ§å¶ãæç§°ä¹ä¸º[æ§å¶é¸é¨](https://github.com/geoffdavis92/react-floodgate#controlled-floodgate)æ¨¡å¼ã

#### âï¸ç¨äºä»¶é©±å¨çåè°éå·å¤çäºä»¶

render prop å½æ°å¬å¼äºè®¸å¤ç±å¶å­ç»ä»¶è°ç¨ç Floodgate æ¹æ³ï¼å³`loadNext`ã`loadAll`ã`reset`å`exportState`ãå½è¿äºæ¹æ³è¢«è°ç¨æ¶ï¼Floodgate è°ç¨æä¾ç»`on[MethodName]`éå·çå½æ°ï¼å¦ææä¾çè¯ã

#### ð®å©ç¨ä¸ä¸æ API

ä» v0.6.0 å¼å§ï¼Floodgate å©ç¨ React ç[ä¸ä¸æ API](https://reactjs.org/docs/context.html) ,é¿åäºå¼åäººåä½¿ç¨`FloodgateContext`å¯¼åºå°æ¸²æéå·ä¸­å¬å¼çæ¹æ³ä¼ éå°ä»»ä½éè¦å®ä»¬çå°æ¹ã

#### ð ç¨æå­ç¨¿å»ºæ

Floodgate æ¯ç¨ Typescript æå»ºçï¼å¹¶ååäºä¸ä¸ª[ç±»åå®ä¹æä»¶](https://github.com/geoffdavis92/react-floodgate/blob/master/src/types.d.ts)ï¼ä»¥å¢å¼ºå¼åäººåçä½éªã

### ä¾å­

ä¸ºäºæ´å¥½å°çè§£ Floodgate å¦ä½å·¥ä½ï¼è¯·æ¥çè¿äº [Codesandbox.io ç¤ºä¾](https://codesandbox.io/search?query=&page=1&configure%5BhitsPerPage%5D=12&refinementList%5Btags%5D%5B0%5D=react-floodgate-examples)ãæ¨å¯ä»¥çå°ä»£ç çè®¾ç½®æ¹å¼ï¼ä»¥åæç»ç¨æ·å°ä¸ä¹äº¤äºçåºç¨ç¨åºã

æä¸ªäººç½ç«ç[åä½é¡µé¢](https://geoffdavis.info/writing/)ä¸å°±æä¸ä¸ªç°å®çæ´»ä¸­çä¾å­ã

### è·¯çº¿å¾

è¿ä¸ªé¡¹ç®éå¸¸ç¯ä¿ï¼ä½æ¯é¤äºè§£å³é®é¢åæ»¡è¶³ Floodgate ç¨æ·çè¿«åéæ±ä¹å¤ï¼æè¿è®¡åå¨ä¸ä¹çå°æ¥å®ç°ä¸äºåè½:

*   è¯¯å·®è¾¹ç
*   æé©æ¯æ¶(`useFloodgate`)
*   ææ¡£ç½ç«
*   æ¹è¿èªè¿°æä»¶ï¼å°¤å¶æ¯âè´¡ç®èâé¨å

### æ¢ç´¢ GitHub

Floodgate å¯å¨ GitHub ç[geoffdavis 92/react-flood gate](https://github.com/geoffdavis92/react-floodgate)è·å¾ãå¨é£éï¼æ¨å¯ä»¥æ¥çç»ä»¶çèªè¿°æä»¶ï¼æ£æ¥æºæä»¶ï¼æäº¤é®é¢ï¼æ¥çæå¼çé¡¹ç®ï¼å¹¶åææå¸¸è§ç GitHub repo äºæã

### ä½ æä¹çï¼

ä½ åæ¬¢æ°´é¸åï¼ä½ ç app æ¥éè¿ä¸ªç»ä»¶åï¼è¯·éè¿[tweet åè¯æ](https://twitter.com/intent/tweet?text=%40gdavis92%20%5Byour%20thoughts%20here%5D%20%23reactfloodgate)è¿ä»¶äºï¼æèå¨ä¸é¢çä¸è¯è®ºï¼

ðå¿«ä¹åå±ï¼ð