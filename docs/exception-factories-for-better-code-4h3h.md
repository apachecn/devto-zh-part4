# 更好代码的异常工厂

> 原文：<https://dev.to/eduarguz/exception-factories-for-better-code-4h3h>

是的，异常工厂...虽然我认为它已经在网络上被分享了很多次。今天，我在做代码审查，发现了一个很好的“异常工厂”用例，所以我认为值得与你分享这个编程技巧。

我审查的代码在某个方法中有这样的内容:

```
public function user(){
    ...

    $token = $this->getToken();
    $siteCredential = $this->getSiteCredential();

    throw_if(empty($token),
        AuthenticationException::class,
        __('auth.failed', ['code' => AuthenticationCodes::TOKEN_NOT_PROVIDED]), 401);

    throw_if(is_null($siteCredential),
        AuthenticationException::class,
        __('auth.failed', ['code' => AuthenticationCodes::CREDENTIALS_NOT_FOUND]), 401);

    throw_if($siteCredential->hasExpired(),
        AuthenticationException::class,
        __('auth.failed', ['code' => AuthenticationCodes::CREDENTIALS_EXPIRED]), 401);

    throw_if(! optional($siteCredential->site)->isEnabled(),
        AuthenticationException::class,
        __('auth.failed', ['code' => AuthenticationCodes::SITE_DISABLED]), 401);
    ...
} 
```

代码工作得很好，不要误解我的意思，我试图通过这种重构来实现的事情是使代码更加可读和干净。正如你所看到的，每个`Exception`都是利用 php 中的多行表达式有条件抛出的，这些长表达式在阅读代码时可能变得不那么友好。

每当我遇到这种情况，我喜欢将它们重构为“异常工厂方法”。我们可以看到，每个抛出的异常都是一个`AuthenticationException`，所以在类本身内部工作是有意义的，我们将引入一个(或多个)新的`static`方法来处理异常的“构造”。

重构后，`AuthenticationException`类看起来像这样:

```
class AuthenticationException extends Exception
{
    protected $httpStatusCode = 401;

    public static function forCode(string $code): self
    {
        return new self(__('auth.failed', ['code' => $code]));
    }

    public static function forTokenNotFound(): self
    {
        return self::forCode(AuthenticationCodes::TOKEN_NOT_PROVIDED);
    }

    public static function forCredentialsNotFound(): self
    {
        return self::forCode(AuthenticationCodes::CREDENTIALS_NOT_FOUND);
    }

    public static function forCredentialsExpired(): self
    {
        return self::forCode(AuthenticationCodes::CREDENTIALS_EXPIRED);
    }

    public static function forSiteDisabled(): self
    {
        return self::forCode(AuthenticationCodes::SITE_DISABLED);
    } 
```

现在我们可以清理初始的`user()`方法来使用我们的工厂方法:

```
public function user(){
    ...

    $token = $this->getToken();
    $siteCredential = $this->getSiteCredential();

    if (empty($token)) {
        throw AuthenticationException::forTokenNotFound();
    }

    if (is_null($siteCredential)) {
        throw AuthenticationException::forCredentialsNotFound();
    }

    if ($siteCredential->hasExpired()) {
        throw AuthenticationException::forCredentialsExpired();
    }

    if (is_null($siteCredential->site) || $siteCredential->site->isDisabled()) {
        throw AuthenticationException::forSiteDisabled();
    }
    ...
} 
```

我认为这使得代码更具表现力和可读性。

分享一下你的想法！！