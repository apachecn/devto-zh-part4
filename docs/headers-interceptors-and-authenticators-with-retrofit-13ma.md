# 标头、拦截器和授权码的改进

> 原文：<https://dev.to/ddinorahtovar/headers-interceptors-and-authenticators-with-retrofit-13ma>

在 Android 中，有时你需要添加几个参数，如头，以成功地发出请求，这是所有 Android 应用程序的正常行为，当你使用 Retrofit 时，你可以通过多种方式来做到这一点
例如，你可以使用注释头将参数直接添加到你的请求接口，并放入一个普通字符串，如下所示: