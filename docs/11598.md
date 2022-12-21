# 沟渠控制台日志

> 原文：<https://dev.to/rockarts/ditch-console-log-13kg>

人们喜欢使用 console.log 来快速调试值，但从 Chrome 73 开始，有一种更快的方法。您可以查看文件的源代码，然后不用设置断点，而是在同一个位置右键单击并选择 Add Logpoint。当点击 logpoint 时，输出将显示在控制台窗口中。您还可以将日志点添加到生产代码中。

[![Add logpoint](img/4b158c71fc3d7e80ac1a89158e13df93.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rGO11VZl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developers.google.com/web/updatimg/2019/01/add-logpoint.png)

[![Add logpoint expression](img/e512d8579115efe7defb1657515fae8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zsNVE6_g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developers.google.com/web/updatimg/2019/01/logpoint-expression.png)

[![Logpoint console](img/a5b5c10b0a0d6a8c6c7c8d4dda9b77e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mpY29RXm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developers.google.com/web/updatimg/2019/01/logpoint-result.png)

如果您使用 Firefox，您还可以通过右键单击并选择 add log 来添加日志点