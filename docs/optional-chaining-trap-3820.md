# ð°å¯éé¾æ¥é·é±ï¼

> åæï¼<https://dev.to/slashgear_/optional-chaining-trap-3820>

å å¤©åï¼TC39 ç¬¬ä¸é¶æ®µåå¸äºä¸ä¸ªå¬åï¼è¿æ¯è®¸å¤äººææå¾çã**å¯éé¾æ¥**

è¿éçä¾å­æ

[![thelarkinn image](img/6324cdb2587d1afb07a7ec13677b7b9b.png)](/thelarkinn)

## [èæ©Â·æé](/thelarkinn)

[/thelarkinn](/thelarkinn)

æ¶²ä½éè¯¯:åé¨

å¯¹äº Javascript æ¥è¯´ï¼è¿æ¯ä¸ä¸ªå¥½æ¶æ¯ï¼è¿ä¸ªåè½å¾æ£ï¼ä½æ¯...

è¯·åææçè§ç¹ï¼è¿é¡¹åè½æå¤©å°ä¸ä¼å¨æä»¬çæµè§å¨ä¸­æä¾ãå¨ç¬¬ 3 é¶æ®µï¼å¾æå¯è½å¨ ES çä¸ä¸ä¸ªçæ¬ä¸­æ·»å è¿ä¸ªç¹æ§ã

# ä½æ¯

æä»¬éè¦å¨å¾é¿ä¸æ®µæ¶é´åæå®æ··æ·èµ·æ¥ã

å¦æä½ çä¸ç[@ babel/plugin-proposal-optional-chaining](https://babeljs.io/docs/en/babel-plugin-proposal-optional-chaining)ï¼è¿å°±æ¯ babel å°å¦ä½ä¼ è¾å®ã

[![babel transpile example](img/64ac0884862eed8114be281d509c4a5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0IUdYEe_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vtjdvqtvw5h6ti11xh7u.png)

è¿æ¯ä¸ä¸ªéå¸¸åé¿çè¾åºã

è®©æä»¬æ³è±¡ä¸ä¸ï¼æä»¬å¨ä¸ä¸ª web åºç¨ç¨åºä¸­å¤æ¬¡ä½¿ç¨è¿ä¸ªç¹æ§ï¼èæ¨å°å®ç¨äºæ·±å±æåµã

```
const foo = everything?.could?.be.nullable?.maybe

// Babel will generate this output

var _everything, _everything$could, _everything$could$be$;

var foo = (_everything = everything) === null || _everything === void 0 ? void 0 : (_everything$could = _everything.could) === null || _everything$could === void 0 ? void 0 : (_everything$could$be$ = _everything$could.be.nullable) === null || _everything$could$be$ === void 0 ? void 0 : _everything$could$be$.maybe;

// Terser would modify like this
l,n,o;null===(l=everything)||void 0===l||null===(n=l.could)||void 0===n||null===(o=n.be.nullable)||void 0===o||o.maybe 
```

Enter fullscreen mode Exit fullscreen mode

å¨ä½ çåéä¼éå¸¸åé¿ãbabel å¨ç¶æä¸­æåçè½¬æ¢æ ¹æ¬ä¸å`lodash.get`æè½åçé£æ ·å±äº« *nullsafe* è®¿é®æºå¶ãå³ä½¿*æ´è¾¾ä»*éå¸¸/å¤ªéãå®æä¾äºæ´ææçç©ºå®å¨å®ç°ï¼åæ¶çææ´å°çä»£ç ã

ä½ è¦åè¯æï¼

> âä»ä¹é¬¼ä¸è¥¿ï¼Antoineï¼è¿ä¸æ¯æä»¬ç¬¬ä¸æ¬¡ä½¿ç¨ä¸å¤ªå¥½ç polyfill æ¥ä½¿ç¨ EcmaScript çæ°åè½â

# è¶

å¥½çï¼ä½æ¯è¿æ¬¡æä»¬ä»ç¶å¯ä»¥è±å åéçæ¶é´æ¥æåºä¸ä¸ªæ´ç®åç polyfill å®ç°ãè¿ä¸ªè§£å³æ¹æ¡å¨ä¸ä¸ª web å¼åäººååæçäººçä¸çéæ æ³çæ­£åºç¨ã

æä»¬æ¥çç`lodash.get`æ¯å¦ä½å·¥ä½çã [Github é¾æ¥](https://github.com/lodash/lodash/blob/master/.internal/baseGet.js)T3ã

```
import castPath from './castPath.js'
import toKey from './toKey.js'

/**
 * The base implementation of `get` without support for default values.
 *
 * @private
 * @param {Object} object The object to query.
 * @param {Array|string} path The path of the property to get.
 * @returns {*} Returns the resolved value.
 */
function baseGet(object, path) {
  path = castPath(path, object)

  let index = 0
  const length = path.length

  while (object != null && index < length) {
    object = object[toKey(path[index++])]
  }
  return (index && index == length) ? object : undefined
}

export default baseGet 
```

Enter fullscreen mode Exit fullscreen mode

å¾ææ(å¾ç´§å)ãå¦æå¯éé¾æ¥ç**èåå¡«åæ¶åå°å`lodash.get`è¿æ ·çå®ç¨å½æ°çåºç¨ä¼ææ ·ï¼**

ä½ å¯¹æ­¤æä»ä¹çæ³ï¼

å¦æä½ åæ¬¢è¿ç¯æç« ï¼è¯·éæåäº«åååºã