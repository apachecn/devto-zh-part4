# 您可以编写更好的 jQuery

> 原文：<https://dev.to/graffino/you-can-write-better-jquery-38f3>

虽然几乎每篇新文章都提到它，谈论它的消亡，但我决定写一篇关于我遇到的最常见的 jQuery 坏习惯之一以及如何处理它的文章。这是:

#### 由此出发:

```
 $('.multiSelectOne').multiselect({
    buttonClass: 'btn btn-default',
    buttonWidth: '100%',
    ...
});
$('.multiSelectTwo').multiselect({
    buttonClass: 'btn btn-default',
    buttonWidth: '100%',
    nonSelectedText: '---',
    ...
}); 
```

#### 到此:

```
<select class="js-multiselect" 
    data-include-select-all-option="true" 
    data-all-selected-text="All">
    <option></option>
</select>

$('.js-multi-select).each(function () {
    let options = {};

    Object.entries($(this).data())
        .map(property => options[property[0]] = property[1]);

  $(this).multiselect(options);

} 
```

**并且永远不再返回脚本文件进行调整。**