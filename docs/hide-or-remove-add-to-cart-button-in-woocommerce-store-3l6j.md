# 隐藏或删除 WooCommerce 商店中的“添加到购物车”按钮

> 原文：<https://dev.to/alexanderbiscajin/hide-or-remove-add-to-cart-button-in-woocommerce-store-3l6j>

[![](img/c0c35aad2226770a7b9ba5b034ad9bd2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CzgKictp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.wpblog.com/wp-content/uploads/2017/09/add-to-cart.jpg)

这篇文章最初发表在 WPblog [隐藏或删除 WooCommerce 商店](https://www.wpblog.com/add-to-cart-button-in-woocommerce-store/)中的添加到购物车按钮。

我发现大多数店主在删除或禁用“添加到购物车”按钮时会感到困惑。现在，对于任何商店页面或产品，您都可以删除“添加 2 个购物车”按钮。你只需要遵循这些简单的步骤。在 woocommerce.php(位于 WP-内容/插件/woocommerce)

 `function WpBlog() {
remove_action( 'woocommerce_after_shop_loop_item', 'woocommerce_template_loop_add_to_cart');
remove_action( 'woocommerce_single_product_summary', 'woocommerce_template_single_add_to_cart');
return WooCommerce::instance();
}` 

刷新商店页面后，您会看到按钮将从页面中移除。

您还可以删除特定产品页面的“添加到购物车”按钮。

将此代码添加到 functions.php(位于主题文件夹中):

 `add_filter('woocommerce_is_purchasable', 'wpblog_specific_product');
function wpblog_specific_product($purchaseable_product_wpblog, $product) {
return ($product->id == specific_product_id (512) ? false : $purchaseable_product_wpblog);
}`