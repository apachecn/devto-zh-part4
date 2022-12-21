# 基于字符串的问题。

> 原文：<https://dev.to/pykashchain/string-based-question-5782>

问题:-编码三个字符串:Anand 被分配了一项任务，为任何给定的三个字符串提出一种编码机制。他提出了以下计划。第一步:-给定任意三根弦，将每根弦分成三部分。
举个例子——如果三串在下面:
输入 1:“约翰”
输入 2:“张诗钟”
输入 3:“詹纳丹”
“约翰”要拆分成“J”、“哦”、“n”，分别作为前、中、后三部分。
“张诗钟”要泼成“jo”、“h”、“ny”分别做前、中、后。
“Janardhan”要拆分成“Jan”、“ard”、“han”分别作为前、中、后部分。
即，如果字符串中的字符数是 3 的倍数，那么每个拆分部分将包含相同数量的字符，如“Janadhan”示例所示。
如果字符串中的字符数不是 3 的倍数，并且有一个字符多于 3 的倍数，那么中间部分会得到多出来的字符，如“john”的例子所示。
如果字符串中的字符数不是 3 的倍数，并且有两个字符多于 3 的倍数，那么前面和结尾部分将各得到一个额外的字符，如“张诗钟”的例子所示。
第二步:按照下面指定的连接规则连接(join)字符串的前面、中间和结尾部分，以形成三个输出字符串。
Output 1:input 1 的前部 input 2 的中部 input 3 的端部
Output 2:-input 1 的中部 input 2 的端部 input 3 的前部
Output 3:input 1 的端部 input 2 的前部 input 3 的中部
例如，对于上面的示例输入字符串:
Output 1 = " J "+" h "+Han " = " JH Han "
Output 2 = " oh "+" ny "+" Jan " = =

第三步:-
根据输出处理规则处理结果输出字符串。经过以上两步，我们现在将有三个输出字符串。根据下面的规则-
“切换字符串中每个字符的大小写”，仅需要对第三个输出字符串进行进一步处理，即在第三个输出字符串中，所有小写字符都应变成大写，反之亦然。
例如，对于上面的示例字符串，output3 是“nJoard”，所以在应用了 toggle 规则之后。Output3 应该变成“NjOARD”。

最终结果–应用上述三个步骤后的三个输出字符串，即上例中的结果。
output 1 = " Jnhan "
output 2 = " ohnyJan '
output 3 = " NJOARD "
帮助 Anand 编写一个程序来完成上述任务。

[https://repl.it/@VikasTomar/Important-Question-for-amcat?lite=true](https://repl.it/@VikasTomar/Important-Question-for-amcat?lite=true)