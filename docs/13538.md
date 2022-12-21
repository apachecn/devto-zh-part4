# 实用锈网开发-协会

> 原文：<https://dev.to/werner/practical-rust-web-development-associations-45h1>

作为 web 开发人员，我们已经习惯了大多数框架提供的一些工具，然而，当我们选择从更高性能的角度来看我们的应用程序时，我们必须走出我们的舒适区。

其中一个功能是模型之间的关联，例如，在 Ruby on Rails 中，我们有`belongs_to`、`has_many`和`accepts_nested_attributes_for` ActiveRecord 方法，这些方法为我们提供了多种资源，让我们的生活变得更加轻松。

Diesel 提供了一个 [`BelongsTo`](https://docs.diesel.rs/diesel/associations/index.html) 宏，我们可以使用它，但是我们需要更多的努力来使它工作。

假设我们需要通过用户来关联我们的产品，因为一个用户不应该访问另一个用户的产品。我们可能需要的其他关系是价格，我们希望我们的产品有不同的价格(折扣、最大值、最小值等)。

## 数据库迁移

我们将创建几个迁移，一个用于向我们的 products 表添加一个`user_id`列，以了解哪个用户拥有每个产品，然后我们需要另外两个表，prices 将保存名称，另一个将产品和价格与相应的金额相关联。

`migrations/2019-07-06-151805_add_user_to_products/up.sql` :

```
ALTER TABLE products ADD COLUMN user_id INTEGER NOT NULL;
ALTER TABLE products ADD CONSTRAINT products_user_id_foreign_key 
FOREIGN KEY (user_id) REFERENCES users (id) ON DELETE CASCADE; 
```

`migrations/2019-07-07-134611_add_prices/up.sql` :

```
CREATE TABLE prices (
  id SERIAL PRIMARY KEY,
  name VARCHAR NOT NULL,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  CHECK (name <> '')
);

CREATE TABLE prices_products (
  id SERIAL PRIMARY KEY,
  price_id INTEGER NOT NULL REFERENCES prices(id),
  product_id INTEGER NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  amount INTEGER, --representing cents
  UNIQUE (price_id, product_id)
);

ALTER TABLE products RENAME COLUMN price TO cost; 
```

## 联想

我将省略几个部分，因为它们与 product 非常相似，如 prices handler 和一些结构，但是我将突出 PriceProduct 结构。

`src/models/price.rs` :

```
#[derive(Identifiable, Associations, Queryable, Serialize, Deserialize, Debug, Clone, PartialEq)]
#[belongs_to(Price)]
#[belongs_to(Product)]
#[table_name="prices_products"]
pub struct PriceProduct {
    pub id: i32,
    pub price_id: i32,
    pub product_id: i32,
    pub user_id: i32,
    pub amount: Option<i32>
} 
```

正如你所看到的非常简单，你只需要使用`belongs_to`宏来关联你想要的模型结构。

现在，将我们的`PriceProduct`保存到数据库的方法:

```
#[derive(Serialize, Deserialize, Clone)]
pub struct PriceProductToUpdate {
    pub price_product: NewPriceProduct,
    pub to_delete: bool
}

use diesel::PgConnection;

impl PriceProductToUpdate {
    pub fn batch_update(records: Vec<Self>, param_product_id: i32, param_user_id: i32, connection: &PgConnection) ->
        Result<Vec<PriceProduct>, diesel::result::Error> {
            use diesel::QueryDsl;
            use diesel::RunQueryDsl;
            use diesel::ExpressionMethods;
            use diesel::Connection;
            use itertools::Itertools;

            connection.transaction(|| {
                let mut records_to_keep = vec![];
                for price_product_to_update in records {

                    if price_product_to_update.to_delete &&
                        price_product_to_update.price_product.id.is_some() {

                        diesel::delete(
                                prices_products::table
                                    .filter(prices_products::user_id.eq(param_user_id))
                                    .find(price_product_to_update.price_product.id.unwrap()))
                            .execute(connection)?;
                    } else {
                        records_to_keep.push(price_product_to_update)
                    }
                }

                records_to_keep
                    .iter()
                    .map(|price_product| {

                        let new_price_product = NewPriceProduct {
                            user_id: Some(param_user_id),
                            product_id: Some(param_product_id),
                            ..price_product.clone().price_product
                        };

                        diesel::insert_into(prices_products::table)
                            .values(&new_price_product)
                            .on_conflict((prices_products::price_id, 
                                        prices_products::product_id))
                            .do_update()
                            .set(prices_products::amount.eq(new_price_product.amount))
                            .returning((prices_products::id, 
                                        prices_products::price_id,
                                        prices_products::product_id,
                                        prices_products::user_id,
                                        prices_products::amount))
                            .get_result::<PriceProduct>(connection)
                    })
                    .fold_results(vec![], |mut accum, value| {
                        accum.push(value);
                        accum
                    })
            })

        }
} 
```

我正在使用一个事务，所以，如果某个东西失败了，就会发生回滚。当我们需要找到一种产品来给我们带来相关价格时，我们使用`belonging_to`方法。

`src/models/product.rs` :

```
 pub fn find(product_id: &i32, param_user_id: i32, connection: &PgConnection) -> 
        Result<(Product, Vec<PriceProduct>), diesel::result::Error> {
            use diesel::QueryDsl;
            use diesel::RunQueryDsl;
            use diesel::ExpressionMethods;
            use crate::schema;
            use crate::schema::products::dsl::*;

            let product: Product =
                schema::products::table
                    .select(PRODUCT_COLUMNS)
                    .filter(user_id.eq(param_user_id))
                    .find(product_id)
                    .first(connection)?;

            let products_with_prices =
                PriceProduct::belonging_to(&product)
                    .load::<PriceProduct>(connection)?;

            Ok((product, products_with_prices))
    } 
```

你可以在这里看一下源代码