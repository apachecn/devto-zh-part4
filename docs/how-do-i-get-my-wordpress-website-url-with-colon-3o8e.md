# 我如何获得带有冒号':'的 wordpress 网站 url..？

> 原文：<https://dev.to/ahsanmunawar/how-do-i-get-my-wordpress-website-url-with-colon-3o8e>

```
add_action( 'init', function() {

    add_shortcode( 'site_url', function( $atts = null, $content = null ) {
        return site_url();
    });

}); 
```

我正在使用短代码 **[ `site_url` ]** ，我的短代码函数返回了`https//shad-pro.com/contact-us`

如何用冒号 **`:`** 得到合适的`Url`