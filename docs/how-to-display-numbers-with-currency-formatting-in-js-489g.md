# 如何在 JS 中显示货币格式的数字？

> 原文：<https://dev.to/igor_alexandrov/how-to-display-numbers-with-currency-formatting-in-js-489g>

使用 Intl。NumberFormat 启用国家/货币敏感格式。

```
const toCurrency = (n, curr, LanguageFormat = undefined) =>
  Intl.NumberFormat(LanguageFormat, { style: 'currency', currency: curr }).format(n); 
```

```
toCurrency(123456.789, 'EUR'); // €123,456.79  | currency: Euro | currencyLangFormat: Local
toCurrency(123456.789, 'RUB'); // RUB 123,456.79  | currency: Ruble | currencyLangFormat: Local
toCurrency(123456.789, 'RUB', 'Ru-ru') // 123 456,79 ₽  | currency: Ruble | currencyLangFormat: Russian
toCurrency(123456.789, 'USD', 'en-us'); // $123,456.79  | currency: US Dollar | currencyLangFormat: English (United States)
toCurrency(123456.789, 'USD', 'fa'); // ۱۲۳٬۴۵۶٫۷۹ ؜$ | currency: US Dollar | currencyLangFormat: Farsi
toCurrency(322342436423.2435, 'JPY'); // ¥322,342,436,423 | currency: Japanese Yen | currencyLangFormat: Local
toCurrency(322342436423.2435, 'JPY', 'fi'); // 322 342 436 423 ¥ | currency: Japanese Yen | currencyLangFormat: Finnish 
```