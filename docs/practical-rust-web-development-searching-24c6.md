# 实用锈网开发-搜索

> 原文：<https://dev.to/werner/practical-rust-web-development-searching-24c6>

我们的应用程序应该列出待售产品，并像 Amazon 或 Ebay 一样工作，为了做到这一点，我们需要实现一些功能，如搜索，但是，在这种情况下，我们需要一种方法来搜索长文本，如产品描述和名称。

我们可以通过 PostgreSQL 提供的一个功能来实现它，称为全文搜索，有几个产品可用，如弹性搜索，但是我们可以使用 PostgreSQL 提供的一个，这样我们就可以维护一个资源。

## 迁徙

我们首先需要一些东西，我们需要一个描述字段，这是之前的实现中所没有的，所以，让我们为它创建一个迁移。

```
diesel migration generate add_description_to_products 
```

`migrations/2019-06-25-102903_add_description_to_products/up.sql` :

```
ALTER TABLE products ADD COLUMN description VARCHAR; 
```

`migrations/2019-06-25-102903_add_description_to_products/down.sql` :

```
ALTER TABLE products DROP COLUMN description; 
```

然后，我们需要为全文搜索配置进行迁移，我们需要一个 tsvector 类型的新列，这个新列将包含搜索将执行的文档，我们需要一个索引和一个触发器，每次在数据库中创建新记录时，它都会更新我们的新列。

```
diesel migration generate add_tsvector_column_to_products 
```

`migrations/2019-06-25-112356_add_tsvector_column_to_products/up.sql` :

```
ALTER TABLE products ADD COLUMN text_searchable_product_col tsvector NOT NULL;

UPDATE products SET text_searchable_product_col = 
    to_tsvector('english', name || ' ' || coalesce(description, ''));

CREATE INDEX textsearch_idx ON products USING GIN (text_searchable_product_col);

CREATE TRIGGER tsvectorupdateproducts BEFORE INSERT OR UPDATE
ON products FOR EACH ROW EXECUTE PROCEDURE
tsvector_update_trigger(text_searchable_product_col, 
'pg_catalog.english', 
name, 
description); 
```

`migrations/2019-06-25-112356_add_tsvector_column_to_products/down.sql` :

```
ALTER TABLE products DROP COLUMN text_searchable_product_col;

DROP TRIGGER tsvectorupdateproducts ON products; 
```

## 搜索实现

有一个板条箱将帮助我们进行全文搜索所需的柴油类型，它被称为`diesel_full_text_search`，让我们将它添加到`Cargo.toml`。

`Cargo.toml` :

```
diesel_full_text_search = "1.0.1" 
```

`src/lib.rs` :

```
extern crate diesel_full_text_search; 
```

我们需要手动修改我们的模式来添加全文搜索列，但是我们将在我们的模型中忽略它，因为更新将由我们的数据库触发器来完成。

`src/schema.rs` :

```
 table! {
    use diesel_full_text_search::TsVector;
    use diesel::sql_types::Int4;
    use diesel::sql_types::VarChar;
    use diesel::sql_types::Float8;
    use diesel::sql_types::Nullable;
     products (id) {
        id -> Int4,
        name -> VarChar,
        stock -> Float8,
        price -> Nullable<Int4>,
        description -> Nullable<VarChar>,
        text_searchable_product_col -> TsVector,
     }
 } 
```

在模型中，我们需要告诉 diesel 我们只需要表中的几个字段，这就是为什么我们需要一个常量来告诉 Rust 带来所有列，除了 tsvector，然后我们需要对代码进行新的修改以插入所需的过滤器。

`src/models/product.rs` :

```
pub struct Product {
    pub id: i32,
    pub name: String,
    pub stock: f64,
    pub price: Option<i32>,
    pub description: Option<String>
}

type ProductColumns = (
    products::id,
    products::name,
    products::stock,
    products::price,
    products::description
);

const PRODUCT_COLUMNS: ProductColumns = (
    products::id,
    products::name,
    products::stock,
    products::price,
    products::description
);

#[derive(Insertable, Deserialize, Serialize, AsChangeset, Debug, Clone, PartialEq)]
#[table_name="products"]
pub struct NewProduct {
    pub name: Option<String>,
    pub stock: Option<f64>,
    pub price: Option<i32>,
    pub description: Option<String>
}

impl ProductList {
    pub fn list(connection: &PgConnection, search: &str) -> Self {
        use diesel::RunQueryDsl;
        use diesel::QueryDsl;
        use diesel::pg::Pg;
        use crate::schema::products::dsl::*;
        use crate::schema;
        use diesel_full_text_search::{plainto_tsquery, TsVectorExtensions};

        let mut query = schema::products::table.into_boxed::<Pg>();

        if !search.is_empty() {
            query = query
                .filter(text_searchable_product_col.matches(plainto_tsquery(search)));
        } 
        let result = query
            .select(PRODUCT_COLUMNS)
            .limit(10)
            .load::<Product>(connection)
            .expect("Error loading products");

        ProductList(result)
    }
}

impl NewProduct {
    pub fn create(&self, connection: &PgConnection) -> Result<Product, diesel::result::Error> {
        use diesel::RunQueryDsl;

        diesel::insert_into(products::table)
            .values(self)
            .on_conflict_do_nothing()
            .returning(PRODUCT_COLUMNS)
            .get_result::<Product>(connection)
    }
}

impl Product {
    pub fn find(id: &i32, connection: &PgConnection) -> Result<Product, diesel::result::Error> {
        use diesel::QueryDsl;
        use diesel::RunQueryDsl;

        products::table.find(id).select(PRODUCT_COLUMNS).first(connection)
    }

...

impl PartialEq<Product> for NewProduct {
    fn eq(&self, other: &Product) -> bool {
        let new_product = self.clone();
        let product = other.clone();
        new_product.name == Some(product.name) &&
        new_product.stock == Some(product.stock) &&
        new_product.price == product.price &&
        new_product.description == product.description
    }
} 
```

正如你在前面的代码中看到的，我们在`select`方法中告诉 diesel 需要返回哪些列。

最后，我们需要修改产品索引处理程序来引入我们的搜索。

```
#[derive(Deserialize)]
pub struct ProductSearch{ 
    pub search: String
}

pub fn index(_user: LoggedUser, pool: web::Data<PgPool>, product_search: web::Query<ProductSearch>) -> Result<HttpResponse> {
     let pg_pool = pg_pool_handler(pool)?;
    let search = &product_search.search;
    Ok(HttpResponse::Ok().json(ProductList::list(&pg_pool, search)))
} 
```

为了测试它，你需要在查询字符串中传递搜索，即使你不想搜索，就像这样:`http://localhost:8088/products?search=`

这里可以看到源代码[。](https://github.com/practical-rust-web-development/mystore/tree/v4.0)