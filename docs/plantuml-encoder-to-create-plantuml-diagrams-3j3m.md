# PlantUML 编码器创建 PlantUML 图

> 原文：<https://dev.to/samandar/plantuml-encoder-to-create-plantuml-diagrams-3j3m>

```
from functorflow import f

r = f('plantuml', 'A -> B: Hello')
# print(r) # SrJGjLDmibBmICt9oGS0 
img = 'http://www.plantuml.com/plantuml/img/{}'.format(r)

print(img) 
```

Enter fullscreen mode Exit fullscreen mode

PlantUML 是一个允许快速编写以下内容的组件:

*   程序表
*   用例图
*   类图
*   活动图(这里是遗留语法)
*   组件图
*   状态图
*   对象图
*   配置图
*   时间图

还支持以下非 UML 图:

*   线框图形界面
*   建筑图
*   规范和描述语言(SDL)
*   Ditaa diagram
*   甘特图
*   思维导图
*   工作分解结构图
*   带 AsciiMath 或 JLaTeXMath 符号的数学
*   实体关系图

更:[http://plantuml.com/](http://plantuml.com/)

[https://repl.it/@functorflow/plantuml?lite=true](https://repl.it/@functorflow/plantuml?lite=true)

> 停止浪费时间安装和配置 Python 库。马上使用它们。 [FunctorFlow](https://functorflow.org) 立即为您自动安装和自动配置它们。