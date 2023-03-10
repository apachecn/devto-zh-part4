# 生物认证:在 Android 中实现的步骤

> 原文：<https://dev.to/emily3103/biometric-authentication-steps-to-implement-in-android-19f2>

本文探讨了如何使用 google 生物特征在系统中实现生物认证。使用 API 功能“生物计量提示”。生物识别提示允许在 Android 中建立生物识别认证过程。[生物特征提示](https://developer.android.com/reference/android/hardware/biometrics/BiometricPrompt)类管理系统提供的对话框

Android 6.0 早就支持生物指纹认证了。如果以更准确的结果认证终端用户，这种新的生物识别技术在这个意义上更有前途。[在线身份验证 API](https://shuftipro.com/api/docs/?python#verification-services)使用生物识别技术来识别和验证最终用户，因为生物识别技术在远程验证时代越来越深入人心。生物识别技术采用了一致的[安全等级](https://shuftipro.com/blogs/biometric-authentication-its-applications-and-associated-constraints/)，这种技术的需求使得它可以兼容大量设备。

### **#1。添加权限**

在 AndroidManifest.xml 中，添加使用生物识别的权限，特别是指纹权限。

manifest xmlns:Android = "[http://schemas.android.com/apk/res/android](http://schemas.android.com/apk/res/android)"
package = " com . an . biometric "

用途-权限 Android:name = " Android . permission . use _ BIOMETRIC "
用途-权限 Android:name = " Android . permission . use _ FINGERPRINT "

### **#2。**

**检查设备兼容性**

确保设备与生物识别技术兼容。确保它有 6.0 或高于 6.0 的版本，还装有指纹传感器。需要用户许可来授予对指纹传感器的访问，并且至少一个指纹被注册到设备。

/*检查 android 版本
*指纹支持设备检查
*如果 minSdkversion 是> = 23，那么就不需要应用下面的检查
*/

公共类 Bio_utils{

公共静态布尔值 isBioPromptEnabled(){
返回(Build)。版本. SDK_INT > =构建。版本 _ 代码。p)；
}

/ *检查指纹传感器检查* /

公共静态布尔 sdkversioncheck(){
return(Build。版本. SDK_INT > = Build。版本代码。m)；
}

/*类似地嵌入硬件支持检查、设备中指纹可用性以及许可授权检查*/
}

### **#3。生物识别提示对话框**

生物识别提示对话框仅在 Android P 中显示。确保上述检查后，使用生物识别提示生成器:

**setTitle():** 设置显示标题
**setSubtitle():** 设置显示字幕
**setDescription():** 设置对话框的描述
**setnegative button():**设置负按钮的文本

上述列表中，setTitle()和 setNegativeButton 为必填字段，其他为可选字段。

### **#4。生物计量提示。**

**认证回调**

为了在某些功能之后监听认证事件，用户需要响应这样的事件。根据那个反应，生物计量提示。使用了 AuthenticationCallback。以下是四种方法:

**OnAuthenticationSucceeded:**当指纹与注册的指纹成功匹配时
**onauthentication failed:**当指纹与设备中注册的指纹不匹配时，此时此回调提示
**OnAuthenticationError:**当出现难以处理的错误并且遇到认证过程完成时
**OnAuthenticationHelp:**当认证过程中出现非致命错误时使用此方法

### **#5。创建一个 UI 代码**

创建一个 UI 代码，看起来类似于生物识别提示的对话框。设置标题、副标题、更新状态、设置描述和插入按钮。继续基本的用户界面，稍后输入详细的花式。
以上这些步骤是在 Android 中实现生物认证系统的基础。