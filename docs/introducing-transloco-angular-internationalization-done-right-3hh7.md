# ðTransloco ç®ä»:è§åº¦å½éåè¿å±é¡ºå©

> åæï¼<https://dev.to/netanelbasal/introducing-transloco-angular-internationalization-done-right-3hh7>

æååè¡¨äº[netbasal.com](https://netbasal.com/introducing-transloco-angular-internationalization-done-right-54710337630c)

æä¸æ®µæ¶é´ï¼æä¸ç´å¨èèåå»ºä¸ä¸ª Angular i18n åºï¼å®åå«äºæèæµ·ä¸­çä¸äºæ¦å¿µã

å å¨åï¼æå¾æ èðï¼æä»¥æå³å®æ¯æ¶åä¸çè°é©ï¼ç»§ç»­åå»ºä¸ä¸ªå¥å£®çåºäºï¼éé¢è£æ»¡äºæææä»è¿æ ·ä¸ä¸ªåºä¸­è·å¾çææç¹æ§ã

ææåäºæ²åå°Â·å¡æå¹åä¼æ³°Â·å¥¥å¾·æ¥å®æè¿é¡¹ä»»å¡ï¼æä»¬ä¸èµ·å¨å·¥ä½æ¥çæä¸åªåå·¥ä½ï¼è¿å°±æ¯ Transloco çè¯çã

æè¿åå»ºäº ng-neat ç»ç»æ¥ä¿å­æææçå¼æºåºãç®åå®åªææ Translocoï¼ä½æä¹è®¡åè½¬ç§»[æè§è](https://github.com/NetanelBasal/spectator)ã [ngx-until-destroy](https://github.com/NetanelBasal/ngx-take-until-destroy) ãngx-content-loader ä»¥åæå°æ¥åå»ºçä»»ä½å¼æº Angular åºã

è®©æä»¬çç Transloco æä¾äºä»ä¹ã

ä½¿ç¨ Angular CLI å®è£åº:

```
ng add @ngneat/transloco 
```

[![](img/4d3b4b28ddd113790f17a8da7eac4603.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YzGZMBA3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2600/1%2ARnSX-ybFkKsUYEawHDWJHQ.gif)

ä½ä¸ºå®è£è¿ç¨çä¸é¨åï¼æ¨ä¼éå°ä¸äºé®é¢ï¼ä¸æ¦ä½ åç­äºè¿äºé®é¢ï¼ææä½ éè¦çä¸è¥¿é½ä¼èªå¨ä¸ºä½ çæãè®©æä»¬ä»ç»çççæçæä»¶ã

é¦åï¼Transloco ä¸ºè¯·æ±çç¿»è¯åå»ºæ ·æ¿æä»¶:

assets/i18n/en.json

```
{  "hello":  "transloco en",  "dynamic":  "transloco {{value}}"  } 
```

assets/i18n/es.json

```
{  "hello":  "transloco es",  "dynamic":  "transloco {{value}}"  } 
```

æ¥ä¸æ¥ï¼å®å°`TranslocoModule`æ³¨å¥å°`AppModule`ä¸­ï¼å¹¶ä¸ºæ¨è®¾ç½®ä¸äºé»è®¤éé¡¹:

```
import { TRANSLOCO_CONFIG, TranslocoModule } from '@ngneat/transloco';
import { HttpClientModule } from '@angular/common/http';
import { httpLoader } from './loaders/http.loader';
import { environment } from '../environments/environment';

@NgModule({
  imports: [TranslocoModule, HttpClientModule],
  providers: [
    httpLoader
    {
      provide: TRANSLOCO_CONFIG,
      useValue: {
        prodMode: environment.production,
        listenToLangChange: true,
        defaultLang: 'en'
      }
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule {} 
```

è®©æä»¬è§£éä¸ä¸æ¯ä¸ªéç½®éé¡¹:

*   `listenToLangChange`:è®¢éè¯­è¨æ´æ¹äºä»¶ï¼åè®¸æ¨æ´æ¹æ´»å¨è¯­è¨ãå¨ä¸åè®¸ç¨æ·å¨è¿è¡æ¶æ´æ¹è¯­è¨çåºç¨ç¨åºä¸­ï¼è¿æ¯ä¸éè¦ç(ä¾å¦ï¼ä»ä¸æèåä¸­)ï¼å æ­¤å¨è¿äºæåµä¸ï¼éè¿å°å¶è®¾ç½®ä¸º falseï¼æ¨å¯ä»¥éè¿æ¸²æè§å¾ä¸æ¬¡å¹¶åæ¶è®¢éè¯­è¨æ´æ¹äºä»¶(é»è®¤ä¸º false)æ¥èçåå­
*   `defaultLang`:è®¾ç½®é»è®¤è¯­è¨
*   `fallbackLang`:è®¾ç½®é»è®¤è¯­è¨ä½ä¸ºå¤ç¨è¯­è¨
*   `failedRetries`:å¦æå è½½å¤±è´¥ï¼Transloco åºè¯¥éè¯å è½½ç¿»è¯æä»¶å¤å°æ¬¡(é»è®¤ä¸º 2 æ¬¡)

å®è¿å° httpLoader æ³¨å¥å°`AppModule`æä¾è:

```
import { HttpClient } from '@angular/common/http';
import { Translation, TRANSLOCO_LOADER, TranslocoLoader } from '@ngneat/transloco';
import { Injectable } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class HttpLoader implements TranslocoLoader {
  constructor(private http: HttpClient) {}

  getTranslation(langPath: string) {
    return this.http.get<Translation>(`/assets/i18n/${langPath}.json`);
  }
}

export const httpLoader = { provide: TRANSLOCO_LOADER, useClass: HttpLoader }; 
```

`HttpLoader`æ¯ä¸ä¸ªå®ç°`TranslocoLoader`æ¥å£çç±»ãå®è´è´£æå¯¼ transloco å¦ä½å è½½ç¿»è¯æä»¶ãå®ä½¿ç¨ Angular HTTP å®¢æ·ç«¯æ ¹æ®ç»å®çè·¯å¾è·åæä»¶ã

## æ¨¡æ¿ä¸­çç¿»è¯

Transloco æä¾äºä¸ç§ç¿»è¯æ¨¡æ¿çæ¹æ³:

### ä½¿ç¨ç»æåæä»¤

è¿æ¯æ¨èçæ¹æ³ãå®ç®æ´é«æï¼å ä¸ºå®ä¸ºæ¯ä¸ªæ¨¡æ¿åå»ºä¸ä¸ªè®¢é:

```
<ng-container *transloco="let t">
  <ul>
    <li>{{ t.home }}</li>
    <li>{{ t.alert | translocoParams: { value: dynamic } }}</li>
  </ul>
</ng-container> 
```

### ä½¿ç¨å±æ§æä»¤

```
<ul>
  <li><span transloco="home"></span></li>
  <li>
    <span transloco="alert" [translocoParams]="{ value: dynamic }"></span>
  </li>
  <li><span [transloco]="key"></span></li>
</ul> 
```

### ä½¿ç¨ç®¡é

```
<span>{{ 'home' | transloco }}</span> 
<span>{{ 'alert' | transloco: { value: dynamic } }}</span> 
```

## ç¨åºç¿»è¯

ææ¶æ¨å¯è½éè¦ç¿»è¯ç»ä»¶ææå¡ä¸­çä¸ä¸ªé®ãä¸ºæ­¤ï¼æ¨å¯ä»¥æ³¨å¥ TranslocoService å¹¶ä½¿ç¨å®ç translate æ¹æ³:

```
export class AppComponent {
  constructor(private service: TranslocoService) {}

  ngOnInit() {
    this.service.translate('hello');
    this.service.translate('hello', { value: 'world' });
    this.service.translate(['hello', 'key']);
    this.service.translate('hello', params, 'en');
    this.service.translate<T>(translate => translate.someKey);
  }
} 
```

## ð±ä½æ¯ç­ç­ï¼è¿ææ´å¤ï¼

è¿åªæ¯ Transloco çä¸ä¸ªå°è¯ï¼å®è¿æ¯æ:

*   ð´æ°æ§è£è½½
*   ðå¤ç§è¯­è¨
*   ð¥å¤æ¬¡åè½
*   ð¤æµè¯
*   ð¦å®å¨å¯å®å¶

æ¨å¯ä»¥å¨æä»¬çå®æ¹èªè¿°æä»¶ä¸­æ¾å°æ¨éè¦çææåå®¹åç¤ºä¾ã

## ä» ngx è¿ç§»-ç¿»è¯

Transloco æä¾äºä¸ä¸ª schematics å½ä»¤æ¥å¸®å©æ¨å®æè¿ç§»è¿ç¨ãæ¥çæ¯è¾[è¡¨](https://github.com/ngneat/transloco/#comparison-to-other-libraries)äºè§£æ´å¤ä¿¡æ¯ã

ç¿»è¯æå¿«ï¼å«å¿äºéè¿ä¸»æ¼æ¥è¡¨è¾¾ä½ çæ¬£èµï¼

â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸â­ï¸