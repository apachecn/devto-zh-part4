# EBS 安装的应用程序

> 原文：<https://dev.to/iarehilton/ebs-installed-applications-13e5>

用于查找 EBS 已安装应用程序的脚本

```
SELECT
fa.application_short_name,
fa.application_name,
fpi.patch_level,
DECODE(fpi.status, 'I', 'Installed', 'S', 'Shared', 'N', 'Inactive', fpi.status) status,
fpi.db_status
FROM
fnd_product_installations fpi,
fnd_application_vl fa
WHERE
1 = 1
AND fa.application_id = fpi.application_id
ORDER BY
fa.application_short_name; 
```