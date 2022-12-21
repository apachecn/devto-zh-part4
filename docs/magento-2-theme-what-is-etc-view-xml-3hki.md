# Magento 2 主题什么是 etc/view.xml

> 原文：<https://dev.to/asrar7787/magento-2-theme-what-is-etc-view-xml-3hki>

在该文件中，可以配置前端相关属性，即产品图像的大小。此外，可以在这里添加图像的自定义 ID 及其大小，然后可以在 PHP 中生成该图像。

此外，有些属性可以在不覆盖模板文件的情况下设置:

`<vars module="Magento_ConfigurableProduct">
<var name="gallery_switch_strategy">replace</var>
</vars>`

现在，在`vendor/magento/module-configurable-product/view/frontend/templates/product/view/type/options/configurable.phtml`中，变量`gallery_switch_strategy`的取值如下

`<?php /* @escapeNotVerified */ echo $block->getVar('gallery_switch_strategy', 'Magento_ConfigurableProduct') ?: 'replace'; ?>`

因此，我们可以使用 theme 的`etc/view.xml`，而不是覆盖模板文件。

主题的`etc/view.xml`可用于从捆绑中排除任何`js`文件、任何`js`组件或包含静态资产的完整目录，如下所示:

`<exclude>
<!-- js file -->
<item type="file">Lib::jquery/jquery.min.js</item>
<!-- Catalog level : js component -->
<item type="file">Magento_Catalog::js/zoom.js</item>
<!-- Dir level : exclude bundling for any static contents -->
<item type="directory">Lib::modernizr</item>
<item type="directory">Lib::tiny_mce</item>
<item type="directory">Lib::varien</item>
<item type="directory">Lib::jquery/editableMultiselect</item>
<item type="directory">Lib::jquery/jstree</item>
<item type="directory">Lib::jquery/fileUploader</item>
<item type="directory">Lib::css</item>
<item type="directory">Lib::lib</item>
<item type="directory">Lib::mage/backend</item>
</exclude>`