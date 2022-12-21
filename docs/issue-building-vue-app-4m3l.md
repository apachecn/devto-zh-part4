# 构建 Vue 应用程序的问题

> 原文：<https://dev.to/daledevuk/issue-building-vue-app-4m3l>

嗨，

我有一个正在开发的应用程序，现在我想把它部署在某个地方，而不是在本地运行。

在本地，这运行得很好`vue-cli-service serve`然而，当我运行`vue-cli-service` build 时，我得到构建错误，说它找不到模块，基本上每次导入我自己的模块都会失败，例如…

```
<script lang="ts">
    import {Component, Vue} from 'vue-property-decorator';
    import MarketOpeningTimeBadge from "@/components/MarketOpeningTimeBadge";

    @Component({
        components: {MarketOpeningTimeBadge}
    })
    export default class MarketOpeningTimes extends Vue {

    }
</script> 
```

错误示例…

```
 error  in C:/projects/Research and Development/daleellis/sentiment-investment/client/src/components/MarketOpeningTimes.vue

ERROR in C:/projects/Research and Development/daleellis/sentiment-investment/client/src/components/MarketOpeningTimes.vue
12:36 Cannot find module '@/components/MarketOpeningTimeBadge'.
    10 | <script lang="ts">
    11 |     import {Component, Vue} from 'vue-property-decorator';
  > 12 |     import MarketOpeningTimeBadge from "@/components/MarketOpeningTimeBadge";
       |                                    ^
    13 | 
    14 |     @Component({
    15 |         components: {MarketOpeningTimeBadge}
There are no issues when I run serve locally in regards to the application working, but I notice that also displays the errors in the console. 
```

我需要做什么来解决构建问题？

感谢

```
"vue": "^2.6.10",
"vue-class-component": "^7.0.2",
"vue-property-decorator": "^8.1.0",
"vue-router": "^3.0.3",
"vue-socket.io": "^3.0.7",
"vuetify": "^1.5.5",
"vuex": "^3.0.1",
"vuex-class": "^0.3.2" 
```