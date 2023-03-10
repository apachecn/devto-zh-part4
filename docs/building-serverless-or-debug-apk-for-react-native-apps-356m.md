# 为 React 原生应用构建无服务器或调试 APK

> 原文：<https://dev.to/shubhkirtisharma/building-serverless-or-debug-apk-for-react-native-apps-356m>

[![Photo by [Artem Sapegin](https://unsplash.com/photos/ZMraoOybTLQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/react-native?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)](img/2b45bbc32731a5ac7e8463cfe39fec50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_99e6qO6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Awyxuq21keffc5b0d_lMkUw.jpeg) 
照片由 [Artem Sapegin](https://unsplash.com/photos/ZMraoOybTLQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/react-native?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

因此，我已经在 React Native 上工作了很长时间，现在没有人问我这个问题，直到我问自己，“所以每次，我都必须创建一个签名的 APK 来在另一个设备上测试它？”。像往常一样，我开始谷歌搜索，但得到了很多结果，但没有一个结果真正适用于我创建或使用的所有应用程序。最终，我想出了一个解决方案来构建一个完整的、工作的无服务器 APK，它不需要打包程序就能运行。

基本上，过程是非常容易的。在项目的根目录中打开终端/命令提示符，并运行以下命令:

1.启动节点打包捆绑器:

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

2.当终端显示:“正在加载依赖关系图…”，在同一个目录中打开一个新的终端，现在运行以下命令在应用程序中创建一个新的目录来存储资产:

```
mkdir -p android/app/src/main/assets 
```

Enter fullscreen mode Exit fullscreen mode

3.使用 react-native 的 bundle 来捆绑上面创建的目录中的资产:

```
react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res 
```

Enter fullscreen mode Exit fullscreen mode

4.卷曲。创建到 index.android.bundle 的 js 文件:

```
curl "http://localhost:8081/index.bundle?platform=android" -o "android/app/src/main/assets/index.android.bundle" 
```

Enter fullscreen mode Exit fullscreen mode

5.转到 */android* 目录，运行 *gradlew* 构建 APK:

```
cd android && ./gradlew clean assembleDebug 
```

Enter fullscreen mode Exit fullscreen mode

嗯，就是这样。放轻松，皮斯！一切完成后，它显示“构建成功”，你的 APK 将出现在
文件夹中

```
<project>/android/app/build/outputs/apk/debug 
```

Enter fullscreen mode Exit fullscreen mode

如果你有什么建议，在下面评论或者鼓掌！