# ðåå»º React usePosition()æé©ä»¥è·åæµè§å¨çå°çä½ç½®

> åæï¼<https://dev.to/trekhleb/creating-react-useposition-hook-for-getting-browser-s-geolocation-jdi>

### TLï¼éåº¦ä¸è§å½¢å®ä½æ³(dead reckoning)

å¨æ¬æä¸­ï¼æä»¬å°åå»ºä¸ä¸ª React *usePosition()* é©å­æ¥è·åå¹¶è·è¸ªæµè§å¨çä½ç½®ãæä»¬å°ä½¿ç¨å¨å±å¯¹è±¡ *navigator.geolocation* æä¾ç[*getCurrentPosition*](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation/getCurrentPosition)å [*watchPosition*](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation/watchPosition) å½æ°ã *usePosition()* é©å­çæç»çæ¬æ¯ [***åå¸å¨ GitHub***](https://github.com/trekhleb/use-position) å[***ãNPMã***](https://www.npmjs.com/package/use-position)ä¸ï¼åå¤å¥½è¢«ä½ çåºç¨æ¶è´¹ã

### ä¸ºä»ä¹æä»¬å¯è½éè¦ usePosition()é©å­

[React é©å­](https://reactjs.org/docs/hooks-intro.html)çä¼å¿ä¹ä¸æ¯*è½å¤åç¦»å³æ³¨ç¹*ãæä»¬å¯ä»¥ä¸ä½¿ç¨ç¶æå¯¹è±¡ï¼æ¯å¦å°çä½ç½®**å**å¥æ¥å­è¿æ¥ï¼èæ¯å®å¨é¿åä½¿ç¨ç¶æï¼åªä½¿ç¨ä¸¤ä¸ªä¸åçé©å­æ¥ä¸ºæä»¬å¤çç¶æç®¡çãæ­¤å¤ï¼æä»¬å¯ä»¥å°è¿ä¸ªé»è¾åæä¸¤ä¸ªç¬ç«çé©å­ï¼èä¸æ¯å¨åä¸ä¸ª*componentidmount()*åè°ä¸­å¯å¨æµè§å¨ä½ç½®çè§å¨**å**æå¼å¥æ¥å­è¿æ¥ãè¿ç»äºæä»¬æ´å¹²ååæ´æç»´æ¤çä»£ç ã

### æä»¬å°å¦ä½ä½¿ç¨ Position()é©å­

è®©æä»¬åä¸äºéåå·¥ç¨ï¼æ³è±¡æä»¬å·²ç»å®ç°äºä¸ä¸ª *usePosition()* é©å­ãä¸é¢æ¯æä»¬å¯è½æ³è¦ä½¿ç¨å®çæ¹å¼:

*â ï¸ä¸é¢ææçä»£ç ç¤ºä¾é½ä½¿ç¨äºéè¯¯çç¬¦å·=â»ï¼èä¸æ¯= >ä½ä¸º lambda å½æ°çæ³¨éãå¨å¤å¶ä»£ç å¹¶å°è¯æ§è¡å®æ¶è¦æ³¨æè¿ä¸ç¹ï¼å ä¸ºå®å¯è½ä¸èµ·ä½ç¨ãè¯·å¨æ¨çä»£ç ä¸­ç¨>æå¨æ¿æ¢â»ãåºäºæç§åå ï¼æå¨ä»£ç åä¸­ç²è´´>ç¬¦å·æ¶éå°äºé®é¢ãæ±æ­ç»æ¨å¸¦æ¥ä¸ä¾¿ã*

```
import React from 'react';
import {usePosition} from './usePosition';
export const UsePositionDemo = () =â» {
  const {latitude, longitude, error} = usePosition();
  return (
    <code>
      latitude: {latitude}<br>
      longitude: {longitude}<br>
      error: {error}
    </code>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

ä½ çï¼åªæä¸è¡ç¨ *usePosition()* hook å¹¶ä¸ä½ å·²ç»æäºæ°æ®(*çº¬åº¦*å*ç»åº¦*)ãæä»¬è¿éçè³ä¸ç¨ *useState()* å *useEffect()* ãä½ç½®è®¢éåè§å¯å¨æ¸çå°è£å¨ *usePosition()* é©å­ä¸­ãç°å¨ï¼React å°ä¸ºæä»¬å¤çéç»ç»ä»¶é­æ¯ï¼æä»¬å°çå° *<ä»£ç >â¦</ä»£ç >* åä¸æ­æ´æ°æµè§å¨çææ°ä½ç½®å¼ãçèµ·æ¥å¾æ´æ´å¹²åã

### usePosition()é©å­å®ç°

æä»¬èªå®ä¹ç *usePosition()* hook åªæ¯ä¸ä¸ª JavaScript å½æ°ï¼å®ä½¿ç¨äºå¶ä»ç±»ä¼¼ [*useState()*](https://reactjs.org/docs/hooks-state.html) å [*useEffect()*](https://reactjs.org/docs/hooks-effect.html) çé©å­ãå®çèµ·æ¥ä¼åè¿æ ·:

```
// imports go here...
export const usePosition = () =â» {
  // code goes here...
} 
```

Enter fullscreen mode Exit fullscreen mode

æä»¬å°ä½¿ç¨ *useEffect()* é©å­æ¥é©ä½ç»ä»¶(å°ä½¿ç¨æä»¬çé©å­)è¢«æ¸²æçæ¶å»ï¼å¹¶è®¢éå°çä½ç½®çååãæä»¬è¿å°ä½¿ç¨ *useState()* é©å­æ¥å­å¨*çº¬åº¦*ã*ç»åº¦*å*éè¯¯*æ¶æ¯(ä»¥é²ç¨æ·ä¸åè®¸æµè§å¨å±äº«å¶ä½ç½®)ãæä»¥æä»¬éè¦é¦åå¯¼å¥è¿äºé©å­:

```
import {useState, useEffect} from 'react';
export const usePosition = () =â» {
  // code goes here...
} 
```

Enter fullscreen mode Exit fullscreen mode

è®©æä»¬ä¸ºä½ç½®åéè¯¯åå§åä¸ä¸ªå­å¨:

```
import {useState, useEffect} from 'react';
export const usePosition = () =â» {
  const [position, setPosition] = useState({});
  const [error, setError] = useState(null);

  // other code goes here...
} 
```

Enter fullscreen mode Exit fullscreen mode

è®©æä»¬ä»å½æ°ä¸­è¿åä¸ä¸ªçæ³å¼ãæä»¬è¿æ²¡æå®ä»¬ï¼ä½æ¯è®©æä»¬è¿åå°ç®åä¸ºæ­¢çåå§å¼ï¼ä»¥ååå¡«åå®ä»¬:

```
import {useState, useEffect} from 'react';
export const usePosition = () =â» {
  const [position, setPosition] = useState({});
  const [error, setError] = useState(null);

  // other code goes here...
  return {...position, error};
} 
```

Enter fullscreen mode Exit fullscreen mode

è¿æ¯æä»¬é©å­çä¸ä¸ªå³é®é¨åââè·åæµè§å¨çä½ç½®ãæä»¬å°å¨ç»ä»¶æ¸²æåæ§è¡æåé»è¾(useEffect hook)ã

```
import {useState, useEffect} from 'react';
export const usePosition = () =â» {
  const [position, setPosition] = useState({});
  const [error, setError] = useState(null);

  // callbacks will go here...
  useEffect(() =â» {
    const geo = navigator.geolocation;
    if (!geo) {
      setError('Geolocation is not supported');
      return;
    }
    watcher = geo.watchPosition(onChange, onError);
    return () =â» geo.clearWatch(watcher);
  }, []);
  return {...position, error};
} 
```

Enter fullscreen mode Exit fullscreen mode

å¨ useEffect() hook ä¸­ï¼æä»¬é¦ååä¸äºæ£æ¥ï¼ççæµè§å¨æ¯å¦æ¯æ *navigator.geolocation* ãå¦æä¸æ¯æå°çå®ä½ï¼æä»¬å°è®¾ç½®ä¸ä¸ªéè¯¯å¹¶ä»ææä¸­è¿åãå¦ææ¯æ *navigator.geolocation* ï¼æä»¬å°éè¿æä¾ *onChange()* å *onError()* åè°æ¥è®¢éä½ç½®åå(æä»¬ç¨åå°æ·»å å®ä»¬)ãæ³¨æï¼æä»¬ä» *useEffect()* è¿åä¸ä¸ª lambda å½æ°ãå¨ lambda å½æ°ä¸­ï¼ä¸æ¦ç»ä»¶è¢«å¸è½½ï¼æä»¬å°±æ¸é¤è§å¯å¨ãå æ­¤ï¼è¿ä¸ªè®¢é/åæ¶è®¢éé»è¾å°ç±æä»¬ç *usePosition()* é©å­å¨åé¨å¤çï¼æ¶è´¹èä¸å¿æå¿ã

ç°å¨è®©æä»¬æ·»å ç¼ºå¤±çåè°:

```
import {useState, useEffect} from 'react';
export const usePosition = () =â» {
  const [position, setPosition] = useState({});
  const [error, setError] = useState(null);

  const onChange = ({coords}) =â» {
    setPosition({
      latitude: coords.latitude,
      longitude: coords.longitude,
    });
  };
  const onError = (error) =â» {
    setError(error.message);
  };
  useEffect(() =â» {
    const geo = navigator.geolocation;
    if (!geo) {
      setError('Geolocation is not supported');
      return;
    }
    watcher = geo.watchPosition(onChange, onError);
    return () =â» geo.clearWatch(watcher);
  }, []);
  return {...position, error};
} 
```

Enter fullscreen mode Exit fullscreen mode

æä»¬ç»æäºãé©å­ *usePosition()* å¯ä»¥è¢«ä½¿ç¨ï¼å¹¶ä¸å®åªå°è£å°çä½ç½®ç¸å³çé»è¾ã

### ç¼åè®°

ä½ å¯ä»¥å¨ GitHub ä¸æ¾å°ä¸ä¸ª[æ¼ç¤º](https://trekhleb.github.io/use-position/)åæ´è¯¦ç»ç [usePosition()é©å­çå®ç°ãæå¸æè¿ä¸ªä¾å­å¯¹ä½ ææå¯åãç¼ç å¿«ä¹ï¼](https://github.com/trekhleb/use-position)