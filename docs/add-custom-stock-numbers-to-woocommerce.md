# 向 WooCommerce 添加自定义库存编号

> 原文：<https://dev.to/alammowais/add-custom-stock-numbers-to-woocommerce>

经常发生的情况是，库存经常在仓库和生产车间之间分配。问题是跟踪分布在两个地点的库存。

令人惊讶的是，没有内置的解决方案来跟踪分布式库存。我决定创建一个包含自定义字段的自定义解决方案。将有两个自定义的“工厂库存”字段和“总库存”。

**总库存**字段将包含**工厂库存**和内置库存控制的 WooCommerce 的总和。这是跟踪总库存数量的简单方法，即使它们实际上分布在两个位置。

目前，该解决方案适用于简单的产品，但也可以扩展到可变产品。为了使事情变得更好，我在 product list 列中添加了两列用于这些库存水平。

将以下代码添加到**functions.php**文件中。

```
<?php
add_action( 'wp_enqueue_scripts', 'theme_enqueue_styles' );
function theme_enqueue_styles() {
wp_enqueue_style( 'parent-style', get_template_directory_uri() . '/style.css' );

}

add_action('admin_head', 'my_custom_fonts');

function my_custom_fonts() {
echo '<style>
._total_inventory_field {
        color: red;
    }
._total_inventory_field input {
background-color:red;
color: #fff;
}
</style>';
}

add_action('save_post', 'myWoo_savePost', 10, 2);

function myWoo_savePost($postID, $post) {
if (isset($post->post_type) && $post->post_type == 'product') {
$shopstock = get_post_meta($post->ID, '_stock', true);
$factorystock = get_post_meta($post->ID, '_factory_inventory', true);
$totalstock = $shopstock + $factorystock;
update_post_meta($post->ID, '_manage_stock', 'yes');
update_post_meta($post->ID, '_total_inventory', $totalstock);
}
}

add_action( 'woocommerce_product_options_inventory_product_data', 'woo_add_custom_general_fields' );

// Save Fields
add_action( 'woocommerce_process_product_meta', 'woo_add_custom_general_fields_save');

function woo_add_custom_general_fields() {

    global $woocommerce, $post;

echo '<div class="options_group">';

woocommerce_wp_text_input(
array(
'id' => '_factory_inventory',
'label' => __( 'Factory Inventory', 'woocommerce' ),
'description' => __( 'Please Enter Your Factory Count.', 'woocommerce' ),
'type' => 'number',
)
);
// Number Field
woocommerce_wp_text_input(
array(
'id' => '_total_inventory',
'label' => __( 'Total Inventory', 'woocommerce' ),
'placeholder' => "",
'description' => __( 'Stock Must Match.', 'woocommerce' ),
'type' => 'number',
'custom_attributes' => array(
'step' => 'any',
'min' => '0',
)
)
);
echo '</div>';

}
function woo_add_custom_general_fields_save( $post_id ){

$woocommerce_number_field = $_POST['_factory_inventory'];
if( !empty( $woocommerce_number_field ) )
update_post_meta( $post_id, '_factory_inventory', esc_attr( $woocommerce_number_field ) );
}

function add_custom_stock_values( $column ) {
$column['factory_stock'] = 'Factory Stock';
$column['total_stock'] = 'Total Stock';

return $column;
}
add_filter( 'manage_product_posts_columns', 'add_custom_stock_values');

function add_value_to_custom_stock( $column_name, $post_id ) {

    $custom_fields = get_post_custom( $post_id );

    switch ($column_name) {
        case 'factory_stock' :
echo $custom_fields['_factory_inventory'][0];
break;

        case 'total_stock' :
echo $custom_fields['_total_inventory'][0];
break;

        default:
    }
}

add_action( 'manage_product_posts_custom_column', 'add_value_to_custom_stock', 10, 2 ); 
```

Enter fullscreen mode Exit fullscreen mode

在本文中，我讨论了如何跟踪分布在两个地点的库存水平。我还在 Product List 列中添加了两列，以便您可以方便地查看两个库存水平。