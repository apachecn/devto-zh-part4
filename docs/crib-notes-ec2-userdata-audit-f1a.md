# crib-注释:EC2 用户数据审计

> 原文：<https://dev.to/ferricoxide/crib-notes-ec2-userdata-audit-f1a>

有时，我发现我会回到一个客户/项目，忘记他们如何部署他们的 ec2 什么是“正常的”。如果我知道一个给定的客户/项目倾向于部署包含用户数据的 ec2，但是他们没有很好地记录他们倾向于为所述用户数据做什么，我发现下面的 BASH scriptlet 对于让我自己回到事情的轨道上来是有用的:

```
for INSTANCE in $( aws ec2 describe-instances --query 'Reservations[].Instances[].InstanceId' --output text )
do
   printf "###############\n# %s\n###############\n" "${INSTANCE}"
   aws ec2 describe-instance-attribute --instance-id "${INSTANCE}" --attribute userData --query 'UserData.Value' --output -d | base64 -d -
   echo
done | tee /tmp/EC2-UserData.log 
```

解释一下，上面所做的是:

1.  使用${INSTANCE}作为迭代值启动 for 循环
2.  在每次迭代中，注入${INSTANCE}的值是从 aws ec2 describe-instances 命令的一行输出中获得的。通常，这个命令输出一个 JSON 文档，其中包含关于 account-region 中每个实例的大量信息。使用- query 选项，输出被限制为只输出每个 EC2 实例的 InstanceId 值。然后通过 sed 进行处理，去掉多余的字符，得到一个干净的 EC2 实例 id 列表。
3.  最初的 printf 行创建了一点输出头。这将更容易对输出进行筛选，并保持每个迭代实例的单独用户数据内容是独立的
4.  实例 UserData 被认为是给定 EC2 实例的一个属性。AWS ec2 describe-instance-attribute 命令用于从目标 EC2 中实际提取这些内容。我*可能*已经使用了查询过滤器来约束我的输出。然而，我选择使用 jq，因为它允许我约束输出并进行输出清理，消除了我在循环初始化中使用的那种复杂的 sed 语句的需要(cygwin 的 jq 在今天早上我试图在循环初始化阶段使用它时崩溃了——以防您对不一致的约束/清理方法感到疑惑)。因为 UserData 输出存储为 base64 编码的字符串，所以我必须通过 BASE64 实用程序将清理后的输出进行管道传输，以取回我的纯文本数据。
5.  我在输出流中插入了一个结束空行(通过 echo 命令),使捕获的输出更容易浏览。
6.  我喜欢观察我的 scriptlet 的进展，但仍然喜欢将输出捕获到一个文件中供以后阅读，因此我通过 tee 管道传输整个循环的输出，以便我可以在查看时捕获。

我可以设置它，使每个实例的数据都转储到一个单独的输出文件中。这将节省对 printf 和 echo 行的需求。然而，我喜欢有一个大文件来细读(而不是在大量的单个文件中搜寻)...并且单个文件打开/关闭动作比大量的打开/关闭动作稍微快一些。

在一个有数百个 ec2 的帐户区域中，我可能会更有选择地使用哪个实例 id 来启动我的循环。我会在 aws ec2 describe-instances 命令中使用- filter 语句——可能通过 VPC ID 和一两个其他选择器进行过滤。