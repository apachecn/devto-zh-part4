# ð®ä½ åå®äºå°±å®äºäºã

> åæï¼<https://dev.to/chiangs/git-r-done-when-you-re-done-p5b>

*æåæ¬¡ç­å¾æçåºç¨ç¨åºè¿è¡æµè¯åæå»ºï¼æä»¥ææ³æåºè¯¥ååä¸ä¸ª quickieã*

 *ä¹åæå±ç¤ºäºå¦ä½ä¸ºç´§æ¥æåµç¼åä¸ä¸ª git å½æ°ï¼æéè¦ [GTFO](https://dev.to/chiangs/in-case-of-fire-gtfo-30g4) ã

ä»å¤©ï¼ææ³åä½ å±ç¤ºä¸ä¸ªæ´å¸¸è§çè¿ç¨ï¼å¦ææä»¬ç¬èªæå¨å°å¢éä¸­å·¥ä½ï¼æä»¬é½å¯è½ä¼è¿æ ·åã

å½æå®æä¸ä¸ªåè½åæ¯æ¶ï¼éå¸¸ä¼ç»åä»¥ä¸æ­¥éª¤:

*(åè®¾æå¨æçç®å½çé¡¶å±ï¼å¹¶ä¸æè¿å»åè¿`git push origin head -u`)*

```
> {feature-branch} git add .
> {feature-branch} git commit -m "Awesome commit msg"
> {feature-branch} git push
> {feature-branch} git checkout develop
> {develop} git merge -
> {develop} git push 
```

Enter fullscreen mode Exit fullscreen mode

å¦æææ³å¨æ¬å°åè¿ç¨æ¸çåå¹¶çåæ¯ï¼æä¹å¿é¡»è¿æ ·å:

```
> {develop} git branch -d feature-branch
> {develop} git push origin :feature-branch 
```

Enter fullscreen mode Exit fullscreen mode

***é£å¯æ¯å¥½å¤æ­¥éª¤ã*T3ã**

ä¸é¢æ¯æå¦ä½è®©`git done`å`git donep`åå¾æ´ç®åï¼æå®éä¸ä¸º`git`è®¾ç½®äºä¸ä¸ªå«å`g`ï¼æä»¥ä»ç°å¨å¼å§æå°ä½¿ç¨å®:

æå°è¿å¥æç`.gitconfig`å¹¶åå»ºä¸¤ä¸ªæ°çå«åï¼å¦ä¸æç¤º:

```
[alias]
  done = ""
  donep = "" 
```

Enter fullscreen mode Exit fullscreen mode

ä½¿ç¨ä¸æç`g tfo`å½æ°ç¸åçè¯­æ³ï¼æå°é¦ååå»º`done`ï¼å®æ§è¡ä»¥ä¸æä½:

1.  åå¹¶åæ¯
2.  å é¤æ¬å°åæ¯
3.  å é¤è¿ç¨åæ¯

é£ä¸ªå½æ°çèµ·æ¥æ¯è¿æ ·ç:

```
done = "!f() { git merge $1 && git branch -d $1 && git push origin :$1; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

ä½ ä¼æ³¨æå°`$1`ãè¿æ¯å¯¹ä¸ä¸ªåéçå¼ç¨ï¼å½è°ç¨å½æ°æ¶ï¼è¿ä¸ªåéå°è¢«ä¼ å¥ï¼èè¿ä¸ªå½æ°æ°å¥½æ¯åæ¯åç§°ãç°å¨ï¼æå·²ç»å°è¯ä½¿ç¨äº`-`å`@{-1}`ï¼ä½æ¯å é¤å½æ°çè¿ç¨åæ¯é¨åä¸ä¼æ¥åå®ä»¬ä½ä¸ºå¯¹æåä½¿ç¨çåæ¯çå¼ç¨ãå æ­¤ï¼é¤éè¿éçè¯»èæä»ä¹æ³æ³ï¼å¦åæåªè½å·ä½å°ä¼ å¥ç¹æ§åæ¯åç§°ï¼è¿å¨æç§ç¨åº¦ä¸ä¹ä½¿å®æ´æäºéç¨ï¼ä½æ¯å¦æåæ¯åç§°å¾å¤ææèå¾é¾è®°ä½ï¼è¿å°±æç¹éº»ç¦äºãä½è³å°ï¼ä½ ä»ç¶åªéè¦è¾å¥ä¸æ¬¡ï¼

ð¤·ââï¸ ***é£ä¹æä»¬å¦ä½ä½¿ç¨å®å¢ï¼ãT2***

```
> {develop} g done feature-branch 
```

Enter fullscreen mode Exit fullscreen mode

ð¤¯å£ãå°±æ¯è¿æ ·ã

ä½æ¯å¯¹äºè¿ä¸ä¸ªï¼æä»¬æ²¡ææ¨å¨å¼åè¿ç¨çæ¹åãè¿å°±æ¯`donep`çç¨æ­¦ä¹å°ã

```
donep = "!f() { git merge $1 && git branch -d $1 && git push origin :$1 && git push; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

```
> {develop} g donep feature-branch 
```

Enter fullscreen mode Exit fullscreen mode

***æ³¨æ:*** è¿ä¸ªåè½ä¹åè®¾ä½ å·²ç»åäº`git push origin head -u`å¹¶è®¾ç½®äºä¸æ¸¸ãå¦æè¿æ²¡æä¹ä¸æ³è¦ï¼å¯ä»¥å°åè½ä¿®æ¹ä¸º:

```
donep = "!f() { git merge $1 && git branch -d $1 && git push origin :$1 && git push origin $2; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

ç¶ååè¿æ ·è¿è¡:

```
> {develop} g donep feature-branch develop 
```

Enter fullscreen mode Exit fullscreen mode

ð±âð¤ ***Git r' doneï¼*T3ã**

å¦æä½ è§å¾è¿å¾æä»·å¼ï¼è¯·çä¸è¯è®ºå¹¶å³æ³¨æç[dev . to @ Chiang](https://dev.to/chiangs)å [Twitter @chiangse](https://twitter.com/chiangse) ï¼ð»å¹²æ¯ï¼*