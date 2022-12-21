# Enumize-extend ActiveRecord::Enum to add utility method

> 原文：<https://dev.to/huacnlee/enumize-activerecord-enum-1mf7>

Rails 4.2 (if I remember correctly) has brought us the official Enum solution, and now everyone is using it.

For example:

```
class Book
  enum status: %i[draft published archived]
end 
```

Enter fullscreen mode Exit fullscreen mode

However, when we use it, we often find that we need to display the `status` field in Chinese or other ways on the interface instead of a `draft / published / archived`.

So many people may be like this:

```
class Book
  enum status: %i[draft published archived]

  def status_name
    case status
    when "draft" then "草稿"
    when "published" then "已发布"
    when "archived" then "归档"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

Then as the project progresses, you will find that there are a lot of such functions in your Model.

To solve this problem, we initially implemented an extension in our own project to replace the definition of `enum` and add some function extensions to it. Now it is extracted as a Gem, which can be used conveniently in the future.

* * *

## Usage

```
gem "enumize" 
```

Enter fullscreen mode Exit fullscreen mode

Then your original enum definition doesn't need to be changed. This Gem has already covered that method.

> [![🚨](img/f8e8ea02c53b306cac576d17ea6129fe.png ":rotating\_light:") ](https://res.cloudinary.com/practicaldev/image/fetch/s--J3U04LJ6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://twemoji.ruby-china.com/2/svg/1f6a8.svg) So, you can think that the introduction of this Gem will not change the original usage of ActiveRecord::Enum, and you can seamlessly integrate it into your project!

```
class Book
  enum status: %i[draft published archived]
end 
```

Enter fullscreen mode Exit fullscreen mode

Now you have `status_name`, `status_color`, `status_value` and `Book.status_options` these methods:

*   `#{attribute}_name`-returns I18n information of name for display.
*   `#{attribute}_color`-return the information of reading color from I18n (you can ignore it if you don't need it in your project)
*   `#{attribute}_value`-returns the original database value to replace `Book.statuses[@book.status]`
*   `Book.#{attribute}_options`-Returns an Array for `select` tag, such as `<%= f.select :status, Book.status_options %>`.

Configure I18n:

config/local/en . yml

```
en:
  activerecord:
    enums:
      book:
        status:
          draft: Drafting
          published: Published
          archived: Archived
        status_color:
          draft: "#999999"
          published: "green"
          archived: "red" 
```

Enter fullscreen mode Exit fullscreen mode

config/locales/zh-cn . yml

```
zh-CN:
  activerecord:
    enums:
      book:
        status:
          draft: "草稿"
          published: "已发布"
          archived: "归档" 
```

Enter fullscreen mode Exit fullscreen mode

The following is a demonstration of the use:

```
irb> @book = Book.new(status: :draft)
irb> @book.status
"draft"
irb> @book.draft?
true
irb> @book.published?
false
irb> @book.status_value
0
irb> @book.status_name
"草稿"
irb> @book.status_color
"#999999" 
```

Enter fullscreen mode Exit fullscreen mode

Demonstration of `status_options` method used in View:

```
<% form_for(@book) do |f| %>
  <%= f.text_field :name %>
  <%= f.select :status, Book.status_options %>
  <%= f.submit %>
<% end > 
```

Enter fullscreen mode Exit fullscreen mode

## Project address

[https://github . com/huacnlee/枚举](https://github.com/huacnlee/enumize)