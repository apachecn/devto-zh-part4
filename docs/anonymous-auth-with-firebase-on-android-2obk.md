# Android 上基于 Firebase 的匿名认证

> 原文：<https://dev.to/segun_codes/anonymous-auth-with-firebase-on-android-2obk>

使用 firebase 身份验证时，可以创建临时匿名帐户，以便使用 Firebase 对未注册应用程序的用户进行身份验证，从而存储和使用受安全规则保护的数据。如果匿名用户决定注册您的应用程序，您可以连接他们的登录认证，以便他们可以在未来的会话中继续使用他们确保的信息。

## 安装

*   将 [Firebase](https://firebase.google.com/docs/android/setup) 添加到你的 android 项目中，如果你还没有的话
*   将 Firebase Authentication Android 库的依赖项添加到您的模块( *app-level* ) Gradle 文件(通常是 *app/build.gradle* )中:

```
implementation 'com.google.firebase:firebase-auth:18.1.0' 
```

Enter fullscreen mode Exit fullscreen mode

*   从 [Firebase 控制台将你的应用程序连接到 Firebase 项目。](https://console.firebase.google.com/)
*   启用匿名认证:在 [Firebase 控制台](https://console.firebase.google.com/)中，打开*认证*部分。在*签到方式*页面，启用>匿名签到方式。

## 用法

*   在活动的 *onCreate* 方法中，获取 *FirebaseAuth* 对象的共享实例:

```
private FirebaseAuth mAuth;
// ...
// Initialize Firebase Auth
mAuth = FirebaseAuth.getInstance(); 
```

Enter fullscreen mode Exit fullscreen mode

*   初始化您的活动时，请检查用户当前是否已登录:

```
@Override
public void onStart() {
    super.onStart();
    // Check if user is signed in (non-null) and update UI accordingly.
    FirebaseUser currentUser = mAuth.getCurrentUser();
    updateUI(currentUser);
} 
```

Enter fullscreen mode Exit fullscreen mode

*   匿名拨打*sign inanymous ly*作为匿名用户登录:

```
mAuth.signInAnonymously()
        .addOnCompleteListener(this, new OnCompleteListener<AuthResult>() {
            @Override
            public void onComplete(@NonNull Task<AuthResult> task) {
                if (task.isSuccessful()) {
                    // Sign in success, update UI with the signed-in users information
                    Log.d(TAG, "signInAnonymously:success");
                    FirebaseUser user = mAuth.getCurrentUser();
                    updateUI(user);
                } else {
                    // If sign in fails, display a message to the user.
                    Log.w(TAG, "signInAnonymously:failure", task.getException());
                    Toast.makeText(AnonymousAuthActivity.this, "Authentication failed.",
                            Toast.LENGTH_SHORT).show();
                    updateUI(null);
                }

                // ...
            }
        }); 
```

Enter fullscreen mode Exit fullscreen mode

使用 *getCurrentUser* 方法获取登录用户的帐户数据

## 将匿名账户迁移为永久账户

*   当用户注册时，完成用户的身份验证提供者的登录流程，直到(但不包括)调用 FirebaseAuth.signInWith 方法之一。

*   为新的身份验证提供者获取一个 auth credential:
    **电子邮件和密码**

```
AuthCredential credential = EmailAuthProvider.getCredential(email, password); 
```

Enter fullscreen mode Exit fullscreen mode

*   将 *AuthCredential* 对象传递给登录用户的 *linkWithCredential* 方法

```
mAuth.getCurrentUser().linkWithCredential(credential)
        .addOnCompleteListener(this, new OnCompleteListener<AuthResult>() {
            @Override
            public void onComplete(@NonNull Task<AuthResult> task) {
                if (task.isSuccessful()) {
                    Log.d(TAG, "linkWithCredential:success");
                    FirebaseUser user = task.getResult().getUser();
                    updateUI(user);
                } else {
                    Log.w(TAG, "linkWithCredential:failure", task.getException());
                    Toast.makeText(AnonymousAuthActivity.this, "Authentication failed.",
                            Toast.LENGTH_SHORT).show();
                    updateUI(null);
                }

                // ...
            }
        }); 
```

Enter fullscreen mode Exit fullscreen mode

如果 *linkWithCredential* 方法调用成功，匿名账户数据将被迁移到新用户