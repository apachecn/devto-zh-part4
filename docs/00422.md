# 向 Hugo 主题添加标签

> 原文：<https://dev.to/pianomanfrazier/add-tags-to-hugo-theme-1f3f>

要在 Hugo 主题中添加对标签的支持，您需要两件东西。一个模板呈现标签列表，另一个呈现该标签下的所有内容列表。在涉水通过雨果的文件几次后，我在这里记录的过程。

Hugo 的模板[查找顺序](https://gohugo.io/templates/lookup-order/)简直让人应接不暇。我认识到它的力量，但是给博客主题添加一个简单的功能可能太多了。

这里有一个添加标签支持的简单方法。你需要两个文件。

```
{{/* layouts/taxonomy/terms.html */}}
{{ partial "header.html" . }}
<section>
<h1>Tags</h1>
<ul>
{{ range .Data.Terms.Alphabetical }}
<li>
<h2>
<a href="{{ .Page.Permalink }}">({{ .Count }}) {{ .Page.Title }}</a>
</h2>
</li>
{{ end }}
</ul>
</section>
{{ partial "footer.html" . }}

{{/* layouts/taxonomy/list.html */}}
{{ partial "header.html" . }}
<section>
<h1>Tag: {{ .Title }}</h1>
<ul>
{{ range .Pages }}
<li>
<h2>
<a href="{{ .Permalink }}">{{ .Title }}</a>
</h2>
<time>{{ .Date.Format (.Site.Params.dateform | default "January 2006") }}</time>
</li>
{{ end }}
</ul>
</section>
{{ partial "footer.html" . }} 
```

Enter fullscreen mode Exit fullscreen mode