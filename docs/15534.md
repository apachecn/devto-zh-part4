# 注意:android 命令行

> 原文：<https://dev.to/callas1900/note-android-command-line-no3>

这是我的 android 命令行的东西。

# [度](#gradle)

启动你的 linux 后，你应该运行下面一次。

```
./gradlew --daemon 
```

assembleDebug 是我的任务名，所以你应该用你的等级任务名
来改变它

```
 ./gradlew cleanBuildCache clean assembleDebug --configure-on-demand -x test 
```

# 齐射

```
adb shell setprop log.tag.Volley VERBOSE 
```

# 复位

```
rm -fr .idea
rm -fr ~/.gradle 
```

然后建造

# jarsigner

```
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore /path/to/keystore -signedjar app/build/outputs/apk/release/app-release-signed.apk app/build/outputs/apk/release/app-release-unsigned.apk keystorealias 
```

# 如何拾取领域文件

```
$ adb shell
$$ run-as ${package.name}
$$ ls -laF files/
$$ cat files/default.realm > /sdcard/Download/default.realm
$$ exit
$$ exit
$ adb pull /sdcard/Download/default.realm . 
```