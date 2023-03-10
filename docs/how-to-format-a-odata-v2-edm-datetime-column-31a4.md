# 如何格式化 OData v2 Edm？日期时间列

> 原文：<https://dev.to/kfowlks/how-to-format-a-odata-v2-edm-datetime-column-31a4>

## 用例

在许多情况下，如果您使用的 OData v2 实体带有类型为 ***Edm 的列。DateTime*** ，你将需要在某个时候构造一个合适的 OData 查询字符串。如果您不知道 Edm 的正确语法，这可能有点困难。日期时间类型。

```
/sap/opu/odata/sap/ZGW_JOB_CLASSIFICATION_SRV/JobSet?$format=json&$filter=BeginDate le datetime'2019-08-02T04:00:00' and EndDate ge datetime'2019-08-02T04:00:00'&$select=JobCode,Title,Level,PersonSubArea,Link 
```

Enter fullscreen mode Exit fullscreen mode

> **注意:**乍一看，您可能会注意到这个查询字符串在时间戳值前面有一个* *DateTime ** 前缀。

```
/sap/opu/odata/sap/ZGW_JOB_CLASSIFICATION_SRV/JobSet?$format=json&$filter=BeginDate le datetime'2019-08-02T04:00:00' and EndDate ge datetime'2019-08-02T04:00:00'&$select=JobCode,Title,Level,PersonSubArea,Link 
```

Enter fullscreen mode Exit fullscreen mode

在 SAPUI5 框架的内部，他们有一个优秀的助手库来帮助添加这样的前缀。

### 库

sap.ui.model.odata.odatautils

> **注意:**为了在你的控制器中使用这个库，你需要把它添加到你的 ***sap.ui.define*** 数组中，就像这样`"sap/ui/model/odata/ODataUtils"`。

### 法

formatValue( date, EdmType)

### 示例用法

`ODataUtils.formatValue(beginDate, "Edm.DateTime")`

正如您将在下面的示例中看到的，有一个警告，带有时间戳的 OData 查询需要进行 URL 编码，因此为了做到这一点，我们需要使用内置的 javascript 函数 encodeURIComponent()

#### 举例:

```
var sBindUrl = "/JobSet(BeginDate=" + encodeURIComponent(ODataUtils.formatValue(beginDate, "Edm.DateTime")) +  
",JobCode='" + oEvent.getParameter("arguments").jobCode + "')?$expand=JobDescriptionSet" +  
"&$filter=BeginDate le " + encodeURIComponent(ODataUtils.formatValue(beginDate, "Edm.DateTime")); 
```

Enter fullscreen mode Exit fullscreen mode