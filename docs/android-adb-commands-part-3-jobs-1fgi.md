# Android ADB 命令(第 3 部分)作业

> 原文：<https://dev.to/neokleoys2005/android-adb-commands-part-3-jobs-1fgi>

# ADB 命令

以前的帖子:

*   第一部分
*   [第二部分](https://dev.to/neokleoys2005/android-adb-commands-part-2-1gog)

根据文件:

> Android Debug Bridge (adb)是一个多功能的命令行工具，可以让您与设备进行通信。adb 命令有助于各种设备操作，如安装和调试应用程序，它提供了对 Unix shell 的访问，您可以使用该 shell 在设备上运行各种命令。

更多信息的链接。

3)使用以下命令检查作业调度程序信息:

> adb shell 转储系统作业调度程序

由于输出很大，您应该使用`cmd+F`或`ctrl+f`作为您的包名。它应该看起来像

```
JOB #u0a93/17: eec3709 com.foo.bar.application/com.evernote.android.job.v21.PlatformJobService
    u0a93 tag=*job*/com.foo.bar.application/com.evernote.android.job.v21.PlatformJobService
    Source: uid=u0a93 user=0 pkg=com.samplejobscheduler.application
    JobInfo:
      Service: com.foo.bar.application/com.evernote.android.job.v21.PlatformJobService
      PERIODIC: interval=+15m0s0ms flex=+5m0s0ms
      PERSISTED
      Requires: charging=false deviceIdle=false
      Network type: 2
      Backoff: policy=1 initial=+30s0ms
      Has early constraint
      Has late constraint
    Required constraints: TIMING_DELAY DEADLINE UNMETERED
    Satisfied constraints: CONNECTIVITY NOT_ROAMING APP_NOT_IDLE DEVICE_NOT_DOZING
    Unsatisfied constraints: TIMING_DELAY DEADLINE UNMETERED
    Earliest run time: 07:23
    Latest run time: 12:23
    Ready: false (job=false pending=false active=false user=true) 
```

如您所见，输出中包括了调度作业时设置的要求，以及退避策略、网络等。最重要的信息

> 最早运行时间:07:23
> 最晚运行时间:12:23
> 就绪:假(作业=假挂起=假活动=假用户=真)

如果您在 **Android 7.1 和更高版本**上进行测试，那么您可以使用下面的命令强制运行一个调度的作业。

> adb shell cmd jobscheduler run -f 程序包名称作业 ID

**PACKAGE_NAME** 应该是你的 app 的包。
**JOB_ID** 在调度作业时设置

一个关于**如何读取**输出和**作业调度器有什么工具可用**的优秀 SO 回答[查看一下](https://stackoverflow.com/a/42133270/3330058)