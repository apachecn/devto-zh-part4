# 更改 WooCommerce 动态定价显示

> 原文：<https://dev.to/alvinarichard/change-woocommerce-dynamic-pricing-display-29ne>

[WooCommerce 定价](https://www.cloudways.com/blog/change-woocommerce-price-display/)展示是产品页面必不可少的一部分。有两种方法可以改变 WooCommerce 价格显示。

woocommerce_get_price_html:价格显示在产品和商店页面上。
·woo commerce _ cart _ item _ price:产品价格显示在购物车表中。

**更改所有 WooCommerce 产品的价格显示**
 `function cw_change_product_price_display( $price ) {
$price .= ' At Each Item Product';
return $price;
}
add_filter( 'woocommerce_get_price_html', 'cw_change_product_price_display' );
add_filter( 'woocommerce_cart_item_price', 'cw_change_product_price_display' );` 

[![](img/1814afc563716f76f0e09690c9fac558.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vgdNxTV5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.cloudways.com/blog/wp-content/uploads/01-1.jpg)

**根据产品字段改变价格显示**

如果您想要更改某些产品的价格显示，那么为列表中的每个产品添加一个字段，并在代码中检索该字段。

[![](img/185c471a143ae9efaa77249b47c9a4b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NA2LEf-z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.cloudways.com/blog/wp-content/uploads/08-1.jpg)