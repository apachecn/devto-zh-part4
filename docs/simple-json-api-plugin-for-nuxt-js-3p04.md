# 简单的 JSON:nuxt . js 的 API 插件

> 原文：<https://dev.to/coolgoose/simple-json-api-plugin-for-nuxt-js-3p04>

大家好，

到目前为止，我让自己每个周末都做一些兼职项目。

也就是说，我是 JSON:API 的忠实粉丝，在我寻求简化前端消费的过程中，我最终选定了一个漂亮的 JSON:API 客户端，名为[吞吃](https://github.com/twg/devour)。它有足够的魔力使它变得有价值，而不至于过分“神奇”。

为此，我做了一个小的 nuxt.js 插件，无论它是在组件中还是在 vuex 商店中，我都可以使用它。

```
import JsonApi from "devour-client";
import * as apis from "~/api/index.js";

export default (ctx, inject) => {
  // eslint-disable-next-line no-undef
  const jsonApi = new JsonApi({ apiUrl: CONFIG.backend.url });
  const apiMap = {};

  for (const [key, value] of Object.entries(apis)) {
    jsonApi.define(
      apis[key].structure().modelName,
      apis[key].structure().fields
    );
  }

  for (const [key, value] of Object.entries(apis)) {
    // eslint-disable-next-line no-undef
    apiMap[key] = new apis[key](jsonApi, CONFIG);
  }

  apiMap.jsonApi = jsonApi;

  // Inject into the nuxt/vue context
  ctx.$api = apiMap;
  inject("api", apiMap);
}; 
```

Enter fullscreen mode Exit fullscreen mode

`CONFIG`常量是由下面的库 [config](https://www.npmjs.com/package/config) 和[注入](https://github.com/lorenwest/node-config/wiki/Webpack-Usage#option-1)到 app 中生成的，方便访问。

为了使用这个插件，`index.js`是我们将要使用的各种 API *模型*的通用导出。

```
export { default as InventoryItem } from "./InventoryItem"; 
```

Enter fullscreen mode Exit fullscreen mode

*模型*只不过是一个简单的对象，它返回 api 所需的配置，并通过自己的方法公开它。

我宁愿不要将应用程序与吞食紧密耦合，所以将它们保留在我们自己的类中应该可以更容易地交换它，和/或在需要时直接使用 axios。

```
export default class InventoryItem {
  constructor(api, config) {
    this.api = api;
    this.config = config;
  }

  static structure() {
    return {
      modelName: "inventory-item",
      fields: {
        name: ""
      }
    };
  }

  getInventoryItems() {
    return this.api.findAll(InventoryItem.structure().modelName);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我很想知道你们是如何在自己的 SPA 应用中分离 API 调用的