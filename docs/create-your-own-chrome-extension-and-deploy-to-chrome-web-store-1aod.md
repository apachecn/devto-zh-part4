# 创建自己的 chrome 扩展——并部署到 Chrome 网络商店

> 原文：<https://dev.to/anu27580168/create-your-own-chrome-extension-and-deploy-to-chrome-web-store-1aod>

### 创建你自己的 chrome 扩展——并部署到 Chrome 网络商店

[Github Readme Markdown](https://chrome.google.com/webstore/detail/github-readme-markdown/paacehodnnofnmhogoclomamladkpabg?hl=en&authuser=0)

> Web 扩展

### [T1】简介](#intro)

首先，我会告诉你这很容易。重要的文件只有一个，就是— ***manifest.json.*** 关于这个文件的一切都要知道—

```
{
_// Required_
 "[**manifest\_version**](https://developer.chrome.com/extensions/manifest/manifest_version)": 2,
 "[**name**](https://developer.chrome.com/extensions/manifest/name#name)": "My Extension",
 "[**version**](https://developer.chrome.com/extensions/manifest/version)": "versionString",

_// Recommended_
 "[**default\_locale**](https://developer.chrome.com/extensions/manifest/default_locale)": "en",
 "[**description**](https://developer.chrome.com/extensions/manifest/description)": "A plain text description",
 "[**icons**](https://developer.chrome.com/extensions/manifest/icons)": {...},

_// Pick one (or none)_
 "[**browser\_action**](https://developer.chrome.com/extensions/browserAction)": {...},
 "[**page\_action**](https://developer.chrome.com/extensions/pageAction)": {...},

_// Optional_
 "action": ...,
 "author": ...,
 "automation": ...,
 "[**background**](https://developer.chrome.com/extensions/background_pages)": {
_// Recommended_
 "[**persistent**](https://developer.chrome.com/extensions/event_pages)": false,
_// Optional_
 "[**service\_worker**](https://developer.chrome.com/extensions/background_pages)":
 },
 "[**chrome\_settings\_overrides**](https://developer.chrome.com/extensions/settings_override)": {...},
 "[**chrome\_ui\_overrides**](https://developer.chrome.com/extensions/ui_override)": {
 "bookmarks\_ui": {
 "remove\_bookmark\_shortcut": true,
 "remove\_button": true
 }
 },
 "[**chrome\_url\_overrides**](https://developer.chrome.com/extensions/override)": {...},
 "[**commands**](https://developer.chrome.com/extensions/commands)": {...},
 "content\_capabilities": ...,
 "[**content\_scripts**](https://developer.chrome.com/extensions/content_scripts)": [{...}],
 "[**content\_security\_policy**](https://developer.chrome.com/extensions/contentSecurityPolicy)": "policyString",
 "converted\_from\_user\_script": ...,
 "current\_locale": ...,
 "declarative\_net\_request": ...,
 "[**devtools\_page**](https://developer.chrome.com/extensions/devtools)": "devtools.html",
 "[**event\_rules**](https://developer.chrome.com/extensions/manifest/event_rules)": [{...}],
 "[**externally\_connectable**](https://developer.chrome.com/extensions/manifest/externally_connectable)": {
 "matches": ["\*://\*.example.com/\*"]
 },
 "[**file\_browser\_handlers**](https://developer.chrome.com/extensions/fileBrowserHandler)": [...],
 "[**file\_system\_provider\_capabilities**](https://developer.chrome.com/extensions/manifest/file_system_provider)": {
 "configurable": true,
 "multiple\_mounts": true,
 "source": "network"
 },
 "[**homepage\_url**](https://developer.chrome.com/extensions/manifest/homepage_url)": "http://path/to/homepage",
 "[**import**](https://developer.chrome.com/extensions/shared_modules)": [{"id": "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"}],
 "[**incognito**](https://developer.chrome.com/extensions/manifest/incognito)": "spanning, split, or not\_allowed",
 "input\_components": ...,
 "[**key**](https://developer.chrome.com/extensions/manifest/key)": "publicKey",
 "[**minimum\_chrome\_version**](https://developer.chrome.com/extensions/manifest/minimum_chrome_version)": "versionString",
 "[**nacl\_modules**](https://developer.chrome.com/extensions/manifest/nacl_modules)": [...],
 "oauth2": ...,
 "[**offline\_enabled**](https://developer.chrome.com/extensions/manifest/offline_enabled)": true,
 "[**omnibox**](https://developer.chrome.com/extensions/omnibox)": {
 "keyword": "aString"
 },
 "[**optional\_permissions**](https://developer.chrome.com/extensions/permissions)": ["tabs"],
 "[**options\_page**](https://developer.chrome.com/extensions/options)": "options.html",
 "[**options\_ui**](https://developer.chrome.com/extensions/optionsV2)": {
 "chrome\_style": true,
 "page": "options.html"
 },
 "[**permissions**](https://developer.chrome.com/extensions/declare_permissions)": ["tabs"],
 "platforms": ...,
 "replacement\_web\_app": ...,
 "[**requirements**](https://developer.chrome.com/extensions/manifest/requirements)": {...},
 "[**sandbox**](https://developer.chrome.com/extensions/manifest/sandbox)": [...],
 "[**short\_name**](https://developer.chrome.com/extensions/manifest/name#short_name)": "Short Name",
 "signature": ...,
 "spellcheck": ...,
 "[**storage**](https://developer.chrome.com/extensions/manifest/storage)": {
 "managed\_schema": "schema.json"
 },
 "system\_indicator": ...,
 "[**tts\_engine**](https://developer.chrome.com/extensions/ttsEngine)": {...},
 "[**update\_url**](https://developer.chrome.com/extensions/autoupdate)": "http://path/to/updateInfo.xml",
 "[**version\_name**](https://developer.chrome.com/extensions/manifest/version)": "aString",
 "[**web\_accessible\_resources**](https://developer.chrome.com/extensions/manifest/web_accessible_resources)": [...]
} 
```

至少我们知道，没有比这更好的了。现在在进行扩展时，我们不需要使用上面写的每一段代码。

#### 现在我们要制作 Github Readme Markdown Chrome 扩展，我在开始时给出了它的链接。

(去看看吧。)

第一步:创建一个 ***manifest.json*** 文件

```
{

“manifest\_version”: 2,

“author”: “https://github.com/Anant016",

“name”: “Github Readme Markdown”,

“icons”: { “48”: “./demo.png”, “128”: “./demo.png” },

“version”: “1.0”,

“description”: “Show options, how to edit while Readme.md is being edited in browser”,

“content\_scripts”: [

{

“matches”: [

“https://github.com/\*/new\*?readme\*"],

“js”: [“showoption.js”],

“run\_at”: “document\_end”

}

]

} 
```

在这里，我们可以指定 ***作者*** 就是你， ***扩展名*** ，就是 ***图标*** ，即它将如何显示。将应用发布到商店时，将显示的基本 ***描述*** 。

在 ***匹配*** 属性中，我们用一些正则表达式指定 URL，当这个 URL 匹配时，它将运行 ***showoption.js*** 文件

> 步骤 2:创建 **showoption_。js 文件 _**

```
buttton = document.createElement(“button”);

buttton.innerHTML = “See Markup”;

buttton.setAttribute(“type”, “button”);

buttton.setAttribute(

“class”,

“flex-item-equal nav-link flex-md-auto preview tabnav-tab js-blob-edit-preview js-blob-edit-tab”

);

buttton.setAttribute(“data-togggle”, “popover”);

buttton.setAttribute(“title”, “Headings\n# The largest heading\n## The second largest heading\n######“ );

var a = document.querySelector(“.tabnav”);

if (a == undefined || null) {

} else {

a.appendChild(buttton);

} 
```

> 就是这样。你有你的分机。！！！！！！:)

### 测试—

1.  访问 
2.  点击 ***加载解压*** 按钮，选择这两个文件所在的文件夹。
3.  这就是你。看看有没有用。

### 出版—

1.  访问 [*Chrome 开发者仪表盘*](https://chrome.google.com/webstore/devconsole)
2.  你必须支付 5 美元，一次性费用，在谷歌网上商店上传多达 20 个包。
3.  付费后，您可以按照简单的程序发布您的扩展，并为您的扩展上传一个. zip 文件。

> 如果你喜欢我的内容，请给它一些掌声。我会很感激的。