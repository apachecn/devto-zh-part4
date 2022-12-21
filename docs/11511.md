# 如何在 React UI 中动态显示 JSON 数据，不仅仅是普通数据，还包括文本间有链接的数据

> 原文：<https://dev.to/satishreddyu/how-to-display-json-data-in-react-ui-dynamically-not-just-plain-data-but-with-data-having-links-in-between-the-text-too-1c88>

{
"test1" : {
"问题":"测试 1 "，
"答案":"测试答案"，
"问题":"测试 2 "，
"答案":"测试答案 2 #谷歌链接，回答一些和所有"
}

那么，我们如何才能在文本中间呈现像这样的链接的答案文本呢？由于 JSON 中的答案节点将是普通文本，如何使它动态地呈现数据和链接