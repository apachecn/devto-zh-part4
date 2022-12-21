# 更高级的管道组成

> 原文：<https://dev.to/patroza/more-advanced-pipeline-composition-4jk7>

当我们向我们的 belt 添加更多的功能组合工具时，我们可以开始组合既简洁又有描述性的用例管道。

## 运算符

*   来自上一篇文章:`map` : `(value => newValue) => Result<newValue, ...>`
*   `flatMap` : `(value => newResult) => newResult`
*   `toTup` : `(value => newValue) => readonly [newValue, value]`
*   `tee` : `(value => any) => Result<value, ...>`
*   `resultTuple` : `(...[Result<..., ...>]) => Result<readonly [value, value2, ...], error[]>`

## 样品

```
type CreateError = CombinedValidationError | InvalidStateError | ValidationError | ApiError | DbError

// ({ templateId: string, pax: Pax, startDate: string }) => Result<TrainTripId, CreateError>
pipe(
  flatMap(validateCreateTrainTripInfo), // R<{ pax: PaxDefinition, startDate: FutureDate, templateId: TemplateId}, CombinedValidationError>
  flatMap(toTup(({ templateId }) => getTrip(templateId))), // R<[TripWithSelectedTravelClass, { pax... }], ...>
  map(([trip, proposal]) => TrainTrip.create(proposal, trip)), // R<TrainTrip, ...>
  tee(db.trainTrips.add), // R<TrainTrip, ...>
  map(trainTrip => trainTrip.id), // R<TrainTripId, ...>
) 
```

`validateCreateTrainTripInfo`功能:

```
// ({ templateId: string, pax: Pax, startDate: string}) => Result<({ pax: PaxDefinition, startDate: FutureDate, templateId: TemplateId }), CombinedValidationError>
pipe(
  flatMap(({ pax, startDate, templateId }) =>
    resultTuple(
      PaxDefinition.create(pax).pipe(mapErr(toFieldError("pax"))),
      FutureDate.create(startDate).pipe(mapErr(toFieldError("startDate"))),
      validateString(templateId).pipe(mapErr(toFieldError("templateId"))),
    ).pipe(mapErr(combineValidationErrors)),
  ),
  map(([pax, startDate, templateId]) => ({
    pax, startDate, templateId,
  })),
) 
```

*均摘自[use cases/create train trip . ts](https://github.com/patroza/fp-app-framework/blob/master/samples/basic/src/TrainTrip/usecases/createTrainTrip.ts)T3】*

这个验证器促进了域级别的验证，不要与 REST 级别的 DTO 验证混淆。它为域工厂`TrainTrip.create`的输入准备经验证的 DTO 数据。这些领域规则被整齐地打包在值对象`FutureDate`和`PaxDefinition`中，减少了工厂中的复杂性和知识蔓延。

同样，如果 [tc39 提议-管道-操作者](https://github.com/tc39/proposal-pipeline-operator)能够实现，我们就能写出更简洁漂亮的代码。

### 组合验证错误

我们将每个`ValidationError`封装到一个`FieldValidationError`中，这样我们就有了错误上下文中的字段名，然后在最后我们将它们组合成一个错误，这个错误可以很容易地被检查并序列化为 REST api 上的 JSON，供客户端使用和检查。

例如:

```
if (err instanceof CombinedValidationError) {
  ctx.body = {
    fields: combineErrors(err.errors),
    message,
  }
  ctx.status = 400
}

const combineErrors = (ers: any[]) => ers.reduce((prev: any, cur) => {
  if (cur instanceof FieldValidationError) {
    if (cur.error instanceof CombinedValidationError) {
      prev[cur.fieldName] = combineErrors(cur.error.errors)
    } else {
      prev[cur.fieldName] = cur.message
    }
  }
  return prev
}, {}) 
```

## 来源

和往常一样，您也可以在 [patroza/fp-app-framework](https://github.com/patroza/fp-app-framework) 找到完整的框架和示例源代码

## 接下来是什么

在本系列的下一篇文章中，我计划研究这个问题:“什么时候返回错误，什么时候抛出错误？”