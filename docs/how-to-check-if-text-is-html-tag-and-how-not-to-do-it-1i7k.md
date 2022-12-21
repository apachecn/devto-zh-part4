# 如何检查文本是否是 Html 标签(以及如何不这样做)

> 原文：<https://dev.to/the_real_syler/how-to-check-if-text-is-html-tag-and-how-not-to-do-it-1i7k>

我写这篇文章的原因是，我有一个函数可以检查一个给定的文本是否是一个 html 标签，它只是稍微慢了一点点。

怎么不做(慢了 99.92%，9，624 ops/s 0.63%)

```
const htmlTags = [
  'a',
  'abbr',
  'address',
  'area',
  'article',
  'aside',
  'audio',
  'b',
  'base',
  'bdi',
  'bdo',
  'blockquote',
  'body',
  'br',
  'button',
  'canvas',
  'caption',
  'cite',
  'code',
  'col',
  'colgroup',
  'data',
  'datalist',
  'dd',
  'del',
  'details',
  'dfn',
  'dialog',
  'div',
  'dl',
  'dt',
  'em',
  'embed',
  'fieldset',
  'figure',
  'footer',
  'form',
  'h1',
  'h2',
  'h3',
  'h4',
  'h5',
  'h6',
  'head',
  'header',
  'hgroup',
  'hr',
  'html',
  'i',
  'iframe',
  'img',
  'input',
  'ins',
  'kbd',
  'keygen',
  'label',
  'legend',
  'li',
  'link',
  'main',
  'map',
  'mark',
  'menu',
  'menuitem',
  'meta',
  'meter',
  'nav',
  'noscript',
  'object',
  'ol',
  'optgroup',
  'option',
  'output',
  'p',
  'param',
  'pre',
  'progress',
  'q',
  'rb',
  'rp',
  'rt',
  'rtc',
  'ruby',
  's',
  'samp',
  'script',
  'section',
  'select',
  'small',
  'source',
  'span',
  'strong',
  'style',
  'sub',
  'summary',
  'sup',
  'svg',
  'table',
  'tbody',
  'td',
  'template',
  'textarea',
  'tfoot',
  'th',
  'thead',
  'time',
  'title',
  'tr',
  'track',
  'u',
  'ul',
  'var',
  'video',
  'wbr'
];

function isHtmlTag(text) {
  let isTag = false;
  for (let i = 0; i < htmlTags.length; i++) {
    const tag = htmlTags[i];
    if (new RegExp(`^ *${tag}$|^ *${tag}:?:?\\.?,?[$#{}()\\w\\-\\[\\]='",\\.# ]*$`).test(text)) {
      isTag = true;
      break;
    }
  }
  return isTag;
} 
```

怎么做(11，594，401 ops/s 0.64%)

```
function isHtmlTag(text) {
  let isTag = false;
  if (/^ *(a|abbr|address|area|article|aside|audio|b|base|bdi|bdo|blockquote|body|br|button|canvas|caption|cite|code|col|colgroup|data|datalist|dd|del|details|dfn|dialog|div|dl|dt|em|embed|fieldset|figure|footer|form|h1|h2|h3|h4|h5|h6|head|header|hgroup|hr|html|i|iframe|img|input|ins|kbd|keygen|label|legend|li|link|main|map|mark|menu|menuitem|meta|meter|nav|noscript|object|ol|optgroup|option|output|p|param|pre|progress|q|rb|rp|rt|rtc|ruby|s|samp|script|section|select|small|source|span|strong|style|sub|summary|sup|svg|table|tbody|td|template|textarea|tfoot|th|thead|time|title|tr|track|u|ul|var|video|wbr)(:|::|,|\.|#)[:$#{}()\w\-\[\]='",\.# ]*$/.test(text)) {
      isTag = true;
  }
  return isTag;
} 
```

用 [jsbench.me](https://jsbench.me/) 测试

如果有更快的方法，请告诉我。