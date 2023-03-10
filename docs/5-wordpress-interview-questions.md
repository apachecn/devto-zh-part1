# 5 个 WordPress 面试问题

> 原文：<https://dev.to/gyrocode/5-wordpress-interview-questions>

参见下面的一些 WordPress 面试问题来提高你的编程技能和实践。

#### 1。使用行号，请描述是什么问题阻止了下面的 PHP/WordPress 代码正常工作，以及你将做什么改变来优化代码，使之更有效，如果有的话。

```
<?
class User {
   public function __get($username) {
      if(is_user_logged_in($username)){
         return 'valid';
      }

      return 'invalid';
   }
}

$e = new User();
if(empty($e->checkinfo))
   $x = 0;

//Menu
for($i = 0; $i < 10; $i++) {
   echo '<ul id="'.$i."">';
   for($j = 0; $j < 10; $j++) {
      if($x == 0)
         break

   echo '<li id=".'$j'.">Menu Item'.$j.'</li>';
   }

   if($x =! 0){
      echo '</ul>';
   }
}

/*Some more menu stuff here...*/
$options = get_option( 'theme-options' );
$colors = array('coral','toffee','sunshine','wildflower','wine');
$i = 0;
echo "<select name='theme-options[color]'>"
while($i < 5); {
echo '<option value="$colors[$i]"'. ((esc_attr( $options['color']== $color[$i])? 'selected="selected"': '' ).'>$colors[$i]</option>';
$i++;
}
echo '</select>'; 
```

Enter fullscreen mode Exit fullscreen mode

#### 2。使用 JavaScript 和 jQuery，请提供将在页面加载时删除模板中占位符的代码，以产生如下所示的结果。

*模板:*

```
<div class="class-replace">
   <p>
      <a href="http://[SITE].com">[NAME]</a> © [START]-[CURRENT]. All rights reserved
   </p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

*预期结果:*

```
<div class="copyright">
   <p>
      <a href="http://foobar.com">FooBar</a> © 2011-2017\. All rights reserved
   </p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

#### 3。下面的函数从 WordPress 用户管理区移除了什么？

```
function remove_stuff ( $actions )
   if( !is_super_admin() && !current_user_can( 'edit_theme_options' ) ) {
      unset($actions['inline hide-if-no-js']);
      return $actions;
   } else {
      return $actions;
   }
}
add_filter( 'page_row_actions', 'remove_stuff' );
add_filter( 'post_row_actions', 'remove_stuff' ); 
```

Enter fullscreen mode Exit fullscreen mode

#### 4。一些用户发现很难通过媒体上传器从 URL 插入媒体。由于 98%的用户不使用该标签，我们希望将其删除。请提供一个例子，你会如何删除所有用户的标签。

#### 5。非超级管理员或不能编辑主题选项的用户不允许创建新页面。即使去掉了这个功能，WordPress 仍然显示“添加新按钮”。请提供一个例子，你会如何删除此按钮。