# 不同语言中方便的 selenium 选择器语法

> 原文：<https://dev.to/vsreelasya/a-handy-selenium-selector-syntax-in-different-languages-o90>

**Java**:
driver . get([http://Google . com '](http://google.com'))
*ID*-driver . find element(by . ID(" ui-div "))；
*CSS*-driver . find element(by . CSS selector(" text-t "))；
*Xpath*-driver . find element(by . Xpath("//div[[@ id](https://dev.to/id)= ' ui-div ']")；
*名称*-driver . find element(by . Name("元素名称"))；
*Link*-driver . find element(by . Link(" Link "))
*标记名*-driver . find element(by . TagName("标记名"))

**Python**:
driver . get([http://Google . com '](http://google.com'))
*ID*-driver . find _ element _ by _ ID(" idname ")
*CSS*-driver . find _ element _ by _ CSS _ selector(' body>div>button ')
*Xpath*-driver . find _ element _ by _ Xpath(' body/Name ')

**JavaScript**:
driver . get([http://Google . com '](http://google.com'))
*ID*-driver . find element(web driver。By.id("密码"))；
*CSS*-driver . find element(web driver。by . CSS(' body>div>div))；
*Xpath*-driver . find element(web driver。by . XPath(' body/name ')；
*名称*-driver . find element(web driver。by . name(' q ')；

**c#**
驱动程序。导航()。GoToUrl("[http://www . Google . com "](http://www.google.com%22))；
*ID* -驱动。FindElementById(" drop able ")；
*CSS* -驱动。FindElementByXPath("//[[@ id](https://dev.to/id)= \ " drop able \ "]/p ")
*Xpath*-驱动程序。findelementbycss selector(" div . form-actions>button ")
*Name*-driver。FindElementByName("密码")