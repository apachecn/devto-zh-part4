# 在 Rails 中缓存大型下拉菜单

> 原文：<https://dev.to/epigene/cache-large-drop-downs-in-rails-46dl>

今天我注意到一个关键表单中的几个下拉列表需要几秒钟才能加载。

起初，我认为这是一个缓慢的查询，因为下拉菜单从数据库加载数据，有几十条记录。但是，不，查询只需要几毫秒。它的渲染是缓慢的。

为下拉菜单实现视图缓存很简单，只需提供一个 expirable 键。

可以使缓存过期的两个核心元素是 dropdown 元素的更改和所选值的更改。通常对于视图缓存，谨慎的做法是将关键字的范围扩大到资源 id 或其他唯一的数据。

在我的例子中，dropdown 保存了数据库中所有的`Thing`。检查更改的一个简便方法是查找最近的`updated_at`,如果有更改，则终止。这种方法适用于新记录和更新，
，但是如果记录被删除，密钥不会过期。不过，通常这不是一个大问题，尤其是如果记录很少被管理的话。然而，如果删除一条记录使缓存过期是很重要的，
增加记录的计数可能是必要的(并且在计算上是昂贵的！)

我发现根本不需要对资源 id 进行范围界定，
,因为我只关心选择的值。
所以我有了一个想法，全局缓存下拉菜单并用`gsub`做一些后处理，而不是计算键和存储单独的缓存条目。

后处理是简单的，但细节特定。在这种情况下，如果我全局缓存下拉 HTML，我需要确保从任何选项(如果有的话)中删除“selected”属性。)将它保存在缓存的 HTML 中，并将其添加到适当的选项中。

下面是我的最终解决方案:

```
:ruby
  key = cache_key(
    locale: I18n.locale,
    in: "app/views/users/_edit.field.thing_id.html.haml",
    for: "thing_dropdown",
    # lookin up the most recent update time, will expire on another update
    most_recent_thing_change: Thing.pluck("MAX(#{ Thing.table_name }.updated_at) AS stamp").last.to_s
  )  

  thing_dropdown =
    Rails.cache.fetch(key, expires_in: 24.hours) do
      select_options = options_from_collection_for_select(
        Thing.all.order(:name), "id", "to_text", user.thing
      )

      render(
        "edit.field_type_item",        
        select_options: select_options,
        include_blank: true,
        input_attributes: {multiple: false}
      )
    end

  # clear whatever is selected in the global cache
  thing_dropdown.gsub!(%r'\s*selected=\S+\s*', " ")
  # make the correct option selected
  thing_dropdown.gsub!(%r'value="#{ user.thing }"', "value=\"#{ user.thing }\" selected=\"selected\"")

= thing_dropdown.html_safe 
```

Enter fullscreen mode Exit fullscreen mode