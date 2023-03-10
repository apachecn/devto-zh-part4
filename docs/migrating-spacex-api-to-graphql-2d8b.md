# å° SpaceX API è¿ç§»å° GraphQLð

> åæï¼<https://dev.to/swcarlosrj/migrating-spacex-api-to-graphql-2d8b>

ä½ä¸ºå¦ä¸ä¸ªç©çãå¤©æãç«ç®­çç±å¥½èï¼å½æåç° SpaceX å¬å± API(æè°¢[å¨æ é¿æ³¢ç½æç¨](http://apollographql.com/docs/tutorial/introduction.html)ð)ï¼æå¯¹è°æ¥å¯ç¨æ°æ®å¾æå´è¶£ã

æç«å³è®¿é®äº [SpaceX API GitHub](https://github.com/r-spacex/SpaceX-API) å¹¶â¦

[![](img/9bda726d09c8e8978b9ff9d8650496d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QPncIGqZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3396/1%2AfrgOjBqsIBs4f8t6gBSECA.png)

*   ðµs å°æ¯æªæ¥ Animoji çæè·è¸ª UX æµè¯

ä¼æ¯ï¼å¤é¨ææ¡£ï¼é®éåï¼ï¼ðSpaceX API ä¹æå¼å§å¹¶ä¸é¡ºå©ï¼ä½æä»ç¶å¯¹ææææ API çèµæºæå°å´å¥ï¼æç»§ç»­æçæ¹å¼å»[ææ¡£](https://documenter.getpostman.com/view/2025350/RWaEzAiG)ð¬

åå¾å¸¸ä¸æ ·ï¼æè·³è¿äºä»ç»ï¼ç´æ¥è¿å¥åèèµæï¼å¯»æ¾**å£æ¯**ã*æ°æ®åå®ä»¬åèªçç±»åå®ä¹*ï¼è®©æä»¬åé¡¾ä¸ä¸éé¢æä»ä¹ð

[![](img/fd03fc109ded538ba0a5f92e2c69c77d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V8JTShS---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2300/1%2A6Bz0dZavgjWvbqisAWSJCg.png)

â **ç»ç¹**ï¼ðv3ï¼ï¼æ v2 åï¼å»çå¨åªéï¼ð

â **ç±»å**ï¼æä¸ç¥éä»ä¹æ¯æ¥è¯¢å­ç¬¦ä¸²ï¼ä½æä»¬æä¸äºç±»åå®ä¹ð

ð²**æ°æ®**ï¼æ æ°æ®ï¼æ ð­ãæä»¬ç»§ç»­æ¾ï¼æ°æ®æ¨¡ååºè¯¥å°±å¨éè¿ï¼

ä½ä¸ºä¸ä¸ªä¹ æ¯ä½¿ç¨ GraphQL çç¨æ·ï¼è¿ç§ä½éªå¹¶ä¸å¤ªå¥½ï¼ä½å¼å¾ä¸è¯ð¤

[![](img/2b4203ec12af6df97937f40350c1ce83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DgruALEc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2296/1%2A3EIsYZy7ht0BJXsZ32NUJQ.png)

å°¤éå¡ï¼ææ¾å°äºð

âç­ä¸ä¸ï¼original_launch_unix çèµ·æ¥ä¸å¤ªçæï¼è®©æä»¬åæ£æ¥ä¸ä¸âç±»åå®ä¹âï¼
ð¤å®ä¸å¨é£éï¼æè¿°æ»æ¯è¯´âè¿æ»¤å¨â¦â
ðé£ä¸æ¯æ°æ®ç±»åå®ä¹ï¼
ð«é£æ¶ä»ä»¬ä¼å¨åªéå¢ï¼

* *æ»å¨è¯å¾æ¾å°ç±»åå®ä¹â¦â¦**

* *æè:
* *ä¸è¯´
ä¸è¯´
ä¸è¯´
ä¸è¯´
ä¸è¯´
ä¸è¯´
ä¸è¯´
ä¸è¯´
ä¸è¯´

* *æ:** âä¿æå·éï¼è®©æä»¬ççè½å¦å¨æä¾ç API éåä¸­æ¾å°å®ä»¬â

**è·æ­¥[é®å·®éå](https://app.getpostman.com/run-collection/3aeac01a548a87943749)â¦â¦**

[![](img/8f8c38edf62646c5c7cf7552dcd95cb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vk-awbsl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Ari-tQl6i7oUMuDjAl6vblA.png)

**å®è£ãæ³¨åãè®¾ç½®â¦**

* *æè:
* *ä¸è¯´
ä¸è¯´
ä¸è¯´
ä¸è¯´
ä¸è¯´
ä¸è¯´
ä¸è¯´
ä¸è¯´
ä¸è¯´

* *æ:** âç¨ GraphQL è¿ç­ä»ä¹ï¼â

æ¯çï¼ææ¾å¼äºðï¼å°±å¨é£ä¹åï¼æå¼å§äº API è¿ç§»â¦

## ä¸ºä»ä¹ï¼

å ä¸ºï¼

> # I just want to explore the data & and their types

ä¸éè¦è®¾ç½®ä»»ä½ä¸è¥¿ï¼å ä¸ºâ¦

ð**è¿åº¦æå** : 90%çååºè¶è¿äºåºç¨ç¨åºçæ°æ®éæ±ï¼æåªæ³é®ææ³è¦ä»ä¹ï¼

ð**ä¸é»**:å¤ä¸ªè¯·æ±æ¯ä¸ä¸ªç³ç³çæ§è½è§£å³æ¹æ¡ï¼æåæ¬¢ä¸æ¬¡è¯·æ±æææ°æ®

ð**æå­**ï¼**ææ¡£**ï¼**çæ¬**ï¼* *çæç³»ç»ï¼** â¦â¾ï¼æ»æ¯æäºæ£æ

## å¦ä½ï¼

[![](img/ea6d7c7309d7e5ca9dc3fac0cdbfd337.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cn7KYGSq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/4408/1%2A01W2XwVEpRyg-3Ayt4FN5g.png)

## ä»ä¹ï¼

å®ä¸æ¯ä¸ä¸ªæ¡æ¶ï¼ä¹ä¸æ¯ä¸ä¸ªåºï¼å®æ¯ä¸ç§é¢å API ç* *æ¥è¯¢è¯­è¨* *ï¼ä½ä¸ºè§èæ¬èº«ï¼å®ä¸ä»»ä½ç¼ç¨è¯­è¨é½å¼å®¹ð«

GraphQL:ä¸ç§ç¨äº API çæ¥è¯¢è¯­è¨

ææ¡å®ð©âðð¨âðä¼èÂ·æ³¢å°åæ´&äºååæ¯Â·ç­åæ¯çãå­¦ä¹ å¾è¡¨ã

[![](img/43bc21a89ec6f6a295db9731dbd7be47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ephyM9OV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Au8MDB0zH-dDLCB82SSRwCQ.png)

## ððè¿ç§»ðð

å³äº GraphQL ææ¯äººå&çå·¥å·ï¼æè½è¯´äºä»ä¹å¢ï¼ç®åå°è¯´ï¼æå·²ç»é¨ç½²äºä¸ä¸ª APIï¼è½å¤äº¤äºå°è·åçå®æ°æ®&æ¢ç´¢å®å¨æ´æ°çææ¡£(ä»¥åæ´å¤)*ï¼* *å¨å ä¸ªå°æ¶å* *ï¼éå¸¸æè¶£&ï¼

## æ­¥éª¤

1ï¸â£è®¾ç½®æå¡å¨åæ¨¡ååæ¨¡å¼âï¸
2ï¸â£å°æ°æ®åº&æå»ºå¨åå«å°ä¸ä¸æä¸­
3ï¸â£åå»ºåçç±»åå®ä¹&è§£æå¨ðª
4ï¸â£å®è¡[çç¢çè§£å³åæ³](https://graphql.org/learn/execution/#trivial-resolvers)t5ã5ï¸â£å¸åæè®­ð¡
6ï¸â£é¨ç½²ðå®

çèµ·æ¥ä¸é¾ï¼å¯¹å§ï¼ï¼å®éä¸è¿å¹¶ä¸æ¯å ä¸ºåºè²ç GraphQL ç¤¾åºðï¼[ä¸è¦éè¿æåçæ¹å](https://github.com/r-spacex/SpaceX-API/compare/master...swcarlosrj:graphql)ï¼

## ç»è®º

èªå·±ç»â¦â¦(åå¼å®ï¼æç ´å®ï¼æ¹è¿å®ï¼ä½ç¹å«æ¯ï¼äº«åå®ð)

> # T2] Buy back [T3ã API ãT4] document & everything

[https://codesandbox.io/embed/yv004pqnq9](https://codesandbox.io/embed/yv004pqnq9)

[![](img/c5ce5801aec5c7d14617a1b50036d15b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3M8UvU2v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3840/1%2Af6IMUipNmjSrEp9iEAX4sw.jpeg)

### ä¸è¦éè¿åºäº SpaceX GraphQL API ç GraphQL æä½³å®è·µ&ç»è®º

> # T2] [Experience and lessons of migrating API to GraphQL](https://medium.com/@swcarlosrj/lessons-learned-migrating-apis-to-graphql-8a015d08b163)

è¯·èèä¸ä¸ðð»è·å°ãcontributâ»ï¸ing åæ²å°ðè¯¥æ­»çã