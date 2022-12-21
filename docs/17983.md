# TinyMCE Templates Package In Episerver

> 原文：[https://dev.to/debpu06/tinymce-templates-package-in-episerver-1ikc](https://dev.to/debpu06/tinymce-templates-package-in-episerver-1ikc)

Recently I posted about how to [Create Episerver TinyMCE Default Templates](https://davidboland.site/blog/create-episerver-tinymce-default-templates). Since then, here at Nansen we have worked on several projects that have benefited from them. I decided to pull some of the generic ones into a NuGet package and share it with the community through [Episerver's NuGet Feed](https://nuget.episerver.com/package/?id=Nansen.Components.TinyMCETemplates).

Aside from what I have below, you can find information on the project, as well as how the templates render on the [project page](https://davidboland.site/projects/tinymce-templates-package).

## Whats Included

The package itself is straight forward. It contains an initialization module for registering the templates. It adds it to the TinyMCE default configuration. It also includes the templates plugin, and the templates icon in the toolbar.

The templates get added to your modules/_protected folder. There will be 9 unique templates, each of which is implemented with both bootstrap classes and custom ones. All the images for the templates are embedded in the HTML using base64.

## Styles

The templates are all rendered using [bootstrap](https://getbootstrap.com/) CSS classes by default and were designed to be responsive.

A set of templates were added that use the same bootstrap classes that are all prefixed with "ntemplate-". This was done to make it easier to add your own custom styling. You can use these templates by adding the following appSetting:

```
<add key="nansen:tinymce:customcss" value="true"></add> 
```

*Note: Updating this appSetting once content is already published with existing templates will not modify them. Once content is published, it saves the content that it got from the template as normal text, and will not look to the template for updates.*

## Final Thoughts

My goal is to have this be a constantly growing repository. The more options we give our editors, the more time and effort they can save when populating content. If you are interested in contributing, feel free to visit our [Github repository](https://github.com/nansen/Nansen.Components.TinyMCETemplates) and create a pull request.

As always, if you have any questions or thoughts on improvements, feel free to leave a comment below.

Thanks!