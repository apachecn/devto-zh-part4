# å©ä¼¯æðconâ¨ Fastlaneâ¨æ¬åååºåºç¨

> åæï¼<https://dev.to/javymb/libera-aplicaciones-de-react-native-con-fastlane-9g8>

ä½¿ç¨ React Native (JavaScript)åå»ºåºç¨ç¨åºå°±åç«¥è¯ä¸æ ·ä¸åé½æ¯ç²çº¢è²ç´å°åè¡æ°ççï¼ç¹å«æ¯å¦æä½ ä¸çæ iOS æ Android çæ¯è¯­ã
å App Store å Play Store ååºæäº¤ç³è¯·çè¿ç¨ï¼å¾å¾æ¯ä¸ä¸ªä»¤äººæ²®ä¸§ãç¼æ¢æç»æçç»åè½ç¶æå¾å¤æä»¶ï¼ä½å¹¶ä¸æ»æ¯å¾æ¸æ¥æåªæ¯éè¿äºè®¸å¤éè¦çæ­¥éª¤ï¼å ä¸ºè¿æå¾å¤äºæè¦åã

é£å°±æ¯ä»æ¥ææ´çæ¶åï¼
æ¬æå°åæ¨ä»ç»å¦ä½å° Android åºç¨ç¨åºçåå°è¿ç¨èªå¨åãâæå¸¸è§çä»»å¡ï¼å¦è¯ä¹¦ãåºç¨ç¨åºç¼è¯ãBeta ååç­ã

Fastlane æ¯ iOS å Android å¼åäººåçä¸ä¸ªå·¥å·ï¼å¯å¸®å©ä»ä»¬èªå¨æ§è¡ç¹ççä»»å¡ï¼å¦çæå±å¹æªå¾ãå¤çè¯ä¹¦åå¯å¨åºç¨ç¨åºã
çå®è¯è¯:

> âæå¼å§ä½¿ç¨ï¼æççæ´»æ¹åäºï¼ç°å¨æææ´å¤çæ¶é´å¨ä¹¦æ¡ä¸åæ¯åå¡æ¾æ¾ä¸ä¸ï¼åæ¶ Fastlane è´è´£ä¸åãð

## å¼å§

å®è£åï¼è¯·ç¡®ä¿å·²å®è£ææ°ç Xcode å½ä»¤è¡å·¥å·ï¼ç¶åå®è£

`brew cask install fastlane;`
å®è£åï¼å¨æ¨çæ¬æºæ ¹çº§ååºé¡¹ç®ååå»º fast lane/âæä»¶å¤¹ãç¶åï¼å¨æ­¤ç®å½ä¸­åå»ºä¸ä¸ªåä¸º Fastfile çæä»¶(ä¸å¸¦æ©å±åï¼ä» Fastlane)ã

Fastfile æä»¶æ¯æä»¬å°å¯¹è½¦é(lanes)è¿è¡ç¼ç çä½ç½®ãè½¦éåå«ä¸ç»å¨ä½ï¼æ¨å¯ä»¥åæ­¥æ§è¡è¿äºå¨ä½ä»¥èªå¨åæµç¨ãæä½æ¯æ§è¡ä»»å¡çå½æ°ã

è®©æä»¬ä»è¿ä¸ªåºæ¬æ¨¡æ¿ *Fastfile* å¼å§ï¼å¦æ¨æè§ï¼æä¸ä¸ª before_all é¾æ¥ï¼å®åºæ¬ä¸æ§è¡ç¶ææ£æ¥ï¼æä¸ä¸ªå¨ä½ï¼ä»¥ç¡®ä¿å®å¤äºæåä¸ä¸ªåæ¯(branch)ä¸­ï¼ç¶ææ¯å¹²åçã

```
fastlane_version '2.53.1'

before_all do
  ensure_git_branch
  ensure_git_status_clean
  git_pull
end

platform :ios do
   # iOS Lanes
end

platform :android do
  # Android Lanes
end 
```

Enter fullscreen mode Exit fullscreen mode

## è®¤è¯

å½æ¨è¦å¯å¨ä¸ä¸ªæ°çåºç¨ç¨åºæ¶ï¼è¿ä¸åé½æ¯å®ç¾çï¼ç´å°æ¨å¿é¡»ç­¾ç½²å¹¶æ§å¶åºç¨ç¨åºè¯ä¹¦ã

### iOSð

ç­¾ç½²ä½ çä»£ç çæå¥½æ¹æ³æ¯ä½¿ç¨ match å¨å° march æ´åå°ãè½¦éã(lane)ä¹åï¼å¿é¡»åæ§è¡ä¸åæ­¥éª¤:

1-ä½¿ç¨ Nuke å é¤ç°æéç½®æä»¶åè¯ä¹¦ã

2-éè¿ init å½ä»¤å¯å¨ match éç½®ã

`Fastlane match init`
3-å¨ ios å¹³å°ä¸åå»ºä½¿ç¨éå¯¹çè½¦éã

```
desc 'Fetch certificates and provisioning profiles'
  lane :certificates do
  match(app_identifier: 'com.app.bundle', type: 'development', readonly: true)
  match(app_identifier: 'com.app.bundle', type: 'appstore', readonly: true)
End 
```

Enter fullscreen mode Exit fullscreen mode

ä¹åï¼æ¨å¯ä»¥ä½¿ç¨ fastlane ios è¯ä¹¦å½ä»¤æä½¿ç¨è¯ä¹¦ä½ä¸ºå¶ä»è½¦éä¸çåè½ãMatch ä¼èªå¨å°éç½®æä»¶åè¯ä¹¦ä¿å­å°æ¨ç OS X å¯é¥(Keychain)ä¸­ã

### å®åð¤

å¨åå¸æ¨¡å¼ä¸ä½¿ç¨âæ±ç¼âä»»å¡ç¼è¯ Android åºç¨ç¨åºæ¶ï¼åºç¨ç¨åºå°èªå¨ç­¾åãä½ä½ é¦åéè¦çææé¢åçæç­¾åå¯é¥å¹¶å°å¶æ·»å å°é¡¹ç®ä¸­ï¼ä¸ç¨æå¿ï¼å¯ä»¥æ¥éæ¬ Facebook æåäºè§£å¦ä½åå°è¿ä¸ç¹ã

## æ±ç¼(æå»º)

### iOSð

ä¸ºäºçæç­¾åæå»ºï¼æä»¬å°åå»ºä¸ä¸ªä½¿ç¨ä»¥ååå»ºçè¯ä¹¦å­å¨åºçè½¦éï¼å¹¶ä½¿ç¨ gym å¿«éè½»æ¾å°æå»ºæä»¬çåºç¨ç¨åºãè¿ç¨ç»ææ¶ï¼æä»¬ä¼å¢å ç¼è¯ç¼å·ï¼ææä»¬çç³è¯·éå°ãbeta æµè¯ T2]æå¡

```
desc 'Build the iOS application.'
private_lane :build do
  certificates
  increment_build_number(xcodeproj: './ios/name.xcodeproj')
  gym(scheme: 'name', project: './ios/name.xcodeproj')
end 
```

Enter fullscreen mode Exit fullscreen mode

### å®åð¤

ä¸ºäºçæç­¾åç. apkï¼æä»¬å°åå»ºç¼è¯è½¦éãå¦æ¨æè§ï¼æä»¬æ­£å¨ä½¿ç¨ grad æä½ï¼ä»¥æ¸çå·¥ç¨å¹¶è£å¤åå°çæ¬ï¼ä»»å¡ä¸º[grad](https://docs.gradle.org/current/userguide/userguide.html)ã

```
desc 'Build the Android application.'
private_lane :build do
  gradle(task: 'clean', project_dir: 'android/')
  gradle(task: 'assemble', build_type: 'Release', project_dir: 'android/')
end 
```

Enter fullscreen mode Exit fullscreen mode

ç¶åèªå¨å¢å *çæ¬ä»£ç *ï¼å°*æ±ç¼ç§èµ*ä¸è¿ä¸ªå°ä»»å¡è¿æ¥èµ·æ¥ã

## åé

### iOSð

testflight æ¯ IOs beta æµè¯æ¶çåè¿ä¹è·¯ãè½ç¶å¼åèé¨æ·æç¹æ··ä¹±ï¼ä½å®ç¡®å®è¿è¡å¾å¾å¥½ãä½¿ç¨ pilotï¼æä»¬å¯ä»¥ç®¡çæä»¬ç testflight ç¼è¯ã

beta è½¦éå°ä½¿ç¨ç¼è¯è½¦éå pilot æä¾ä¸ä¸ªç­¾åç. ipaï¼ç¶åå°åç´ åéå° gitï¼å¹¶éè¿å¢å ç¼è¯ç¼å·æ¥é©±å¨æåçæ´æ¹ï¼æåå°æ¬å°ç¼è¯ä¸è½½å° testflightã

```
desc 'Ship to Testflight.'
  lane :beta do
    build
    pilot
    commit_version_bump(message: 'Bump build', xcodeproj: './ios/name.xcodeproj')
    push_to_git_remote
end 
```

Enter fullscreen mode Exit fullscreen mode

### å®åð¤

Android ä½¿ç¨ playstore å±äº« beta çãfastlane ä¹è½å®ç°èªå¨åï¼

Android ç beta è½¦éä¸ iOS å ä¹ç¸åï¼å®ä½¿ç¨ç¼è¯è½¦éçæå·²ç­¾åç. apkï¼ç¡®è®¤çæ¬ä»£ç çæ´æ¹ï¼ä½¿ç¨ supply ä»¥ beta çå½¢å¼ä¿è¿ playstore çæ¬å°ç¼è¯ã

```
desc 'Ship to Playstore Beta.'
lane :beta do
    build
    supply(track: 'beta', track_promote_to: 'beta')
    git_commit(path: ['./android/gradle.properties'], message: 'Bump versionCode')
    push_to_git_remote
end 
```

Enter fullscreen mode Exit fullscreen mode

ä½ ç Fastfile åºè¯¥çèµ·æ¥å¾åä¸é¢æ¾ç¤ºç

```
fastlane_version '2.53.1'

before_all do
  ensure_git_branch
  ensure_git_status_clean
  git_pull
end

platform :ios do
   # iOS Lanes
  desc 'Fetch certificates and provisioning profiles'
  lane :certificates do
    match(app_identifier: 'com.app.bundle', type: 'development', readonly: true)
    match(app_identifier: 'com.app.bundle', type: 'appstore', readonly: true)
  end
  desc 'Build the iOS application.'
  private_lane :build do
    certificates
    increment_build_number(xcodeproj: './ios/name.xcodeproj')
    gym(scheme: 'name', project: './ios/name.xcodeproj')
  end
  desc 'Ship to Testflight.'
  lane :beta do
    build
    pilot
    commit_version_bump(message: 'Bump build', xcodeproj: './ios/name.xcodeproj')
    push_to_git_remote
  end
end

platform :android do
  # Android Lanes
  desc 'Build the Android application.'
  private_lane :build do
    gradle(task: 'clean', project_dir: 'android/')
    gradle(task: 'assemble', build_type: 'Release', project_dir: 'android/')
  end
  desc 'Ship to Playstore Beta.'
  lane :beta do
      build
      supply(track: 'beta', track_promote_to: 'beta')
      git_commit(path: ['./android/gradle.properties'], message: 'Bump versionCode')
      push_to_git_remote
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

**è°¢è°¢**
Felix Krauseï¼Carlos Cuesta

ä¸ºæç¾ä¸½çå®æ¼å¾äºåæäººçæ¯æï¼
ä½ éè¦å¸®å©åï¼
ç»æçè¨ï¼æå¾ä¹æå¸®ä½ ï¼

åç»´å°Â·ç©å°¼å¥¥æ¯Â·å·´éå¥¥æ¯
([@ jaymb](https://dev.to/javymb))|æ¨ç¹