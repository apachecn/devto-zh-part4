# 每个分析师都需要三张表

> 原文：<https://dev.to/dwl285/three-tables-every-analyst-needs-1hnk>

### 简介

如果你是第一批加入公司的分析师之一，你可能会发现大量的数据。您几乎肯定会有一些关系数据(可能为您的应用程序后端提供动力)，也可能有事件数据(可能通过第三方，如 [Segment](https://segment.com) )。

这使得很难知道每个分析请求从哪里开始。您可能想为每个分析创建一个新的数据集，但这将增加数据量，并很快变得难以管理。

幸运的是，**您可以将所有的数据收集到三个表格**中，然后这些表格可以用来回答大多数与用户相关的问题。

本文中的 SQL 片段适用于 Google BigQuery，但也可以很容易地适用于其他数据仓库。

### 表 1:用户统计

首先，**您需要一个表来告诉您关于您的用户的所有信息**。该表将包含维度(例如，用户帐户创建时间戳)和指标(例如，用户生命周期事务计数)。开始时可能很简单，但是随着公司的发展和变得越来越复杂，您将不断地向这个表中添加内容。

***要点:`user_stats`每个用户一行***T5】

```
SELECT * FROM user_stats 
```

| 用户标识 | 创建日期 | 最后一次出现在 | 交易记录 _ 最近 _ 7 天 | 事务 _ 生命周期 |
| --- | --- | --- | --- | --- |
| Thirty-two million three hundred and fifty-six thousand five hundred and forty-three | 2019-01-01 14:36:51 | 2019-03-14 19:55:10 | Two | Thirteen |
| Sixty-four million seven hundred and sixty-three thousand six hundred and fifty-four | 2019-02-01 10:30:18 | 2019-02-08 10:02:32 | Zero | four |
| Thirty-five million seven hundred and ninety-five thousand four hundred and forty-five | 2019-03-03 23:00:04 | 2019-03-03 23:03:07 | Zero | Zero |
| Ninety-eight million seven hundred and sixty-five thousand four hundred and forty-six | 2019-03-06 14:36:31 | 2019-03-15 08:20:22 | one | Two |
| ... | ... | ... | ... | ... |

#### 这个表可以用来回答什么样的问题？

本周有多少用户注册了？

```
SELECT 
  COUNT(*) as user_count
FROM
  user_stats
WHERE
  created_at >= TIMESTAMP_SUB(current_timestamp, INTERVAL 7 DAY) 
```

有多少用户进行了至少 5 次交易？

```
SELECT
  COUNT(*) as user_count
FROM
  user_stats
WHERE
  transactions_lifetime >= 5 
```

过去 24 小时内有多少用户登录过？

```
SELECT
  COUNT(*) as user_count
FROM
  user_stats
WHERE
  last_seen_at >= TIMESTAMP_SUB(current_timestamp, INTERVAL 24 HOUR) 
```

### 表 2:每日用户统计

对这个表的需求可能不是那么明显，但是事实证明，拥有一个描述每个用户自从注册以来每天的活动的表(即使他们那天根本不活动)是非常有用的。这张表中有四个维度是你绝对想要的:

*   `user_id`:您的用户的唯一标识符，您可以将其链接回`user_stats`
*   这一行对应的是什么日期
*   这个日期代表(用户一生中的)哪一天。用户注册的当天`day_n = 1`，注册后的第二天`day_n = 2`等等
*   用户在这一天是活跃的吗？(您选择如何定义活动应取决于您业务的具体情况。例如，如果您正在创建一个消息应用程序，您可以将“活跃”定义为至少发送了一条消息)

与`user_stats`一样，随着您的业务变得越来越复杂，该表最终可能会包含比这 4 个更多的字段。

***要点:`daily_user_stats`每个用户每天有一行(从他们注册以来的所有日子)***

```
SELECT * FROM daily_user_stats ORDER BY user_id, day_n 
```

| 用户标识 | 日期 | 第几天 | 曾 _ 活跃 |
| --- | --- | --- | --- |
| Thirty-two million three hundred and fifty-six thousand five hundred and forty-three | 2019-03-01 | one | one |
| Thirty-two million three hundred and fifty-six thousand five hundred and forty-three | 2019-03-02 | Two | Zero |
| Thirty-two million three hundred and fifty-six thousand five hundred and forty-three | 2019-03-03 | three | one |
| Thirty-five million seven hundred and ninety-five thousand four hundred and forty-five | 2019-03-02 | one | one |
| Thirty-five million seven hundred and ninety-five thousand four hundred and forty-five | 2019-03-03 | Two | Zero |
| Ninety-eight million seven hundred and sixty-five thousand four hundred and forty-six | 2019-03-03 | one | one |
| ... | ... | ... | ... |

#### 这个表可以用来回答什么样的问题？

注册一周后活跃的用户比例是多少？

```
SELECT 
  AVG(was_active) as active_user_ratio
FROM
  daily_users_stats
WHERE
  day_n = 8 
```

注册后第二天活跃用户的百分比是如何随时间变化的？

```
SELECT
  DATE(user_stats.created_at) AS created_date,
  AVERAGE(was_active) as active_user_ratio
FROM
  daily_user_stats
  INNER JOIN user_stats
    ON daily_user_stats.user_id = user_stats.user_id
WHERE
  day_n = 2
ORDER BY created_date ASC 
```

我们的[用户留存曲线](https://www.sequoiacap.com/article/retention)是什么样子的？

```
SELECT
  day_n,
  AVERAGE(was_active) as active_user_ratio
FROM
  daily_user_stats
  INNER JOIN user_stats
    ON daily_user_stats.user_id = user_stats.user_id
WHERE
  user_stats.created_at >= TIMESTAMP_SUB(current_timestamp, INTERVAL 7 DAY)
  AND day_n <= 7 
```

### 表 3:会话

`sessions`表是存储事件数据的地方。事件是应用程序或网站上的用户交互(例如，点击按钮、提交表单、发送消息等)。您可能会跟踪许多不同的事件，找到一种一致的方法来分析它们可能会很棘手。塞申斯是一张组织混乱的桌子！

会话是代表与您的产品进行端到端交互的事件集合:用户打开应用程序>用户打开产品 abc 页面>用户点击添加到购物车>用户关闭应用程序。如何将用户事件合并到一个会话中取决于您的产品，但标准方法是在 30 分钟不活动后结束会话。来自该用户的任何后续事件都将被添加到新会话中。

使用一些[相对复杂的 SQL](https://gist.github.com/lewish/a37ecb423a45ccf861373d1c942a0ea5) ，你可以将你的原始事件分割成会话。您的会话表至少应具有以下维度:

*   `user_id`:唯一用户标识符
*   `session_id`:从 1(用户的第一个会话)开始，随后的每个会话递增
*   `start_time`:会话中第一个事件的时间戳
*   `end_time`:会话中最后一个事件的时间戳
*   `events`:会话中所有事件的有序列表/数组*(这在 BigQuery 中得到很好的支持，其他查询语言可能需要稍微不同的处理方式，例如使用 JSON)*

***要点:`sessions`为用户的每个会话提供一行***

```
SELECT * FROM sessions 
```

| 用户标识 | 会话标识 | 开始时间 | 结束时间 | 事件 |
| --- | --- | --- | --- | --- |
| Thirty-two million three hundred and fifty-six thousand five hundred and forty-three | one | 2019-03-14 19:55:10 | 2019-03-14 19:58:15 | [已打开的应用程序，已点击的产品，已关闭的应用程序] |
| Thirty-two million three hundred and fifty-six thousand five hundred and forty-three | Two | 2019-03-16 13:45:10 | 2019-03-16 13:51:15 | [打开的应用程序，滚动的列表，关闭的应用程序] |
| Twenty-three million five hundred and one thousand two hundred and ninety-four | one | 2019-03-18 16:13:10 | 2019-03-18 16:14:15 | 【已打开 _app，...，已关闭 _app] |
| Twenty-three million five hundred and one thousand two hundred and ninety-four | one | 2019-03-19 19:55:10 | 2019-03-19 19:58:15 | 【已打开 _app，...，已关闭 _app] |
| ... | ... | ... | ... | ... |

#### 这个表可以用来回答什么样的问题？

平均会话长度如何随时间变化？

```
SELECT
  DATE(end_time) session_date,
  AVG(TIMESTAMP_DIFF(end_time, start_time)) AS session_length
FROM
  sessions
GROUP BY 
  1 
```

包含事务的会话份额是多少？

```
SELECT
  DATE(end_time) session_date,
  AVG(IF(ARRAY_TO_STRING(events, '-') LIKE '%transaction_complete%', 
         1,
         0)) AS transaction_ratio
FROM
  sessions
GROUP BY 
  1 
```

什么样的事件链会导致用户离开应用程序并且不再回来？

```
SELECT
  user_id,
  ARRAY_REVERSE(events)[SAFE_OFFSET(2)] AS last_event_offset_2,
  ARRAY_REVERSE(events)[SAFE_OFFSET(1)] AS last_event_offset_1,
  ARRAY_REVERSE(events)[SAFE_OFFSET(0)] AS last_event_offset_0
FROM
  sessions
  LEFT JOIN user_stats
    ON sessions.user_id = user_stats.user_id
WHERE
  sessions.session_id = 1
  AND user_stats.sessions_all_time = 1 
```

### 总结

这三个结构良好的表格可以帮助回答大多数问题:

*   **`user_stats`** :我的用户群中有哪些人
*   **`daily_user_stats`** :我有多少活跃用户，他们的忠诚度如何
*   **`sessions`** :用户对我的产品有怎样的体验

一旦您编写了将其他数据集合并到这三个简单的表中的查询，您就应该设置一个时间表来使它们跟上任何新的变化。你很快就会开始依赖这些表格来进行大部分的分析。

[Dataform](https://dataform.co/developers?utm_medium=organic&utm_source=dev_to&utm_campaign=3_tables) 提供了一个全面管理的协作工作空间来设置时间表和管理您的数据仓库。想了解更多关于 Dataform 的信息，请查看我们的[文档](https://docs.dataform.co/?utm_medium=organic&utm_source=dev_to&utm_campaign=3_tables)，或者[创建一个免费账户](https://dataform.co/signup/?utm_medium=organic&utm_source=dev_to&utm_campaign=3_tables)，并开始使用 Dataform 的全托管网络平台。