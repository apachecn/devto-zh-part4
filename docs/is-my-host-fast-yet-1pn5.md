# 我的主人快了吗？

> 原文：<https://dev.to/rick_viscomi/is-my-host-fast-yet-1pn5>

Chrome UX 报告是一个真实的 Chrome 用户浏览 500 多万个网站表现的数据集。本周在其最新的[版本](https://twitter.com/ChromeUXReport/status/1148682386013675520)中，首字节时间(TTFB)被添加为一个新的指标。TTFB 是衡量网页从开始导航(单击链接)到响应的第一个字节到达客户端所用时间的指标。这中间发生了很多事情，从 DNS 解析和 SSL 协商，到服务器端呈现和数据库查找。网络和服务器响应时间对 web 性能都很关键，因为它们会延迟响应的第一个字节。所有这一切都必须在之前发生*网站会发送数兆字节的图片和 JavaScript，这会进一步降低加载速度。因此，如果 TTFB 不够快，再多的前端优化也无法为用户提供快速体验。这使得它成为衡量主机提供商的一个很好的标准。*

我已经和很多人分享了这个数据，很多时候他们的第一反应是翻白眼，好像在说“别再做 TTFB 研究了”。但这与其他 TTFB 研究不同。据我所知，Chrome UX 报告是同类报告中唯一一份关于真实用户体验的数据集，而不是基于实验室的测试。这使得它能够独一无二地回答诸如“一个给定的网站有多快？”因为它的方法论是基于用户*实际上*如何体验性能。

今天我[发布](https://twitter.com/rick_viscomi/status/1149146278653284352)一项名为[的新研究，我的主持人快了吗？](https://ismyhostfastyet.com/)使用 CrUX 数据集的主机性能排行榜。它还利用 [HTTP 存档](https://httparchive.org/)数据集，通过在响应头中寻找它们的签名来识别网站的主机。例如，一些主机在`X-Powered-By`报头中自我识别。

其工作方式是通过在 BigQuery 上一起查询数据集，聚合每个主机的网站数量，以及它们的 TTFB 性能分布:

```
#standardSQL
SELECT
  CASE 
   WHEN platform = 'seravo' THEN 'Seravo'
   WHEN platform = 'automattic.com/jobs' THEN 'Automattic' 
   WHEN platform = 'x-ah-environment' THEN 'Acquia'
   WHEN platform = 'x-pantheon-styx-hostname' THEN 'Pantheon'
   WHEN platform = 'wpe-backend' THEN 'WP Engine'
   WHEN platform = 'x-kinsta-cache' THEN 'Kinsta'
   WHEN platform = 'hubspot' THEN 'HubSpot'
   WHEN platform = '192fc2e7e50945beb8231a492d6a8024' THEN 'Siteground'
   WHEN platform = 'x-github-request' THEN 'GitHub'
   WHEN platform = 'alproxy' THEN 'AlwaysData'
   ELSE NULL
  END AS platform,
  client,
  COUNT(DISTINCT origin) AS n,
  SUM(IF(ttfb.start < 200, ttfb.density, 0)) / SUM(ttfb.density) AS fast,
  SUM(IF(ttfb.start >= 200 AND ttfb.start < 1000, ttfb.density, 0)) / SUM(ttfb.density) AS avg,
  SUM(IF(ttfb.start >= 1000, ttfb.density, 0)) / SUM(ttfb.density) AS slow
FROM
  `chrome-ux-report.all.201906`,
  UNNEST(experimental.time_to_first_byte.histogram.bin) AS ttfb
JOIN
  (SELECT _TABLE_SUFFIX AS client, url, REGEXP_EXTRACT(LOWER(CONCAT(respOtherHeaders, resp_x_powered_by, resp_via)), 
      '(seravo|x-kinsta-cache|automattic.com/jobs|x-ah-environment|x-pantheon-styx-hostname|wpe-backend|hubspot|192fc2e7e50945beb8231a492d6a8024|x-github-request|alproxy)')
    AS platform
  FROM `httparchive.summary_requests.2019_06_01_*`)
ON
  client = IF(form_factor.name = 'desktop', 'desktop', 'mobile') AND
  CONCAT(origin, '/') = url
WHERE
  platform IS NOT NULL
GROUP BY
  platform,
  client
ORDER BY
  n DESC 
```

Enter fullscreen mode Exit fullscreen mode

网站按照快速 TTFB 性能的顺序呈现结果:

[![](img/ac88dff4ec10e1a64470ab6c0fd143d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zzDsGk19--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u45kps1d8t8ppn9zxxz3.png)

数据集每月更新，因此我们将能够跟踪主机如何随着时间的推移而改进(或不改进)。添加新主机也很容易，所以如果你知道如何检测我们遗漏的任何东西，请[贡献](https://github.com/rviscomi/ismyhostfastyet/blob/master/README.md#contribute)。