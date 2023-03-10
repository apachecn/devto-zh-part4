# Java 中 Web API 认证的令牌管理实现

> 原文：<https://dev.to/unhurried/a-token-management-implementation-for-web-api-authentication-in-java-4l25>

本文研究了一个 Java 中的同步过程的实现，该实现允许多个线程共享相同的定时令牌，假设访问令牌在 OAuth2 中，用于 Web API 认证。

#### 规范令牌管理

我假设这个令牌管理系统的规范如下。

*   多个线程使用同一个令牌调用 Web APIs。
*   当令牌过期时，首先检测到过期的线程将更新令牌。
*   在更新过程中，从其他线程检索令牌需要等待更新完成。

#### 用 Java 实现

以下是上述令牌管理系统的实现。

```
public class TokenManager {

  // Singleton Pattern
  private static TokenManager instance = new TokenManager();
  private TokenManager() {}
  public static TokenManager getInstance() {
    return instance;
  }

  /* Set "volatile" to variables for token and update time so that any
     threads can retrieve the up-to-date state of the variables. */
  // Timed token
  private volatile String token = null;
  // Update time calcurated from TTL of the token
  private volatile long refreshAt = 0L;

  // getToken doesn't need "synchronized" as any threads can retrieve
  //  the up-to-date token with the aid of "volatile" keyword.
  public String getToken() {
    if (System.currentTimeMillis() >= refreshAt) {
      syncUpdateToken();
    }
    return this.token;
  }

  // Make the method "synchronized" so that only one thread can
  // execute it in time.
  private synchronized void syncUpdateToken() {
    // Prevent subsequents threads that call getToken method during
    // the token update from updating the token again.
    if (System.currentTimeMillis() < refreshAt) {
      return;
    }

    // Token Update Process
    // Update "token" before "refreshAt" as "refreshAt" is used
    // first in getToken method to check the token expiration.
    this.token = ...
    this.refreshAt = ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过使实例变量(token 和 refreshAt)成为“volatile”，getToken 方法不需要“synchronized”，这使得能够在令牌有效时并行执行令牌检索。

请注意，这会导致后续线程调用 updateToken 方法，因此在 updateToken 中还需要进行令牌过期检查，以防止不必要的令牌更新。