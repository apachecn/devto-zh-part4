# ä¸è½½åðï¸è¿æ ·çåºç¨ç¨åº

> åæï¼<https://dev.to/samthor/pwas-that-download-like-apps-fd6>

è¿æ¯ä»å¤©çä¸ç¯ç­æã(éè¿åé£ä¸ªï¼ä¼è®©å®æççï¼)å®ä¹æ´åæ¯ä¸ç¯ç®ç­çå¼åèæ¥å¿ï¼èä¸æ¯æä¸ä¸ªç¹å®çç¹ð

æ¸è¿å¼ç½ç»åºç¨æ¯å½ä»ææç°ä»£æµè§å¨é½æ¯æçä¸è¥¿ãéè¿éè¯»è¿ä¸ªç½ç«ï¼ä½ æ­£å¨ä½¿ç¨ä¸ä¸ª:å¦æä½ æ­å¼äºèç½è¿æ¥ï¼ä½å è½½ [dev.to](https://dev.to) ï¼ä½ ä¼å¾å°ä¸ä¸ªå¯ç±çç¦»çº¿é¡µé¢ï¼ä½ å¯ä»¥å¨é£éæ¶é¸¦ãðï¸ð¨ð

ä¸ºäºæå»ºæå¡å·¥ä½èï¼PWA çæ ¸å¿é¨åï¼æ¨å¯è½æ³è¦ä½¿ç¨[å·¥å·ç®±](https://developers.google.com/web/tools/workbox/)ãä½æ¯å¦æ..ä½ ä¸ç¥éåï¼ð¤

# å±±å§ç Patented^ç½ç«å®è£æ¨¡å¼

æå°åå»ºä¸ä¸ªå ä¹ç©ºæ ä¸ç©çç½ç«æ¥âå®è£âå®æ´çä½éªï¼èä¸æ¯ä½ éå¸¸ä½¿ç¨ç PWA æ¹æ³ââç¼åä¸äºé¡µé¢åèµæºï¼ç¼åä¸ä¸ª SWï¼ç¶å*ç¼å­*è¿äºç¸åçé¡µé¢åèµæºã

è¿ç§å®æ´çä½éªå®éä¸å°æ¯ä¸ä¸ªå­æ¾å¨å¶ä»å°æ¹ç`.tar`æä»¶ãæä»¬æ¥å®è£å§ï¼ðð¥ï¸

## åå»ºä¸ä¸ªå®éç«ç¹

æä»¥ï¼è¦åå°è¿ä¸ç¹ï¼ä½ éè¦ä¸ä¸ªçæ­£çç½ç«ãåå»ºä¸ä¸ªåä¸º`app.tar`çæä»¶ï¼åå«å®çèµæº:`index.html`ãæ ·å¼ç­ã

## å¯å­å¨è½¯ä»¶

å¨æä»¬çåå°é¡µé¢`index.html`ä¸­ï¼æä»¬åå¹³å¸¸ä¸æ ·æ³¨åæä»¬çè½¯ä»¶:

```
<script>
if (!('serviceWorker' in navigator)) {
  throw new Error('unsupported');
}
navigator.serviceWorker.register('/sw.js').then((reg) => {
  console.info('registered');
  // TODO
});
</script> 
```

Enter fullscreen mode Exit fullscreen mode

æä»¬åªéè¦è¿ä¸ªæä»¶åä¸é¢ç`sw.js`æ¯ç± HTTP æå¡å¨æå¡ç*çæ­£ç*ã

## å®è£å¤çå¨

å¨`sw.js`éé¢ï¼æä»¬å¯ä»¥è¿æ ·å:

```
self.addEventListener('install', (ev) => {
  const p = (async() => {
    const response = await fetch('app.tar');
    const buffer = await response.arrayBuffer();

    const cache = await caches.open('app');
    const ops = [];
    untar(buffer, (file) => {
      if (file.name.endsWith('/')) {
        return;  // directory, ignore
      }
      const p = cache.put(file.name, new Response(file.buffer));
      ops.push(p);
    });
    await Promise.all(ops);
    // untar is a modified version of https://github.com/InvokIT/js-untar
  })();
  ev.waitUntil(p);
}); 
```

Enter fullscreen mode Exit fullscreen mode

å¤ªå¥½äºï¼æä»¬ç°å¨å·²ç»ä¸è½½äº`app.tar`å¹¶å°å¶åå®¹å®è£å°æä»¬çç¼å­ä¸­ãå®å¯ä»¥åå«æä»¬åæ¬¢çä»»ä½åå®¹ï¼å¹¶ä¸ä¸éè¦æ å°å°ä½ *å®éä¸*éè¿ HTTP æä¾çæä»¶ã

## è·åå¤çç¨åº

æå·®ç¹å¿äºãæä»¬éè¦ä½¿ç¨`sw.js` :
ä¸­çæ ·æ¿æä»¶ä»ç¼å­ä¸­æä¾æå¡

```
self.addEventListener('fetch', (ev) => {
  const p = (async() => {
    // TODO: make requests for '/index.html' match '/'
    const response = await caches.match(ev.request, {ignoreSearch: true});
    return response || fetch(ev.request);
  })();
  ev.respondWith(p);
}); 
```

Enter fullscreen mode Exit fullscreen mode

(è¿å¯¹äºå ä¹æææè½¯ä»¶çç«ç¹æ¥è¯´é½æ¯ä¸æ ·çã)

# ä¸è¦å¨å®¶éå°è¯è¿ä¸ª

è¿ä¸»è¦æ¯ä¸ä¸ªå®éªï¼ççä»ä¸ä¸ª`.tar`æä»¶å®è£ä¸ä¸ªç½ç«æ¯å¦å¯è¡ãæ¯çï¼å®æ¯ï¼ç°å¨ä½ ä¹å¯ä»¥å¨ç½ä¸äº«åå®è£åºç¨ç¨åºçå®æ´ä½éªäºãð

è¿éæä¸ä¸ªæ¼ç¤ºï¼

Twelve