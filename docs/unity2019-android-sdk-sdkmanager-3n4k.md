# Unity2019 在附加模块 Android SDK 上运行 sdkmanager 很麻烦

> 原文：<https://dev.to/shiena/unity2019-android-sdk-sdkmanager-3n4k>

# 前言

Android SDK/NDK 现在可以从 Unity2019 作为附加模块安装。 这个 Android SDK 只加入了`platforms;android-28`，所以想使用其他 API 等级的时候需要在 Android SDK 上追加安装软件包。
但是，在 Windows 的情况下，由于以下理由变得非常难理解。

*   安装路径很深
    *   Unity2019.1.4f1 时:`C:\Program Files\Unity\Hub\Editor\2019.1.4f1\Editor\Data\PlaybackEngines\AndroidPlayer\SDK`
*   安装到默认的`C:\Program Files`需要管理员权限

因此，制作了打开具有`sdkmanager.bat`的文件夹的管理员权限的命令提示符的编辑器扩展。

# 用管理员权限打开 Android SDK 文件夹的编辑器扩展

Remember ※

还提供了从 Unity2018 添加的支持 Package Manager 的版本。

*   [从 Unity 快速打开嵌入式 Android SDK 和 logcat 的编辑器扩展](https://qiita.com/shiena/items/8ec1e821702a213fa32f)

* * *

请将以下脚本保存在`Assets/Editor`文件夹中。 执行
菜单中追加的`Tools/Open android sdk folder as administrator`后，在管理员权限的命令提示符下打开带有`sdkmanager.bat`的文件夹。
然后请按照`sdkmanager "platforms;android-25"`所示安装必要的软件包。

```
#if UNITY_2019_1_OR_NEWER && UNITY_ANDROID && (UNITY_EDITOR_WIN || UNITY_EDITOR_OSX)
using System;
using System.Diagnostics;
using System.IO;
using UnityEditor;

public static class AndroidSDKHelper
{
#if UNITY_EDITOR_WIN
    [MenuItem("Tools/Open android sdk folder as administrator", false, 100)]
    private static void OpenAndroidSdkFolderOnWindows()
    {
        var androidPlaybackEngineDirectory =
            BuildPipeline.GetPlaybackEngineDirectory(BuildTarget.Android, BuildOptions.None);
        var androidSdkTools = Path.Combine(androidPlaybackEngineDirectory, "SDK", "tools", "bin");
        var jdkPath = Path.Combine(androidPlaybackEngineDirectory, "Tools", "OpenJDK", "Windows");
        var cmd = Environment.GetEnvironmentVariable("ComSpec");

        if (!Directory.Exists(androidSdkTools) || !Directory.Exists(jdkPath) || !File.Exists(cmd))
        {
            return;
        }

        var process = new Process
        {
            StartInfo = new ProcessStartInfo
            {
                FileName = cmd,
                Verb = "runas",
                UseShellExecute = true,
                Arguments = $"/K cd {androidSdkTools} & set \"JAVA_HOME={jdkPath}\""
            }
        };
        process.Start();
    }
#endif

#if UNITY_EDITOR_OSX
    [MenuItem("Tools/Open android sdk folder", false, 100)]
    private static void OpenAndroidSdkFolderOnMac()
    {
        var androidPlaybackEngineDirectory =
            BuildPipeline.GetPlaybackEngineDirectory(BuildTarget.Android, BuildOptions.None);
        var androidSdkTools = Path.Combine(androidPlaybackEngineDirectory, "SDK", "tools", "bin");

        if (!Directory.Exists(androidSdkTools))
        {
            return;
        }

        var process = new Process
        {
            StartInfo = new ProcessStartInfo
            {
                FileName = "open",
                Arguments = $"-a Terminal {androidSdkTools}"
            }
        };
        process.Start();
    }
#endif
}
#endif 
```

# Feeling

每次升级 Unity 的版本都需要追加安装 Android SDK 的软件包，所以像 Unity2018 以前那样单独安装 Android SDK 会更轻松吗？

Unity2018 以前のandroidsdkのセットアップについて->[unity-manual:Android 环境设置](https://docs.unity3d.com/2018.4/Documentation/Manual/android-sdksetup.html)