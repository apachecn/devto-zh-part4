# 彬彬有礼的消费者

> 原文：<https://dev.to/dealeron/the-courteous-consumer-kbc>

作为 DealerOn Reporting 团队的开发人员，我经历了许多从其他服务导入数据的棘手问题。我们每天从一些最大的提供商那里自动下载数千个账户的数据。有人在设计这样的系统时犯的典型错误是为每个报告/帐户建立一个`Task`和`Task.WhenAll`。最终，您意识到过多的请求可能会淹没提供者，所以您添加了一个信号量，并将其设置为 20 左右。完事了，对吧？

这是危险的，因为在大多数情况下这是可行的。你会得到很多数据，认为你是安全的，直到你需要回填一个帐户超过几天。您将查看日志，几天后，您会注意到您的请求超时或有没有意义的错误。怎么了?

大多数大型供应商都在他们的 API 上实现了费率保护，当他们接近限制时会警告消费者，当他们超过限制时会直接阻止他们。如果你不知道如何识别和留意这些警告，你最终只会徒劳地诅咒供应商的名字。在这篇文章中，我们将研究脸书、微软和谷歌如何告诉你什么时候该放慢脚步，这样你才能成为一个有礼貌的消费者。

# 脸书

基于你访问的内容，脸书的 Graph API 有许多不同的[节流限制](https://developers.facebook.com/docs/graph-api/overview/rate-limiting)。根据你阅读这篇文章的时间，脸书也在逐步改变他们的速率限制，称为业务用例使用。旧版本和新版本的基本前提是，您发出的每个呼叫响应都将有一个标题，告诉您正在访问的帐户的当前状态。

```
x-ad-account-usage: {
  'acc_id_util_pct':9.67     //Percentage of calls made for this ad account.
} 
```

当百分比达到 100 时，进一步调用将导致错误。我们决定在 90%时暂停，因为我们可以同时运行多个请求。这个旧版本将在 5 分钟后重置为 0%。新的响应头:

```
x-business-use-case-usage: {
  '{business-id}':  [{
     'call_count': 100,       //Percentage of calls made for this business ad account.
     'total_cputime': 16,     //Percentage of the total cpu time has been used.
     'total_time': 45,       //Percentage of the total time has been used.
     'type': 'ads_insights',                      //Type of rate limit logic being applied.
     'estimated_time_to_regain_access': 10       //Time in minutes to resume calls.
  }]
} 
```

如果前 3 个数字中的任何一个达到 100%，该帐户将受到限制。脸书 API v3.3 版将根据您正在访问的端点使用任一响应。4.0 版本将只是业务用例的使用风格。

我们检查每个响应，并使用一个`SemaphoreSlims`的`Dictionary`,这样我们就可以锁定个人帐户，直到他们准备好恢复。我们为该帐户建立的任何其他调用将检查`_accountLocks`，并发现它已经在使用中。下面的方法只处理旧的样式，需要为新的版本进行更新(包括合并漂亮的`estimated_time_to_regain_access`字段)。

```
public static async Task CheckResponse(this HttpResponseMessage httpResponse, 
           BaseFacebookResponse dataResponse, string url, string accountId)
{
  var accountLimit = JsonConvert.DeserializeObject<FacebookAccountLimit> 
                     (httpResponse.GetHeaderValue("x-ad-account-usage") ?? "");
  var appLimit = JsonConvert.DeserializeObject<FacebookAccountLimit> 
                 (httpResponse.GetHeaderValue("x-app-usage") ?? "");

  if (appLimit != null && appLimit.Usage > MAX_USAGE_PERCENT)
  {
    await _appLock.WaitAsync();
    await Task.Delay(APP_SLEEP_MINUTES);
    _appLock.Release();
  }

  if (accountLimit != null 
   && accountLimit.Usage > WARNING_USAGE_PERCENT 
   && accountLimit.Usage <= MAX_USAGE_PERCENT)
  {
    await Task.Delay(ACCOUNT_SLOWDOWN);
  }

  if (accountLimit != null 
   && accountLimit.Usage > MAX_USAGE_PERCENT)
  {
    await _accountLocks[accountId].WaitAsync();
    await Task.Delay(ACCOUNT_SLEEP_MINUTES);
    _accountLocks[accountId].Release();
  }

  if (dataResponse.Error != null)
  {
    throw new Exception($@"Facebook API Error: {dataResponse.Error.Message}  URL: {url}  Full Error: 
  {JsonConvert.SerializeObject(dataResponse.Error)}");
  }
} 
```

# 谷歌广告词

速率限制基于对响应中`RateExceededError`的观察。

我们使用的是`googleads-dotnet-lib` nuget 包，它在源代码中有一个关于如何处理`RateExceededError` :
的例子

```
try
{
  //make your request
}
catch (AdWordsApiException e)
{
  // Handle API errors.
  var innerException = e.ApiException as ApiException;
  if (innerException == null)
  {
    throw new Exception(
      "Failed to retrieve ApiError. See inner exception for more details.", e);
  }

  foreach (ApiError apiError in innerException.errors)
  {
    if (!(apiError is RateExceededError))
    {
      // Rethrow any errors other than RateExceededError.
      throw;
    }

    // Handle rate exceeded errors.
    var rateExceededError = (RateExceededError)apiError;
    //lock out other requests for this account
    await _accountLocks[accountId].WaitAsync();
    await Task.Delay(rateExceededError.retryAfterSeconds * 1000);
    _accountLocks[accountId].Release();
    //we should retry this request now
  }
} 
```

另一方面，最好的进攻是良好的防御，我们通常通过使用信号量来避免 RateExceededError 错误，以限制我们自己在任何时候只能进行 10 次 open Adwords 报告调用。

# 谷歌分析

这个产品和 Adwords 是同一家公司生产的，所以 API 肯定是相似的，对吗？一旦我们都笑完了，看看[文档](https://developers.google.com/analytics/devguides/reporting/mcf/v3/limits-quotas)。

每个帐户都有严格的每日限额和每秒实时查询限额。您可以通过状态代码 403 或 429 来检测是否超出了每日或 QPS 限制，但更好的做法是限制您的请求，使您不会达到它。

```
public async Task<T> MakeRateLimitedRequest<T>(Func<T> action)
{
  await _semaphore.WaitAsync();
  Task.Delay(_timeSpan).ContinueWith(_ => _semaphore.Release());
  return action();
} 
```

如果您的信号量有 10 个槽，时间跨度是 1 秒，那么您应该不能每秒发出 10 个以上的请求。如果你要处理多个账户，你需要一个信号量字典，这样每个账户都能以最大分配运行。

# 必应广告

微软可能有[最简单的方法](https://docs.microsoft.com/en-us/advertising/hotel-service/throttle-requests)来检测和处理节流请求:

> 为了确保每个人都有资源，API 限制了客户 ID 每分钟可以发出的请求数量。该限制未记录在案，可能会发生变化。如果超过每分钟请求的限制，API 将返回 HTTP 状态代码 429。当您收到状态代码 429 时，您必须等待 60 秒才能重新提交请求。

我们的脸书方法的一个修改版本将足够容易地处理这个问题。

# 结论

在每一部分中可能会出现的情况是，文档通常会解释提供商将如何限制对其服务器的访问。这对于防止恶意用户或不小心编写了无限循环的人破坏一切是必要的。无论你正在访问什么样的第三方数据，确保你已经完全测试了你的代码，运行它直到你的限速器开始工作，这样你就可以看到它们工作了。通过遵循这里的一些建议，你将很快学会在你注意到生产中的问题之前*实施速率限制。*