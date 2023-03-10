# 在 Angular 中使用 API 时 JSON 数据的类型

> 原文：<https://dev.to/rudolfolah/types-for-json-data-when-consuming-apis-in-angular-2lmj>

Angular 可以使用 TypeScript 对来自 API 的 JSON 数据进行类型检查，这很简单。不太整洁的是，一些 API 没有遵循其字段名的 camel-case 命名约定。

## 问题

例如，如果您有一个提供 API 的 Rails 服务器，并且响应返回一个 JSON 对象，该对象有三个字段:`first_name`、`email_address`和`postal_code`。

JSON 响应的一个例子可能是这样的:

```
{  "first_name":  "Rudolf",  "email_address":  "rudolf@localhost",  "postal_code":  "A1B 2C3"  } 
```

在 TypeScript 中，您必须使界面看起来像这样:

```
interface EntryForm {
  first_name: string;
  email_address: string;
  postal_code: string;
} 
```

它不遵循惯例，如果字段名发生变化，这种变化将会在整个代码库中传播。

## 解决方案:适配器模式和`adapt`功能

我在这篇博文[“在 Angular 中使用 API:模型适配器模式”](https://blog.florimond.dev/consuming-apis-in-angular-the-model-adapter-pattern)中找到了这个问题的解决方案。本质上，我们可以使用[适配器模式](https://en.wikipedia.org/wiki/Adapter_pattern)来解决这个问题:

> 适配器允许两个不兼容的接口一起工作。这是适配器的真实定义。接口可能不兼容，但是内部功能应该适合需要。适配器设计模式通过将一个类的接口转换成客户机期望的接口，允许原本不兼容的类协同工作。

### 界面定义

使用上面的例子，我们可以定义一个通用适配器，将任何 JSON 对象映射到任何其他对象类:

```
function adapt(mapper: any, json: any): any {
  let adaptedObj: any = {};
  const fields: Array<string> = Object.keys(mapper);
  for (let field of fields) {
    const targetField: any = mapper[field];
    adaptedObj[targetField] = json[field];
  }
  return adaptedObj;
} 
```

然后我们可以定义一个特定的映射器:

```
function EntryFormAdapter(json: any): EntryForm {
  const mapper = {
      'first_name': 'firstName',
      'email_address': 'emailAddress',
      'postal_code': 'postalCode'
  };
  return adapt(mapper, json);
} 
```

### 保存类型检查

似乎没有一个好的方法来保持类型检查，因为我们正在用上面的适配器进入`any`类型的领域。

如果您真的需要类型检查，您可以移除`adapt`函数，为 JSON 对象定义一个接口，然后重构`EntryFormAdapter` :

```
interface JsonEntryForm {
  first_name: string;
  email_address: string;
  postal_code: string;
}

function EntryFormAdapter(json: JsonEntryForm): EntryForm {
  return {
    emailAddress: json.email_address,
    firstName: json.first_name,
    postalCode: json.postal_code
  }
} 
```

这将确保在您处理代码时进行类型检查。

### 用法同 HttpClient

要在 Angular 的 HttpClient 中使用它，您需要将来自 HTTP [可观察结果](https://rxjs-dev.firebaseapp.com/api/index/class/Observable)的 JSON 数据响应[通过管道传输和映射](https://blog.angularindepth.com/reading-the-rxjs-6-sources-map-and-pipe-94d51fec71c2#cf90)到适配器

```
class MyService {
  constructor(private http: HttpClient) { }
  get(): Observable<EntryForm> {
    return this.http.get('/path/to/api/').pipe(
      map((json: JsonEntryForm) => EntryFormAdapter(json))
    );
  }
} 
```