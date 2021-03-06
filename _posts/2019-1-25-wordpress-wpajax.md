---
layout: post
title:
modified:
categories: Tech

tags: [wordpress]

comments: true
---

### wordpress 引入 js 脚本

<https://www.solagirl.net/wordpress-ways-to-load-js-css.html>

```php
function wpzan_scripts(){
    wp_enqueue_style( 'wp-zan-0.0.10', wpzan_css_url('wp-zan-0.0.10'), array()
    , WPZAN_VERSION );
    wp_enqueue_script( 'wp-zan-0.0.10',  wpzan_js_url('wp-zan-0.0.10'), array()
    , WPZAN_VERSION );
    //给上面enqueue加载的js传递1个对象,wpzan_ajax_url，它的值就是1个字符串.
    wp_localize_script( 'wp-zan-0.0.10', 'wpzan_ajax_url', WPZAN_ADMIN_URL . "admin-ajax.php");
}
```

js 如何使用该对象?就是 jquery ajax post 时的 url:

```js
jQuery.post(wpzan_ajax_url, {
    "action": "wpzan",
    "post_id": post_id,
    "user_id": user_id
}, function(result) { //console.log(result);
//...
}
```

## 后台 hook

```sh
action=wpzan:
登录用户: wp_ajax_wpzan
未登录用户: wp_ajax_nopriv_wpzan
```

后台处理逻辑通过 hook 函数:

```php
add_action( 'wp_ajax_wpzan', 'wpzan_callback');
//未登录用户
add_action( 'wp_ajax_nopriv_wpzan', 'wpzan_callback');
```

对于登录用户，后台界面已经自动赋值了一个 js 全局变量 ajaxurl,因此,在上述情况下,js 代码中可以直接引用此全局变量作为 ajax 的请求路径.

未登录仍然只能用上面的方法传递 url 到 js 对象里.

## 通过 url 参数控制是否加载 js

```sh
    if ( isset( $_GET['goto_comments']) ) {
        $flag = $_GET['goto_comments'];
        ff_log($flag );
        wp_register_script( 'goto-comments', get_template_directory_uri()
         . '/core/functions/goto/goto-comments.js', array(), THEME_VERSION);
        wp_enqueue_script('goto-comments');
    } else {
        wp_dequeue_script('goto-comments');
    }
```
