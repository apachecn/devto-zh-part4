# 使用 FollowRedirect(false)传递翻新客户端，但调用返回 200 而不是预期的 302

> 原文：<https://dev.to/mowdownjoe/passing-retrofit-a-client-with-followredirect-false-but-call-is-returning-a-200-instead-of-expected-302-21mp>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [通过使用 FollowRedirect(false)改进客户端，但调用返回 200 而不是预期的 302](https://stackoverflow.com/questions/58102577/passing-retrofit-a-client-with-followredirectfalse-but-call-is-returning-a-20)

Sep 25 '19 Comments: 1 Answers: 0[![](img/e3f0373ec76330150a340eacd410b600.png)0![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/58102577/passing-retrofit-a-client-with-followredirectfalse-but-call-is-returning-a-20) </header>

我有一个应用程序，试图从一个标题获得授权码。我将改装配置为:

```
private val moshi = Moshi.Builder().add(KotlinJsonAdapterFactory()).build()
private val httpClient = OkHttpClient.Builder().run {
    followRedirects(false)
    followSslRedirects(false)
    build()
}
private val retrofit = Retrofit.Builder().run {
    baseUrl(BASE_URL)
    client(httpClient)
    addConverterFactory(MoshiConverterFactory.create(moshi))
    build()
} 
```

在界面中…

<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/58102577/passing-retrofit-a-client-with-followredirectfalse-but-call-is-returning-a-20)</button>