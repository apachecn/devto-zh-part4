# JSON 解析的调整更少(1)

> 原文：<https://dev.to/lbonanomi/tweaking-less-1-for-json-parsing-10km>

我经常使用`less`(我敢打赌[你也会用](https://dev.to/ecologic/what-are-your-five-most-used-terminal-commands-1hmd))，因为`less`能读懂你的想法:

*   对一个文本文件运行它，它会整齐地一次显示一页。
*   对一个 gzipped tarball 运行它，它会显示里面文件的索引。
*   针对 RPM 运行它，它会显示文件索引和 changelog。
*   对一个平面 JSON 文件运行它，它会显示:

```
{"data":{"__schema":{"queryType":{"name":"Query"},"mutationType":{"name":"Mutation"},"subscriptionType":null,"types":
[{"kind":"SCALAR","name":"Boolean","description":"Represents `true` or `false` values.","fields":null,"inputFields":null,
"interfaces":null,"enumValues":null,"possibleTypes":null},{"kind":"SCALAR","name":"String","description":"Represents 
textual data as UTF-8 character sequences. This type is most often used by GraphQL to represent free-form human-readable 
text.","fields":null,"inputFields":null,"interfaces":null,"enumValues":null,"possibleTypes":null},{"kind":"OBJECT",
"name":"Query","description":"The query root of GitHub's GraphQL interface.","fields":[{"name":"codeOfConduct",
"description":"Look up a code of conduct by its key","args":[{"name":"key","description":"The code of conduct's key",
```

...一墙未解析的 JSON 数据。

让我们用一个 shell 脚本编辑和一个简单的函数来教这只忠实的狗一些新的技巧。

### 少而平的 JSON 文件

`less`实用程序的一个非常酷的特性是 shell 脚本`lesspipe.sh`，它用在输入文件上运行的命令的输出*替换输入文件*的内容*，并根据输入文件的扩展名选择规则。我们可以很容易地添加另一个扩展处理程序到规则中。json "文件用一个`jq`的输出替换原始数据，运行如下:*

`*.json) jq . < "$1"; handle_exit_status $? ;;`

现在我们可以通过运行`export LESSOPEN="||$HOME/lesspipe.sh %s"`来更新我们自己的`$LESSOPEN`

### 少和流 JSON

调整`lesspipe.sh`的问题在于它对分页 STDOUT 没有任何作用，这是我探索 REST 或 GraphQL 端点的常用工作流。我们可以通过查看前两个字符是否是 JSON 文档的合法开头(“”、“[”或““”)来尽最大努力进行检查，而不是缓冲潜在的巨大 JSON 块并使用“大”工具来确定文件类型。如果 STDOUT 作为合法的 JSON 开始，我们将尝试把它提供给`jq | less`，如果`jq`阻塞，就把它推给`less`。这种方法将花费我们一些额外的分叉，但是我认为简单是值得的。

```
function jqless() {
    if [[ -e "$1" ]]
    then
        /bin/less "$@"
    else read -u 0 -n2 X
        [[ $(echo "$X" | tr -d [:space:] | cut -c1,2 | egrep "\[|\"|{"{2}) ]] &&\
        (((echo -n "$X" && cat) | jq . | /bin/less ) || (echo -n "$X" && cat) | /bin/less ) ||\
        (echo -n "$X" && cat) | /bin/less
    fi
} 
```