# 实用 Rust Web 开发-宏

> 原文：<https://dev.to/werner/practical-rust-web-development-macros-565o>

有人可能会注意到，与其他编程语言相比，我们需要编写多少样板文件，特别是对于 web 来说，然而，我们需要编写一些代码，比如类型，我们许多人都认为这对改进开发过程是必要的。

我们可以减少其他领域的代码，我们有宏可以做到这一点，但是我们需要小心，因为宏会使我们的代码难以维护。所以，尽量少用宏。

让我们创建一个宏来减少我们为处理程序编写的代码量。乍一看，这似乎让人不知所措，但是，一旦你理解了它们，它们就会成为一个强大的工具。

`src/handlers/function_handler.rs` :

```
 macro_rules! function_handler {
    ( $handler_name:ident ($($arg:ident:$typ:ty),*) -> $body:expr) => {
        pub fn $handler_name(user: LoggedUser, pool: web::Data<PgPool>, $($arg:$typ,)*) 
            -> impl Future<Item = HttpResponse, Error = actix_web::Error>
        {
            web::block(move || {
                let pg_pool = pool
                    .get()
                    .map_err(|_| {
                        crate::errors::MyStoreError::PGConnectionError
                    })?;
                $body(user, pg_pool)
            })
            .then(|res| match res {
                Ok(data) => Ok(HttpResponse::Ok().json(data)),
                Err(_) => Ok(HttpResponse::InternalServerError().into()),
            })
        }
    };
} 
```

我们用`macro_rules!`声明一个宏，你甚至可以用一个宏创建新的语法，在我们的例子中，我们想要创建遵循下一个语法的处理程序:`handler name (params) -> block of code`，它可以这样表达:`$handler_name:ident ($($arg:ident:$typ:ty),*) -> $body:expr`，`$handler_name`是一个标识符，我们处理程序的名字，然后我们传递参数，因为我们不知道它们有多少，我们用逗号分隔它们并使用这个语法:`$(),*`，这是为了告诉 Rust 我们有无限数量的参数。现在我们需要一种方法来告诉 Rust 从代码块中分离参数，我用一个箭头来实现。

我们将闭包标识为`$body`，然后传递参数，这样我们就可以在处理程序中使用它，特别是登录用户和数据库连接。

现在比较一下这个:

```
pub fn index(user: LoggedUser, pool: web::Data<PgPool>) 
    -> impl Future<Item = HttpResponse, Error = actix_web::Error> {
        web::block(move || {
            let pg_pool = pool
                .get()
                .map_err(|_| {
                    crate::errors::MyStoreError::PGConnectionError
                })?;
            PriceList::list(user.id, &pg_pool)
        })
        .then(|res| match res {
            Ok(data) => Ok(HttpResponse::Ok().json(data)),
            Err(error) => Err(actix_web::error::ErrorInternalServerError(error)),
        })
} 
```

有了这个:

```
function_handler!(
    index () -> (|user: LoggedUser, pg_pool: PgPooledConnection| {
        PriceList::list(user.id, &pg_pool)
    })
); 
```

很酷，对吧？

源代码[这里](https://github.com/practical-rust-web-development/mystore/tree/v7.0)。