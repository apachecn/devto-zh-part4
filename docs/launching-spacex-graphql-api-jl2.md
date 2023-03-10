# å¯å¨ SpaceX GraphQL APIð

> åæï¼<https://dev.to/swcarlosrj/launching-spacex-graphql-api-jl2>

åç°ð¯åå»ºçäº§å°±ç»ªåç°ä»£ API çå·¥å·åç»éªï¼åºäºðä¸ºäººç±»èçï¼

[![Next destination: [SpaceX Land](https://spacex.land) ð](img/39cc9ecc96aab8af739906c25efbd023.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--_9NfY8Gx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3840/1%2AixnzMP6t4Iot9nZpBmr1Vw.png) *ä¸ä¸ä¸ªç®çå°: [SpaceX Land](https://spacex.land) ð*

> # Do you have your own â¦
> 
> # How high is the rocket? How many launches have landed successfully? What is the maximum crew capacity of the space capsule?
> 
> # If you do, I may have good news for you!

### ð¥æå¾é«å´å°å®£å¸â¦

> # [SpaceXðGraphQL API](https://api.spacex.land/graphql) ç°å·²ä¸çº¿ð

[![](img/b5a4bdb0d5558a2691c67f7288fe79a4.png)](https://camo.githubusercontent.com/532c82955f92f382873cb9c31801703d4141c0ca/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f3273594432464d4f5a63373868396a4666372f67697068792e676966)

> # An API is used for
> 
> # Technicians
> 
> # & No technicians
> 
> # come from anywhere
> 
> Use any equipment
> 
> # Also,
> 
> # are those little dreamers

## æå¼é¾æ¥&å°½æäº«åä¸çå®ç SpaceX æ°æ®â¨äºå¨å§

> # Discover all the interesting details about capsule, core, landing pad, launch, mission, payload, rocket, ship &. You can even check that Elon's Roadster has finally arrived at Mars!

**å¨è¿å¥ææ¯ç»èä¹åï¼è®©æå¯¹ 90%ä¸ç¥éä»ä¹æ¯ [API](https://en.wikipedia.org/wiki/Application_programming_interface) çäººæ¸©åä¸ç¹ï¼åªæ¯è®²ä¸ä¸ªè½¶äºâ¦â¦**

å¨ä¼ ç»ç 2018 å¹´ðå£è¯æé¤æ¶ï¼æåæçç©çå­¦çç­åå­ååè°è®ºææ­£å¨åçå³äº SpaceX API çå·¥ä½(*ä»ä»æªå¬è¯´è¿ä»ä¹æ¯ APIï¼ä½ä»ç¡®å®åæ¬¢ç«ç®­çä¸è¥¿ï¼æä»¥æ¿æå¬*)ï¼ææ¿èµ·æçð»ï¼æå¼æµè§å¨ï¼æå ä¸ªé®ï¼æä»¬å°±å¾å°ææ SpaceX ç«ç®­çåå­ï¼æè¿°&åå¨æºçç»èï¼ä»æè®¶äºè¿æ¯å¤ä¹å®¹æ(*ç°å¨ä½ çè³å¯ä»¥ç¨ä½ çð±ï¼ç®ç´ä»¤äººæä¹±æè¿·*ã

å¨é£ä¹åï¼æ¯çï¼å¨æ¥ä¸æ¥çä¸ä¸ªå°æ¶éæä¸¢å¤±äºæçç¬è®°æ¬çµèðä»ç¥éå¾å¤å³äºæ¨è¿å¨çç¥è¯&å æ­¤ï¼ä»å¼å§æ¢ç´¢ APIï¼èæå¨ç©å°çãå¨æä¸ªæ¶åï¼ä»æ¿æ¥äºä¸äºåå°å¾åï¼æ 5 å²çè¡¨å¼çè¯å°é®ææ¯å¦å¨éé¢ð(æåæ¬¢é£äºèå§ççæ´»è§è§ï¼ä»ä»¬ç¸ä¿¡ä¸åçæå¯è½)ãå°é£æ¶ï¼è¿å·²ç»åçäºï¼ç±äºè¿ç§**å¯è®¿é®çææ¯** &å®ä»¬ä»¤äººé¾ä»¥ç½®ä¿¡çåè½ï¼æä»¬ä¸ä¸ªäººè±äºæä¸çä¸é¨åæ¶é´æè®¶äºç«ç®­ä»»å¡ãå¤ªç©ºè±&åå¶ä» SpaceX æ°æ®ï¼

> # Moral: Let's build accessible software to continuously stimulate people's passion & dreams!

[![For humans & everyone else ð½](img/16ee6fec8c3d6e3eb11b27dd9b93b7b5.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--TUE540Rc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AuiMzAvGBwBhU5tNYnvJcrQ.jpeg) *å¯¹äºäººç±»&å¶ä»äººð½*

éæ³¨:é£ä¸ªå°å®¶ä¼å·²ç»æ¯ææ´äºè§£ SpaceX äº(å ä¸ºä»è¿ä¸ªæä¸ç´å¨ç© API)ãæå¾ðåªæ¯å ä¸ºä»å¾æ³ç¥éã

## å¼åèç©ºé´

å¥½äºï¼ç°å¨ï¼æä»¬æ¥æ·±å¥æ¢è®¨ææ¯ç»èãå ä¸ºæåä¿¡**å¯è®¿é®æ§** & a great **U/DX** æ¯è½¯ä»¶è¢«éç¨çåºæ¬è¦ç´ ï¼æä»¥ä½ å¯è½å¿é¡»å¤çå¶ä»é®é¢ï¼å°±åæåç°ç°æç SpaceX API å®ç°æ¶ä¸æ ·â¦

### å¨æº

å½ææ¾å°å®æ¶ï¼æççå¾å´å¥å°æ¥çäºå¯ç¨çæ°æ®ï¼ä½æ¯â¦å åéåæå·²ç»**å¤ççæ¬ãææ¡£åç¬¬ä¸æ¹ SWs**ï¼åºæ¬ä¸ï¼ææ¾å¼äºï¼ç¶åæå³å®å¼å§è¿ç§»ï¼

å¨è¿ç¯æç« ä¸­ï¼ä½ ä¼åç°åçè¿ç§æåµçä¸»è¦åå ã
[**å° SpaceX API è¿ç§»å° GraphQLð**
*ä½ä¸ºå¦ä¸ä¸ªç©çãå¤©æãç«ç®­çç±å¥½èï¼å½æåç° SpaceX å¬å± API(æè°¢å¨æ  Apolloâ¦*medium.com](https://medium.com/open-graphql/migrating-spacex-api-to-graphql-e1fe69a3a8e7)

### å¥½å¤

å³äº GraphQL & REST APIs çä¼ç¼ºç¹ï¼ä¸å®¶ä»¬å·²ç»åäºå¤§éçæç« ãæä¸æç®å¨è¿ééå¤è¿äºï¼ä½æå®å¨é¼å±ä½ å»ç ç©¶å®ï¼ä½ ä¹å¯ä»¥æ¥ç GraphQL å®æ¹ç½ç«æ¥äºè§£å®çä¸»è¦ç¹æ§ï¼
[**GraphQL:ä¸ç§ API çæ¥è¯¢è¯­è¨ã**
*GraphQL ä¸ºæ¨ç API ä¸­çæ°æ®æä¾äºå®æ´çæè¿°ï¼è®©å®¢æ·è½å¤åç¡®å°è¯¢é®ä»ä»¬æ³è¦ä»ä¹â¦â¦* graph QL . org](https://graphql.org/)

### å®ç°

è®©æä»¬è°è°*é¡¹ç®ç»æãç±»åå®å¨&å®å¨*å±ç¤ºä½¿ç¨çä¸»è¦å·¥å·&æä½³å®è·µ:

â«ï¸å®éµå¾ª**æ¨¡å¼ä¼å**çæ¹æ³ï¼ä½¿ç¨**é¢åæ¨¡åå* *æ¨¡å¼*** *ç»æã

[![](img/a7d4f74dc24ec8ba2e3823934f21ebb5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AKG6r-Iz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A4bDeJQj6EFlgntOqS52xrw.png)

è¯¥æ¨¡å¼ç±ä¸åç**æ°æ®éå** ( *è¶åãå¬å¸ãæ ¸å¿ãé¾ãåå²ãçéå¹³å°ãåå°ãåå°å°ãä»»å¡ãææè½½è·ãè·è½¦ãç«ç®­&é£è¹*)åå²ï¼è¿åå«**å¨å±**ç±»åå®ä¹(*ç±ä¸¤èç»æï¼å¬å±&åå§ GraphQL ç±»åå®ä¹*s)

ææè¿äºé½ç»åä¸ºä»¥ä¸å ç±»:

ð **typeDefs**
å®åå«ç±å³æ³¨ç¹æ¨¡ååç*æ°æ®éå*ç±»åå®ä¹*ã*

ð**è§£æå¨**
åè½è´è´£*è·å&è¿æ»¤*æéç*æ°æ®*ã

ð**ç±*è§£æå½æ°*ç»æç utils**
æè*è¿åæ°æ®*å¯è½éè¦çä»»ä½æä½ï¼

å³ä½¿ç¥éå½åç GraphQL API å®ç°å¹¶ä¸å¤ªå¤æï¼**å®å¯è½/å°ä¼å¢é¿**ï¼è¿æå³çæä»¬å¿é¡»**å³å¿æ¨¡åå**ä»¥é¿åä»£ç éæï¼å·²ç»è¯´è¿â¦

> # Break the pattern by focusing on the point rather than the type!

å®å¯ä»¥è®©ä½ è½»æ¾å°è¯å«åºä½ ç[çç¢è§£æå¨](https://graphql.org/learn/execution/#trivial-resolvers) &éç¨å¶ä¸­çé»è¾å½æ°ð

> # Divided by type

[![](img/a9d6969a874fb73c4bba252da0c6525f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZCaheWm9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2An1IiMijXeAlPBUc6yleHAg.png)

[![](img/4e614c3f653513e6604dc1abd1bcc5fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DtDZs1iL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AvJCg_LNA5_BgKJa5b-OLfw.png)

[![Even if youâd split your resolvers, you could mess everything up whenever youâd have to import functions from other data collections ð](img/51b8cfd04e6367286a1bb041abb40eda.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WfVCIobW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2308/1%2Ae81P9nP0P8qDHKVNnLG-mA.png) ð

> # Get attention

[![](img/a2e6d084d6dc21431c18bf5fa94dc6a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LY8a_0Dq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ASW93Mo9EDoCRCKV-EtHK-Q.png)

[![](img/fc38b5fec23edcb372fc9a2146ec682c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--slCM_hxu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ANxV6nYeBz6jZFsspdh9NWg.png)

[![Identify smartly your trivial resolvers & reuse their functions, itâd be a huge win if youâd have to scale in the future ð](img/1d1c3525c18b6c63746648aa47f3c9b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rXGyPCBN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ApxjOgS1Kbb3GOzJN_Rou9w.png) ð

â«ï¸æä¾**ç±»åå®å¨**èªå¨çæ typedef&è§£æå¨**ç±»åèæ¬**ç±»åã

å°æä»¬ç GraphQL æ¨¡å¼ä½ä¸º**çå®çåä¸æ¥æºï¼* *æä»¬å¯è½æ³è¦* *èªå¨çæ**æä»¬çææç±»åï¼ä»¥ä¾¿å¨æ´ä¸ªåºç¨ç¨åºä¸­ä¿ææ°æ®ç»æåæ­¥ãè¿å°±æ¯ [**GraphQL ä»£ç çæå¨](https://graphql-code-generator.com/) **(ç± [Dotan Simha](https://dev.toundefined) åå»º)çç¨æ­¦ä¹å°ï¼
[**GraphQL ä»£ç çæå¨*éè¿ä¸ä¸ªå½æ°è°ç¨***
graphql-code-generator.com](https://graphql-code-generator.com/)ä»æ¨ç GraphQL æ¨¡å¼çæä»£ç 

> # It generates all typedefs & parser functions (including data returned by args &) and script types.

å¦ææ¨æ³äºè§£æ´å¤å³äº it å®æ½åä¼å¿çä¿¡æ¯ï¼è¯·ä¸è¦éè¿ä¸é¢çå¸å­ï¼
[**ç±»åå®å¨ç GraphQL æå¡å¨ð**
*è¿ç¯æç« å±ç¤ºäºå¨ GraphQL Servers*medium.com ä¸­æ·»å ç±»åå®å¨çå¨æº&æ­¥éª¤](https://medium.com/open-graphql/type-safe-graphql-servers-3922b8a70e52)

â«ï¸it ' s**protected**from**infinite **&**å¤æè¯·æ±* *æä¾èªå®ä¹éè¯¯æ¶æ¯ã

å° GraphQL è§ä¸ºä¸ä¸ªå·¨å¤§çç«¯ç¹ï¼å¶ä¸­æææ°æ®é½å¯ç¨ï¼æ²¡æéå½çä¿æ¤å¯è½ä¼å¯¼è´æ¼æ´ï¼ä¾å¦åå° DoS(æç»æå¡)æ»å»çå½±åã

ä½¿ç¨å[graph QL-éçéå¶](https://github.com/teamplanes/graphql-rate-limit)ã[graph QL-æ·±åº¦éå¶](https://github.com/stems/graphql-depth-limit)ã&ã[graph QL-éªè¯-å¤ææ§](https://github.com/4Catalyzer/graphql-validation-complexity)è¿æ ·çå·¥å·å¯ä»¥å¾å®¹æå°é¿åè¿ç§æåµ

å¦æä½ å¯¹ä¿è¯ä½ ç GraphQL API çå®å¨æå´è¶£ï¼ä¸è¦éè¿è¿ç¯é©¬åæ¯Â·æ¯æä¼ä¼¯çæç« ð¯å³äºä¸»è¦é®é¢çæ¦è¿°&å¶åèªçè§£å³æ¹æ¡ï¼
[**ä¿æ¤æ¨ç GraphQL API ååæ¶ææ¥è¯¢**
*ä½¿ç¨ GraphQL ä»¤äººæå¹ï¼ä½ä¹æå¤æçå®å¨éæ£ãè®©æä»¬æ·±å¥äºè§£ä¸äºå¿è¦çä¿æ¤æªæ½â¦â¦* blog . apollographql . com](https://blog.apollographql.com/securing-your-graphql-api-from-malicious-queries-16130a324a6b)

### æ°æ®

æ¨å¯è½æ³ç¥éæ°æ®æ¥èªåªéï¼å¨æ·±å¥ç ç©¶ä¹åï¼æ¨å¿é¡»ç¥ééè¿ API å¬å¼çæ°æ®â¦

> # is not the official data

è½ç¶ï¼**æ¯å ä¸ª SpaceX ä¿¡æ¯æºæä¾ççå®æ°æ®**ï¼å¶ä¸­* *ä¸»è¦* *æ¯ [Reddit SpaceX è®ºå](https://www.reddit.com/r/spacex/)

It [Wiki](https://www.reddit.com/r/spacex/wiki) é¨ååæ»¡äºç±ç¤¾åºæåæ°åçæ°æ®ï¼æ­¤å¤ï¼è¿ææ´å¤æ¥èªä»¥ä¸ç½ç«:

*   [https://www.spacex.com](https://www.spacex.com/webcast)

*   [https://www.space-track.org](https://www.space-track.org)

*   [https://ssd.jpl.nasa.gov](https://ssd.jpl.nasa.gov)

*   [https://www.marinetraffic.com](https://www.marinetraffic.com)

    > # All data from these platforms are parsed & and collected in a public Mongo database, from which information is obtained.

è¿é¡¹ä»¤äººé¾ä»¥ç½®ä¿¡çå·¥ä½æ¯ç±æåæ¬¢è¿çéå®æ¹ [SpaceX REST API](https://api.spacexdata.com/) (å·²ç»è¿è¡äºä¸¤å¹´)å®æçãææ³ä¸ºè¿æ ·çå·¥ä½ç»ä»ä»¬ä¸ä¸ªå¤§å¼å°å«ï¼è®©æä»¬æè½åè®¿é®ææè¿äºæè¶£çæ°æ®ã
[**r-SpaceX/SpaceX-API**ðéå¯¹ç«ç®­ãæ ¸å¿ãå¤ªç©ºè±ãåå°å°ååå°æ°æ®çå¼æº REST API](https://github.com/r-spacex/SpaceX-API)

æå®å¨é¼å±ä½ å¸®å©ä»ä»¬æåºæ°çåè½æä¿®å¤ç°æçéè¯¯ï¼ä»ä»¬ä¸å®ä¼ææ¿ä½ çï¼

## SpaceX graph QL API

ä½ å¯è½æ³è¦ðå¨ä»£ç åºä¸­ä½ å¯ä»¥æ¾å°æææå°çä¸»é¢çå®ç°ï¼å¨è¿éï¼

> # T2] Buy back [T3ã API ãT4] document & everything

[https://codesandbox.io/embed/yv004pqnq9](https://codesandbox.io/embed/yv004pqnq9)

## âï¼è¿ææ´å¤â¦

> # What if you don't want to use GraphQL? That's fair, but â¦ what if you could generate a REST API based on your GraphQL implementation to get all the benefits of both worlds? , does this make sense?

### ç¡®å®å¦æ­¤ï¼ä½ ä¼æ³å¨ä½¿ç¨&æ´é² REST çåæ¶è·å¾ GraphQL çææä¼ç¹ï¼æ¯å¦:

ðå®å¨ææ°çæçææ¡£ã
ðè¿è¡æ¶æ°æ®éªè¯* *ç¡®ä¿ç±»åå®å¨ãT3ãðä¸å**çè¦**çæ¬**ã
ðå¿è®°**å**æå¨è·¯ç±&æ§å¶å¨ã
âå¤äº

> # ç°å¨ï¼è¿æ¯å¯è½çð¤¯ï¼å°±ç¨ Sofa Rest(Kamil Kisiela çåæ)æ ¹æ®ä½ ç GraphQL æ¨¡å¼èªå¨çæä¸ä¸ª REST APIï¼
> 
> [ðæ²åââæå¥½çä¼æ¯æ¹å¼(å°±æ¯ GraphQL)ãå½»åºç»æ REST vs GraphQL çäºè®º](https://medium.com/the-guild/sofa-the-best-way-to-rest-is-graphql-d9da6e8e7693)

### è¯´å°è¿éï¼ð¥æä¹è¦å®£å¸â¦

> # [SpaceX REST API](https://api.spacex.land/rest/) ä¹ä¸çº¿äºð

[![](img/a37fafa7793ead3b8b50f22719a2c39a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b96_j9np--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Aox_Zf1Iqm-hn4x0IT-ag0A.gif)

## æå¼é¾æ¥&è¯è¯å¯ç¨çç«¯ç¹åºæ¥ð«

> # If you are a fan of REST API, you can get the data exposed through the recorded endpoints. In addition, you will be able to get all the mentioned benefits from GraphQL, for example, the latest typed documents!

å°½ç®¡å¦æ­¤ï¼å¦ææ¨è¿½æ±æ§è½ï¼æè¿æ¯å»ºè®®æ¨ä½¿ç¨ç°æç REST API ï¼

å¦æä½ å·²ç»å®ç°äºä¸ä¸ª GraphQL APIï¼**å¨å åéå**ä½ ä¹å¯ä»¥æä¸ä¸ª REST APIï¼ççä½ æ¯å¦ä½å®ç°å®çï¼

âªï¸ä½¿ç¨' *express* å¹¶éè¿æå¡å¨å±äº«ä½ ç**æ¨¡å¼**(ç±*typedef*&*è§£æå¨*ç»æ)ï¼ä»¥åä½ ç**ä¸ä¸æ**ï¼

âªï¸ä½¿ç¨ **sofa** & **OpenAPI** ï¼ä»¥ä¾¿åºäºæ¨ç *GraphQL å®ç°*çæä¸ä¸ª* REST API*ãæ¨å¯è½è¿æ³ä½¿ç¨' *swagger-ui-express* æ¥ä¸ºææ¡£æä¾æå¡ï¼

âªï¸ä½¿ç¨'*Apollo-server-express*'&å°'*app*express å®ä¾åºç¨å° GraphQL æå¡å¨ä¸ï¼

> # That's it! GraphQL rest and stop arguing & The best of both worlds!

## éå¸¸æ¬¢è¿å¬å³äººåð¤

è¿æå¾å¤å·¥ä½è¦åï¼ä»*éè¯¯å¤ç*ã*æµè¯*ç­ç­â¦â¦æå¾ä¹ææ¶å°ä»»ä½å»ºè®®/é®é¢ï¼ä»¥æ¹åç°æçï¼
[**spacexland/api** ðgraph QL&REST API æ¢ç´¢ç«ç®­ï¼åå°&å¶ä» SpaceX çæ°æ®*github.com](https://github.com/spacexland/api)

å¦å¤ï¼æä¼è®©ä½ ä»¬**ä¸ºæææå°çææ¯**ååºè´¡ç®ï¼ä»ä»¬å·²ç»å®æäºå¦æ­¤ä¸å¯æè®®çå·¥ä½ï¼ä½æå¾ç¡®å®ä»ä»¬ä¼æè°¢ä¸äºå¸®å©ï¼

## æé é·ðææ

åé ååäº«ä»æªå¦æ­¤ç®åð¯ç±äº Codesanbox [Ives van Hoorne](https://dev.toundefined) çåé ï¼web åºç¨ç¨åºé¡¹ç®ä½¿ç¨ä½ æåæ¬¢ç FE åºãæ è®ºæ¨æ¯å¦ä» **GraphQL** å¼å§ï¼è¯·ç»åºä¸ä¸ªðå¨ä¸åçå®ä¾ä¸­ï¼ä½ ä¼åç°æè¶£çç¨æ³ï¼

### â¤ï¸æ£±è§åæ

äºè§£å¦ä½å¨ MVVM æ¶æä¸­ä½¿ç¨ TypeScript ä¸ GraphQL API è¿è¡äº¤äºã

[https://codesandbox.io/embed/o9nmplk96y](https://codesandbox.io/embed/o9nmplk96y)

### ðååº

é©å­å·²ç»å¨è¿éäº(ä¸æ¯å¼ç©ç¬ï¼çç[è¿ä¸ª](https://github.com/facebook/react/pull/14679)),ä½ å¯ä»¥æå®ä»¬å GraphQL å®¢æ·ç«¯åºä¸èµ·ç¨ä½' [react-apollo-hook](https://github.com/trojanowski/react-apollo-hooks) 'ð¤©

[https://codesandbox.io/embed/r4p8q1kmvp](https://codesandbox.io/embed/r4p8q1kmvp)

### ðè§å¾(vue)

æ¨å¯è½æ³å­¦ä¹ å¦ä½ç¨æé·çæ¸è¿å¼ JavaScript æ¡æ¶ç¼åæ¥è¯¢åè·åæ°æ®ï¼

[https://codesandbox.io/embed/m3v8yv0nw9](https://codesandbox.io/embed/m3v8yv0nw9)

> # Choose the FE library of programming language & that you feel sure of, and arrange some to enjoy coding webapps in your calendar &!

å¦æèªå­¦è¿ä¸è¶³ä»¥æä¸ºå»ºé æäºä¸è¥¿ççç±ï¼ä½ å¯è½æ³ç¥éæ¥èªä¸çåå°çäººä»¬ðæ¿æä»ä½ çé¡¹ç®ä¸­å­¦ä¹ ï¼è¯´åºæ¥â¦

å¦æåéä¸è½è¯´æä½ ï¼é£å°±ä¸ºä»ä»¬å»åå§ï¼OSS ç¤¾åºå°ä¼éå¸¸ææ¿ä½ çå·¥ä½ï¼ä¸ç®¡ææ ·ï¼æå·²ç»ä¸ºæ¯ä¸ä¸ª SpaceX ä»£ç æ²ç®±å¶å®äºä¼å¤§çè®¡åï¼ç¹å«æ¯ð¡ä¼è®¡ä»¬ï¼å¸®æä¸æï¼

## å­¦å

é¦åï¼ææ³é¼å±ä»»ä½äººå¼å§ååå®¢ï¼ä½ å®å¨æè½åè¿æ ·åðªæå ä¸ªæåæå¼å§(æçè³æ²¡æåä¸ç¯åæ ·çè±è¯­)ï¼æå¯¹æ­¤æå°éå¸¸é«å´ï¼ä½ ä¼åå¾æ´å¥½ï¼è¿åªæ¯æ¶é´é®é¢ãåäº«ä½ çä¸åðï¼äººä»¬ä¼å­¦ä¹ ï¼åæ¬¢&è°¢è°¢ï¼

å¦æä½ åæä¸æ ·æ¯ GraphQL çç±å¥½èï¼æè¯å®ä¼æ¨èå¨ [Open GraphQL](https://medium.com/open-graphql) ä¸åè¡¨æç« ãè¿æ¯ä¸ä¸ªå¯¹ä»»ä½äºæé½å¼æ¾çåºçç©&ä»»ä½ç±[çº³å¾·Â·è¾¾æ¯ç¹](https://dev.toundefined)ç»è¥ç GraphQL ç¸å³çä¸åï¼ç»ä»åæ¶æ¯ï¼ï¼ä»è¯å®ä¼æ¿æè®©ä½ ä¸è¹çï¼

æ­¤å¤ï¼æè¿æ³å¯¹[å¬ä¼](https://medium.com/the-guild) [ä¹éæ](https://dev.toundefined)çåºéä¼ç¨ [GraphQL ä»£ç çæå¨](https://graphql-code-generator.com/)ã[ç­å·¥å·æ¯æ GraphQL ç¤¾åºè¡¨ç¤ºæè°¢ðæ²å](https://sofa-api.com/)ã [GraphQL æ¨¡å](https://graphql-modules.com/) & [GraphQL æ£æ¥å](https://github.com/kamilkisiela/graphql-inspector)é¤æ­¤ä¹å¤ï¼å®ççæ¹åäº DXï¼

æ»ç»ä¸ä¸ï¼å¸æä½ è½ä»è¿ç¯æç« ä¸­å­¦å°ä¸äºä¸è¥¿ï¼é¤æ­¤ä¹å¤ï¼è¯·ç»§ç»­ä¸ºä¸å¯æè®®ç OSS ç¤¾åºåè´¡ç®ð¤

[![](img/c5ce5801aec5c7d14617a1b50036d15b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3M8UvU2v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3840/1%2Af6IMUipNmjSrEp9iEAX4sw.jpeg)

## æä»¬æ­£å¨æ¥è¿ä¸ä¸ä¸ªç®çå°ðï¼æ¬è¯·æå¾ä¸è¦éè¿è½å°ï¼

è¯·èèä¸ä¸ðð»è·å°ãcontributâ»ï¸ing åæ²å°ðè¯¥æ­»çã