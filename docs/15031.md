# 实用 Rust Web 开发-分页

> 原文：<https://dev.to/werner/practical-rust-web-development-pagination-3nb>

正如你可能知道的，PostgreSQL 中提供的默认分页机制不是很有效，实际上当你需要深入到[分页](https://www.postgresql.org/docs/10/queries-limit.html)时，它们会警告你不要使用`OFFSET`。

那么，除了`OFFSET`我们还有什么选择呢？一种选择可能是通过设计，假设我们将构建一个具有无限滚动的现代应用程序，其工作方式类似于 Instagram，这意味着一个简单的解决方案。

我们可以只返回一组记录，比如说 10 条，并按任何字段对它们进行排序，在这种情况下，我们可以创建一个 rank 字段，其中包含一些数字，这些数字根据一些因素对搜索进行优先排序，比如 premium account 或我们可以决定的任何其他因素，总之我们将忽略 rank 字段的算法，并假设我们已经有了它。

我们需要另一个排名，这是一个 PostgreSQL 函数，名为 [ts_rank](https://www.postgresql.org/docs/10/textsearch-controls.html) ，我们使用全文搜索，我们需要根据匹配词的频率对我们的行进行排序。

然而，我们有一个问题，由于之前的要求(全文搜索排名)，我们的查询可能会[慢](https://www.postgresql.org/docs/10/textsearch-controls.html)，所以我们需要找到一个解决方案。

## 朗姆酒指数

我们有一些可能会有帮助的东西，有一个外部 PostgreSQL 模块，它创建了一个自定义索引，允许我们使用全文搜索和更快的结果排名，它被称为 [Rum index](https://github.com/postgrespro/rum) ，问题是我们会有缓慢的插入，但这没关系，因为我们需要快速读取。

我们需要做的第一件事是安装扩展并加载到 PostgreSQL 中，在 ubuntu 中我们可以这样做:

```
sudo apt install postgresql-10-rum
sudo su - postgres
psql -d mystore
CREATE EXTENSION rum; 
```

然后我们需要编辑最后一次迁移，将`GIN`改为`RUM`，并添加函数`rum_tsvector_ops`:

`migrations/2019-06-25-112356_add_tsvector_column_to_products/up.sql` :

```
ALTER TABLE products ADD COLUMN text_searchable_product_col tsvector NOT NULL;

UPDATE products SET text_searchable_product_col = 
    to_tsvector('english', name || ' ' || coalesce(description, ''));

CREATE INDEX textsearch_idx ON products USING RUM (text_searchable_product_col rum_tsvector_ops);

CREATE TRIGGER tsvectorupdateproducts BEFORE INSERT OR UPDATE
ON products FOR EACH ROW EXECUTE PROCEDURE
tsvector_update_trigger(text_searchable_product_col, 'pg_catalog.english', name, description); 
```

现在，我们可以重做我们的迁移:

```
diesel migration redo 
```

小心检查模式文件，我们添加了一个名为`TsVector`的新字段类型，每次执行迁移模式时，都需要从`diesel_full_text_search`箱中加载它。

## 朗姆酒指数支持

我们名为`diesel_full_text_search`的板条箱不支持 RUM 操作符:`<=>`，所以我们可以使用我的 fork:

`Cargo.toml` :

```
diesel_full_text_search = { version = "1.2.1", git = "https://github.com/werner/diesel_full_text_search" } 
```

我们说过，我们将使用产品等级，让我们添加它。

```
diesel migration generate add_rank_to_products
diesel migration generate add_rank_index 
```

`migrations/2019-07-01-114531_add_rank_to_products/up.sql` :

```
ALTER TABLE products ADD COLUMN product_rank NUMERIC DEFAULT 0.0; 
```

`migrations/2019-07-01-192138_add_rank_index/up.sql` :

```
CREATE INDEX products_rank_idx ON products (product_rank); 
```

现在我们可以修改模型了。

`src/models/product.rs` :

```
impl ProductList {
    pub fn list(connection: &PgConnection, search: &str, rank: f64) -> Self {
        use diesel::RunQueryDsl;
        use diesel::ExpressionMethods;
        use diesel::QueryDsl;
        use diesel::pg::Pg;
        use crate::schema::products::dsl::*;
        use crate::schema;
        use diesel_full_text_search::{plainto_tsquery, TsRumExtensions, TsVectorExtensions};

        let mut query = schema::products::table.into_boxed::<Pg>();

        if !search.is_empty() {
            query = query
                .filter(text_searchable_product_col.matches(plainto_tsquery(search)))
                .order((product_rank.desc(), 
                        text_searchable_product_col.distance(plainto_tsquery(search))));
        } else {
            query = query.order(product_rank.desc());
        }
        let result = query
            .select(PRODUCT_COLUMNS)
            .filter(product_rank.le(rank))
            .limit(10)
            .load::<Product>(connection)
            .expect("Error loading products");

        ProductList(result)
    }
} 
```

我们添加了相应的`order by`来处理 RUM 功能，如果没有提供搜索，我们就按照产品排名进行常规排序。

当我们需要对结果进行排序时，我们使用一个过滤器来执行一个`where`子句，然后从前端我们将接收相应的等级来返回产品。

最后，我们编辑处理程序:

`src/handlers/products.rs` :

```
#[derive(Deserialize)]
pub struct ProductPagination {
    pub rank: f64
}

pub fn index(_user: LoggedUser,
             pool: web::Data<PgPool>,
             product_search: web::Query<ProductSearch>,
             pagination: web::Query<ProductPagination>) -> Result<HttpResponse> {
    let pg_pool = pg_pool_handler(pool)?;
    let search = &product_search.search;
    Ok(HttpResponse::Ok().json(ProductList::list(&pg_pool, search, pagination.rank)))
} 
```

查看源代码[这里](https://github.com/practical-rust-web-development/mystore/tree/v5.0)