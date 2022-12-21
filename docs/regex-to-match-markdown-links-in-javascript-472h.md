# 正则表达式匹配 Javascript 中的降价链接

> 原文：<https://dev.to/davidwells/regex-to-match-markdown-links-in-javascript-472h>

> 原文发布于 [davidwells.io](https://davidwells.io/snippets/regex-match-markdown-links)

方便的小正则表达式解析 markdown 文件中的链接。

markdown 中的链接如下:

```
[View the analytics docs](https://getanalytics.io/) 
```

要使用正则表达式以编程方式解析`.md`链接，请使用以下模式:

```
/* Match only links that are fully qualified with https */
const fullLinkOnlyRegex = /^\[([\w\s\d]+)\]\((https?:\/\/[\w\d./?=#]+)\)$/

/* Match full links and relative paths */
const regex = /^\[([\w\s\d]+)\]\(((?:\/|https?:\/\/)[\w\d./?=#]+)\)$/

const string = "[View the analytics docs](https://getanalytics.io/)"

const myMatch = string.match(regex)

console.log(myMatch)
/* ["[View the analytics docs](https://getanalytics.io/)", "View the analytics docs", "https://getanalytics.io/", index: 0, input: "[View the analytics docs](https://getanalytics.io/)", groups: undefined] */

// de-structure the array
const [ full, text, url ] = myMatch

console.log(text)
// 'View the analytics docs'

console.log(url)
// 'https://getanalytics.io/' 
```

参见 [regex101](https://regex101.com/r/m9dndl/1) 上的演示

## 解析文件中的所有链接

要获取一个文件的所有链接，你可以使用这个:

```
const regexMdLinks = /\[([^\[]+)\](\(.*\))/gm

// Example md file contents
const mdContents = `
Lorem ipsum dolor sit amet, consectetur adipiscing elit..

[hello link](/admin/table_edit/table_edit.cfm?action=edit&table_name=organizationsXcategories)

Lorem ipsum dolor sit amet, consectetur adipiscing elit..

[otherLink](https://google.com)

Lorem ipsum dolor sit amet, consectetur adipiscing elit..

[third link](https://google.com)
`

const matches = mdContents.match(regexMdLinks)
console.log('links', matches)

const singleMatch = /\[([^\[]+)\]\((.*)\)/
for (var i = 0; i < matches.length; i++) {
  var text = singleMatch.exec(matches[i])
  console.log(`Match #${i}:`, text)
  console.log(`Word  #${i}: ${text[1]}`)
  console.log(`Link  #${i}: ${text[2]}`)
} 
```

如果您对此片段有任何问题或更新，请在下面发表评论！