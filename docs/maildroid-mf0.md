# 邮件机器人

> 原文：<https://dev.to/_nedimf/maildroid-mf0>

## 如何在安卓系统中发送邮件

在 android 中发送电子邮件并不是一件容易的事情，但是使用 Maildroid 库只需要几秒钟

**图书馆**
图书馆[https://github.com/nedimf/maildroid/](https://github.com/nedimf/maildroid/)

图书馆在 androiddev 上有很大的吸引力，这就是它在♥️的运作方式

## 开始

* * *

将它添加到您的 root.gradle 文件

```
allprojects {
        repositories {
            ...
            maven { url 'https://jitpack.io' }
        }
    } 
```

添加依赖关系

```
 dependencies {
            implementation 'com.github.nedimf:maildroid:0.0.1'
          } 
```

### 将 Maildroid 添加到您的应用程序

```
 MaildroidX.Builder()
            .smtp("") //Add your smtp provider
            .smtpUsername("") 
            .smtpPassword("")
            .smtpAuthentication() //true
            .port("")
            .type(MaildroidX.HTML) //Type of email
            .to("")
            .from("")
            .subject("")
            .body("")
            .onCompleteCallback(object : MaildroidX.onCompleteCallback{
                override fun onSuccess() {
                    //Place for your code when email is sent successfully!
                }
                override fun onFail() {
                   //Place for your code when email is not sent!
                }
            },3000)
            .mail() 
```

#### 测试

在[mailtrap]([https://mailtrap.io/](https://mailtrap.io/)上创建一个帐户来测试你的电子邮件。T
在这些构造函数字段中添加用户名和密码。