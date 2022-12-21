# 使用命令行工具漂亮地打印 JSON

> 原文：<https://dev.to/anuragrana/pretty-print-json-using-the-command-line-tools-1bpk>

备选方案 1。使用 python
`cat FileName.json | python -m json.tool`

选项 2。颜色和多种选择。
`cat FileName.json~ | jq ''`

选项 3。漂亮的打印，语法高亮。
`echo '{"foo": "bar"}' | python -m json.tool | pygmentize -g`