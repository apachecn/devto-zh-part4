# 实用 Rust Web 开发- GraphQL

> 原文：<https://dev.to/werner/practical-rust-web-development-graphql-cdg>

根据官方[主页](https://graphql.org/)的说法，GraphQL 是一种 API 的查询语言，也是一种用现有数据完成这些查询的运行时。GraphQL 为 API 中的数据提供了完整且易于理解的描述，使客户能够准确地要求他们需要的东西，使 API 更容易随时间发展，并支持强大的开发工具。

GraphQL 的一个优点是它提供的灵活性，在一个查询中，您可以获得您需要的一切，从而允许随着时间的推移轻松维护代码，并简化服务器和客户端之间的通信。

Juniper 是一个允许创建 GraphQL 服务器的机箱，我们将在我们的项目中使用它。

让我们继续我们的在线商店，我们将需要控制我们在网站上的销售，所以，让我们创建一个销售模块，将接收 GraphQL 查询。

`migrations/2019-07-28-191653_add_sales/up.sql` :

```
CREATE TABLE sales (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  sale_date DATE NOT NULL,
  total FLOAT NOT NULL
);

CREATE TABLE sale_products (
  id SERIAL PRIMARY KEY,
  product_id INTEGER NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  sale_id INTEGER NOT NULL REFERENCES sales(id) ON DELETE CASCADE,
  amount FLOAT NOT NULL,
  discount INTEGER NOT NULL,
  tax INTEGER NOT NULL,
  price INTEGER NOT NULL, --representing cents
  total FLOAT NOT NULL
) 
```

然后，我们需要接收所有查询的端点，这将是一个 post 请求。

`src/graphql.rs` :

```
 pub fn graphql(
    st: web::Data<Arc<Schema>>,
    data: web::Json<GraphQLRequest>,
    user: LoggedUser,
    pool: web::Data<PgPool>
) -> impl Future<Item = HttpResponse, Error = Error> {
    web::block(move || {
        let pg_pool = pool
            .get()
            .map_err(|e| {
                serde_json::Error::custom(e)
            })?;

        let ctx = create_context(user.id, pg_pool);

        let res = data.execute(&st, &ctx);
        Ok::<_, serde_json::error::Error>(serde_json::to_string(&res)?)
    })
    .map_err(Error::from)
    .and_then(|user| {
        Ok(HttpResponse::Ok()
            .content_type("application/json")
            .body(user))
    })
} 
```

`src/main.rs` :

```
 HttpServer::new(
    move || App::new()
        .service(
            web::resource("/graphql").route(web::post().to_async(graphql))
        ) 
```

为了输出读取数据的响应，我们需要一个查询，但是如果我们需要修改状态，我们将需要一个突变，让我们在销售模块中添加两个资源。

`src/models/sale.rs` :

```
use diesel::PgConnection;
use diesel::BelongingToDsl;
use diesel::sql_types;
use chrono::NaiveDate;
use juniper::{FieldResult};
use crate::schema;
use crate::schema::sales;
use crate::schema::sale_products;
use crate::db_connection::PgPooledConnection;
use crate::models::product::{ Product, PRODUCT_COLUMNS };
use crate::errors::MyStoreError;

#[derive(Identifiable, Queryable, Debug, Clone, PartialEq)]
#[table_name="sales"]
#[derive(juniper::GraphQLObject)]
#[graphql(description="Sale Bill")]
pub struct Sale {
    pub id: i32,
    pub user_id: i32,
    pub sale_date: NaiveDate,
    pub total: f64,
    pub bill_number: Option<String>
}

#[derive(Insertable, Deserialize, Serialize, AsChangeset, Debug, Clone, PartialEq)]
#[table_name="sales"]
#[derive(juniper::GraphQLInputObject)]
#[graphql(description="Sale Bill")]
pub struct NewSale {
    pub id: Option<i32>,
    pub sale_date: Option<NaiveDate>,
    pub user_id: Option<i32>,
    pub total: Option<f64>,
    pub bill_number: Option<String>
}

use crate::models::sale_product::{ SaleProduct, NewSaleProduct, NewSaleProducts, FullSaleProduct,FullNewSaleProduct };

#[derive(Debug, Clone)]
#[derive(juniper::GraphQLObject)]
pub struct FullSale {
    pub sale: Sale,
    pub sale_products: Vec<FullSaleProduct>
}

#[derive(Debug, Clone)]
#[derive(juniper::GraphQLObject)]
pub struct FullNewSale {
    pub sale: NewSale,
    pub sale_products: Vec<FullNewSaleProduct>
}

#[derive(Debug, Clone)]
#[derive(juniper::GraphQLObject)]
pub struct ListSale {
    pub data: Vec<FullSale>
}

use std::sync::Arc;

pub struct Context {
    pub user_id: i32,
    pub conn: Arc<PgPooledConnection>,
}

impl juniper::Context for Context {}

pub struct Query;

type BoxedQuery<'a> = 
    diesel::query_builder::BoxedSelectStatement<'a, (sql_types::Integer,
                                                     sql_types::Integer,
                                                     sql_types::Date,
                                                     sql_types::Float8,
                                                     sql_types::Nullable<sql_types::Text>
                                                     ),
                                                     schema::sales::table, diesel::pg::Pg>;

impl Sale {
    fn searching_records<'a>(search: Option<NewSale>) -> BoxedQuery<'a> {
        use diesel::QueryDsl;
        use diesel::ExpressionMethods;
        use crate::schema::sales::dsl::*;

        let mut query = schema::sales::table.into_boxed::<diesel::pg::Pg>();

        if let Some(sale) = search {
            if let Some(sale_sale_date) = sale.sale_date {
                query = query.filter(sale_date.eq(sale_sale_date));
            }
            if let Some(sale_bill_number) = sale.bill_number {
                query = query.filter(bill_number.eq(sale_bill_number));
            }
        }

        query
    }
}

#[juniper::object(
    Context = Context,
)]
impl Query {

    fn listSale(context: &Context, search: Option<NewSale>, limit: i32) 
        -> FieldResult<ListSale> {
            use diesel::{ QueryDsl, RunQueryDsl, ExpressionMethods, GroupedBy };
            use crate::models::sale_product::SaleProduct;
            let conn: &PgConnection = &context.conn;
            let query = Sale::searching_records(search);

            let query_sales: Vec<Sale> =
                query
                    .filter(sales::dsl::user_id.eq(context.user_id))
                    .limit(limit.into())
                    .load::<Sale>(conn)?;

            let query_products = 
                schema::products::table
                    .inner_join(schema::sale_products::table)
                    .select((PRODUCT_COLUMNS, 
                            (schema::sale_products::id, 
                             schema::sale_products::product_id, 
                             schema::sale_products::sale_id, 
                             schema::sale_products::amount,
                             schema::sale_products::discount,
                             schema::sale_products::tax,
                             schema::sale_products::price,
                             schema::sale_products::total)))
                    .load::<(Product, SaleProduct)>(conn)?;

            let query_sale_products = 
                SaleProduct::belonging_to(&query_sales)
                    .inner_join(schema::products::table)
                    .select(((schema::sale_products::id, 
                             schema::sale_products::product_id, 
                             schema::sale_products::sale_id, 
                             schema::sale_products::amount,
                             schema::sale_products::discount,
                             schema::sale_products::tax,
                             schema::sale_products::price,
                             schema::sale_products::total),
                             PRODUCT_COLUMNS))
                    .load::<(SaleProduct, Product)>(conn)?
                    .grouped_by(&query_sales);

            let tuple_full_sale: Vec<(Sale, Vec<(SaleProduct, Product)>)> = 
                query_sales
                    .into_iter()
                    .zip(query_sale_products)
                    .collect::<Vec<(Sale, Vec<(SaleProduct, Product)>)>>();

            let vec_full_sale = tuple_full_sale.iter().map (|tuple_sale| {
                let full_sale_product = tuple_sale.1.iter().map(|tuple_sale_product| {
                    FullSaleProduct {
                        sale_product: tuple_sale_product.0.clone(),
                        product: tuple_sale_product.1.clone()
                    }
                }).collect();
                FullSale {
                    sale: tuple_sale.0.clone(),
                    sale_products: full_sale_product
                }
            }).collect();

            Ok(ListSale { data: vec_full_sale })
        }

    fn sale(context: &Context, sale_id: i32) -> FieldResult<FullSale> {
        use diesel::{ ExpressionMethods, QueryDsl, RunQueryDsl };

        let conn: &PgConnection = &context.conn;
        let sale: Sale =
            schema::sales::table
                .filter(sales::dsl::user_id.eq(context.user_id))
                .find(sale_id)
                .first::<Sale>(conn)?;

        let sale_products = 
            SaleProduct::belonging_to(&sale)
                .inner_join(schema::products::table)
                .select(((schema::sale_products::id, 
                            schema::sale_products::product_id, 
                            schema::sale_products::sale_id, 
                            schema::sale_products::amount,
                            schema::sale_products::discount,
                            schema::sale_products::tax,
                            schema::sale_products::price,
                            schema::sale_products::total),
                            PRODUCT_COLUMNS))
                .load::<(SaleProduct, Product)>(conn)?
                .iter()
                .map(|tuple| {
                    FullSaleProduct {
                        sale_product: tuple.0.clone(),
                        product: tuple.1.clone()
                    }
                })
                .collect();
        Ok(FullSale{ sale, sale_products })
    }
}

pub struct Mutation;

#[juniper::object(
    Context = Context,
)]
impl Mutation {

    fn createSale(context: &Context, param_new_sale: NewSale, param_new_sale_products: NewSaleProducts) 
        -> FieldResult<FullSale> {
            use diesel::{ RunQueryDsl, Connection, QueryDsl };

            let conn: &PgConnection = &context.conn;

            let new_sale = NewSale {
                user_id: Some(context.user_id),
                ..param_new_sale
            };

            conn.transaction(|| {
                let sale = 
                    diesel::insert_into(schema::sales::table)
                        .values(new_sale)
                        .returning(
                            (
                                sales::dsl::id,
                                sales::dsl::user_id,
                                sales::dsl::sale_date,
                                sales::dsl::total,
                                sales::dsl::bill_number
                            )
                        )
                        .get_result::<Sale>(conn)?;

                let sale_products: Result<Vec<FullSaleProduct>, _> =
                    param_new_sale_products.data.into_iter().map(|param_new_sale_product| {
                        let new_sale_product = NewSaleProduct {
                            sale_id: Some(sale.id),
                            ..param_new_sale_product.sale_product
                        };
                        let sale_product =
                            diesel::insert_into(schema::sale_products::table)
                                .values(new_sale_product)
                                .returning(
                                    (
                                        sale_products::dsl::id,
                                        sale_products::dsl::product_id,
                                        sale_products::dsl::sale_id,
                                        sale_products::dsl::amount,
                                        sale_products::dsl::discount,
                                        sale_products::dsl::tax,
                                        sale_products::dsl::price,
                                        sale_products::dsl::total
                                    )
                                )
                                .get_result::<SaleProduct>(conn);

                        if let Some(param_product_id) = param_new_sale_product.sale_product.product_id {
                            let product = 
                                schema::products::table
                                    .select(PRODUCT_COLUMNS)
                                    .find(param_product_id)
                                    .first(conn);

                            Ok(
                                FullSaleProduct {
                                     sale_product: sale_product?, 
                                     product: product? 
                                }
                            )
                        } else {
                            Err(MyStoreError::PGConnectionError)
                        }
                    }).collect();

                Ok(FullSale{ sale, sale_products: sale_products? })
            })
        }

    fn updateSale(context: &Context, param_sale: NewSale, param_sale_products: NewSaleProducts) 
        -> FieldResult<FullSale> {
            use diesel::QueryDsl;
            use diesel::RunQueryDsl;
            use diesel::ExpressionMethods;
            use diesel::Connection;
            use crate::schema::sales::dsl;

            let conn: &PgConnection = &context.conn;
            let sale_id = param_sale.id.ok_or(
                diesel::result::Error::QueryBuilderError("missing id".into())
            )?;

            conn.transaction(|| {
                let sale = 
                    diesel::update(dsl::sales
                                       .filter(dsl::user_id.eq(context.user_id))
                                       .find(sale_id))
                        .set(&param_sale)
                        .get_result::<Sale>(conn)?;

                let sale_products: Result<Vec<FullSaleProduct>, _> =
                    param_sale_products.data.into_iter().map (|param_sale_product| {
                        let sale_product =
                            diesel::update(schema::sale_products::table)
                                .set(&param_sale_product.sale_product)
                                .get_result::<SaleProduct>(conn);

                        if let Some(param_product_id) = param_sale_product.sale_product.product_id {
                            let product = 
                                schema::products::table
                                    .select(PRODUCT_COLUMNS)
                                    .find(param_product_id)
                                    .first(conn);

                            Ok(
                                FullSaleProduct {
                                     sale_product: sale_product?, 
                                     product: product? 
                                }
                            )
                        } else {
                            Err(MyStoreError::PGConnectionError)
                        }

                    }).collect();

                Ok(FullSale{ sale, sale_products: sale_products? })
            })
        }

    fn destroySale(context: &Context, sale_id: i32) 
        -> FieldResult<i32> {
            use diesel::QueryDsl;
            use diesel::RunQueryDsl;
            use diesel::ExpressionMethods;
            use crate::schema::sales::dsl;

            let conn: &PgConnection = &context.conn;
            diesel::delete(dsl::sales.filter(dsl::user_id.eq(context.user_id)).find(sale_id))
                .execute(conn)?;
            Ok(sale_id)
        }
}

pub type Schema = juniper::RootNode<'static, Query, Mutation>;

pub fn create_schema() -> Schema {
    Schema::new(Query {}, Mutation {})
}

pub fn create_context(logged_user_id: i32, pg_pool: PgPooledConnection) -> Context {
    Context { user_id: logged_user_id, conn: Arc::new(pg_pool)}
} 
```

如您所见，我们的业务逻辑非常类似于 REST 端点，可以改变的是我们查询数据的方式，然后我们使用`create_schema`函数导出模式。

`src/main.rs` :

```
 let schema = std::sync::Arc::new(create_schema());

    HttpServer::new(
    move || App::new()
        .data(schema.clone()) 
```

现在，我们如何获取数据并执行我们的突变？，然后我们需要编写一些测试，看看是否一切都像预期的那样工作。

`tests/sale_test.rs` :

```
...
// create a sale:
        let query = 
            format!(
            r#"
            {{
                "query": "
                    mutation CreateSale($paramNewSale: NewSale!, $paramNewSaleProducts: NewSaleProducts!) {{
                            createSale(paramNewSale: $paramNewSale, paramNewSaleProducts: $paramNewSaleProducts) {{
                                sale {{
                                    id
                                    userId
                                    saleDate
                                    total
                                }}
                                saleProducts {{
                                    product {{
                                        name
                                    }}
                                    saleProduct {{
                                        id
                                        productId
                                        amount
                                        discount
                                        tax
                                        price
                                        total
                                    }}
                                }}
                            }}
                    }}
                ",
                "variables": {{
                    "paramNewSale": {{
                        "saleDate": "{}",
                        "total": {}
                    }},
                    "paramNewSaleProducts": {{
                        "data":
                            [{{
                                "product": {{ }},
                                "saleProduct": {{
                                    "amount": {},
                                    "discount": {},
                                    "price": {},
                                    "productId": {},
                                    "tax": {},
                                    "total": {}
                                }}
                            }}]
                    }}
                }}
            }}"#,
...

// show a sale:

       let query = format!(r#"
            {{
                "query": "
                    query ShowASale($saleId: Int!) {{
                        sale(saleId: $saleId) {{
                            sale {{
                                id
                                userId
                                saleDate
                                total
                            }}
                            saleProducts {{
                                product {{ name }}
                                saleProduct {{
                                    id
                                    productId
                                    amount
                                    discount
                                    tax
                                    price
                                    total
                                }}
                            }}
                        }}
                    }}
                ",
                "variables": {{
                    "saleId": {}
                }}
            }}
        "#, id).replace("\n", "");

...

// update a sale

        let query = 
            format!(
            r#"
            {{
                "query": "
                    mutation UpdateSale($paramSale: NewSale!, $paramSaleProducts: NewSaleProducts!) {{
                            updateSale(paramSale: $paramSale, paramSaleProducts: $paramSaleProducts) {{
                                sale {{
                                    id
                                    saleDate
                                    total
                                }}
                                saleProducts {{
                                    product {{ name }}
                                    saleProduct {{
                                        id
                                        productId
                                        amount
                                        discount
                                        tax
                                        price
                                        total
                                    }}
                                }}
                            }}
                    }}
                ",
                "variables": {{
                    "paramSale": {{
                        "id": {},
                        "saleDate": "{}",
                        "total": {}
                    }},
                    "paramSaleProducts": {{
                        "data":
                            [{{
                                "product": {{}},
                                "saleProduct": 
                                {{
                                    "amount": {},
                                    "discount": {},
                                    "price": {},
                                    "productId": {},
                                    "tax": {},
                                    "total": {}
                                }}
                            }}]
                    }}
                }}
            }}"#

...

// delete a sale:

        let query = format!(r#"
            {{
                "query": "
                    mutation DestroyASale($saleId: Int!) {{
                        destroySale(saleId: $saleId)
                    }}
                ",
                "variables": {{
                    "saleId": {}
                }}
            }}
        "#, id).replace("\n", "");
...

// search for a sale with specific date:

       let query = format!(r#"
            {{
                "query": "
                    query ListSale($search: NewSale!, $limit: Int!) {{
                        listSale(search: $search, limit: $limit) {{
                            data {{
                                sale {{
                                    id
                                    saleDate
                                    total
                                }}
                                saleProducts {{
                                    product {{
                                        name
                                    }}
                                    saleProduct {{
                                        amount
                                        price
                                    }}
                                }}
                            }}
                        }}
                    }}
                ",
                "variables": {{
                    "search": {{
                        "saleDate": "2019-11-10"
                    }},
                    "limit": 10
                }}
            }}
        "#).replace("\n", ""); 
```

你可以在这里看一下完整的源代码[。](https://github.com/practical-rust-web-development/mystore/tree/v9.0)