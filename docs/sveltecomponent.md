# <sveltecomponent this="{First}">无法创建构造函数</sveltecomponent>

> 原文：<https://dev.to/arpitkrazybee/sveltecomponent-this-first-cant-able-to-create-constructor-442>

我试图将我的应用程序与基本应用程序合并，但它一直显示这个错误
svelte component . componentdimount
node _ modules/react-svelte-component/dist/index . es . js:61
58 | constructor = _ a . this，
59 | props = _ rest(_ a，[" this "])；
60 |岁

> 61 | this.instance = new 构造函数({
> | ^ 62 | target:this . container . current，
> 63 | props:props
> 64 | })；

我创建了一个基本的计数器 ReactApp。尝试使用< swell component this = { First(svelte)file }加载它。我已经导入了

从“react-svelite-component”导入 SvelteComponent
首先从'导入。/first . svelte '；

请帮助某人