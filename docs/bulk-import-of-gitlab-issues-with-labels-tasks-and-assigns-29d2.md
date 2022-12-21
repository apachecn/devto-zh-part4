# 批量导入带有标签、任务和分配的 gitlab 问题

> 原文：<https://dev.to/c33s/bulk-import-of-gitlab-issues-with-labels-tasks-and-assigns-29d2>

使用 csv 文件导入 gitlab 问题能够完成所有漂亮的快速操作，允许您在导入时分配人员并为问题添加标签。

示例 csv 文件:

```
"title";"description"
"Simple Issue";"Simple text"
"Complex Issue";"lorem ipsum

/label ~Todo ~label1 ~label2
/assign @myusername"
"Task Issue";"test the following
- [ ] test1
- [ ] test2
- [ ] test3

/label ~Todo ~label1 ~label2"
"Codeblock Issue";"\`\`\` 
code block line1
code block line2
\`\`\`

/label ~Todo ~label1" 
```

我在反斜杠前加了一个反斜杠，因为否则 dev.to 会错误地显示帖子。

然后转到问题列表，使用右上角的小按钮“导入 CSV”