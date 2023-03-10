# 第一个 NPM 包，带有 jsonSchema 验证的快速自动路由

> 原文：<https://dev.to/dechamp/first-npm-package-express-autoroute-with-jsonschema-4cma>

这是我的第一个包裹。我目前正把它用于我的另一个个人项目，并决定制作自己的包，供其他人使用。

我有点紧张，因为我没有真正公开分享我的工作，但我以前也写过关于[面对你的恐惧](https://dev.to/dechamp/the-results-of-putting-your-fears-to-the-side-42gg)。

这个包旨在使路由设置变得清晰和可配置，同时增加了为所有路由添加中间件的能力，也可以为单个路由添加中间件。

设置非常简单，只有几行代码。

```
 const {default: autoRoute, RouteRequestJsonSchemaValidator, RouteResponseJsonSchemaValidator} = require('express-auto-route');
//express server
const server = ...;
// see exmple below.
const routes = ...;

autoRoute({
  server,
  routes,
  middlewares: [
    RouteRequestJsonSchemaValidator,
    RouteResponseJsonSchemaValidator,
    // your custom middleware
  ],
}); 
```

Enter fullscreen mode Exit fullscreen mode

然后只需通过配置文件添加您的设置。

```
const routes = [
    {
        id: "resourceGet",
        method: "GET",
        modulePath: "/routes/resource/get.js",
        path: "/resources",
        responseJsonSchema: "/path/to/schemas/resources.schema.json",
    },
    {
        id: "resourceGetUuid",
        method: "GET",
        modulePath: "/routes/resource/get.js",
        path: "/resources/:uuid",
        requestJsonSchema: {
            query: "/path/to/schemas/getByUuid.schema.json"
        },
        responseJsonSchema: "/path/to/schemas/resource.schema.json"
    }
] 
```

Enter fullscreen mode Exit fullscreen mode

然后只需添加我们的模式，正如您在下面看到的，我们完全有能力为强大的干净解决方案组合模式。

/path/to/schemas/resources . schema . JSON

```
{  "$schema":  "http://json-schema.org/draft-07/schema#",  "title":  "resources",  "type":  "array",  "items":  {  "$ref":  "schema/resource.schema.json"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

/path/to/schemas/resources . schema . JSON

```
{  "$schema":  "http://json-schema.org/draft-07/schema#",  "$ref":  "#/definitions/resource",  "definitions":  {  "resource":  {  "title":  "resource",  "type":  "object",  "required":  [  "name",  "path",  "description"  ],  "additionalProperties":  false,  "properties":  {  "uuid":  {  "$ref":  "schema/uuid.schema.json"  },  "name":  {  "type":  "string",  "title":  "Name",  "minLength":  2,  "maxLength":  60  },  "path":  {  "type":  "string",  "title":  "Path",  "minLength":  1,  "pattern":  "^\\/[\\w\\-.~%!$&'()*+,;=:@\\/]*$"  },  "description":  {  "type":  "string",  "title":  "Description",  "minLength":  1,  "maxLength":  254  },  "createdAt":  {  "type":  "string",  "format":  "date-time"  },  "updatedAt":  {  "type":  "string",  "format":  "date-time"  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

/path/to/schemas/getbyuuid . schema . JSON

```
{  "$schema":  "http://json-schema.org/draft-07/schema#",  "title":  "getByUuid",  "type":  "object",  "required":  [  "uuid"  ],  "properties":  {  "uuid":  {  "$ref":  "schema/uuid.schema.json"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

/path/to/schemas/uuid . schema . JSON

```
{  "$schema":  "http://json-schema.org/draft-07/schema#",  "title":  "UUID",  "$ref":  "#/definitions/uuid",  "definitions":  {  "uuid":  {  "type":  "string",  "pattern":  "^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

模式的路径是通过配置和中间件提取的。因此，您可以使用模式，或者如果您愿意，可以放弃中间件，不再需要模式。

正如我上面提到的，您还可以选择单独的路由中间件！

```
const routes = [
    {
        id: "resourceGet",
        method: "GET",
        modulePath: "/routes/resource/get.js",
        path: "/resources",
        middleware: "/path/to/your/customMiddleware.js",
    }
] 
```

Enter fullscreen mode Exit fullscreen mode

希望你喜欢它！请让我知道你的想法！

[https://www.npmjs.com/package/@dechamp/express-auto-route](https://www.npmjs.com/package/@dechamp/express-auto-route)