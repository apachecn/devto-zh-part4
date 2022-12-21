# 如何在 Magento 2 中实现按“畅销”产品排序？

> 原文：<https://dev.to/planetwebsol/how-to-implement-sort-by-best-seller-products-in-magento-2-4cp8>

## 什么是“畅销”产品？

拥有最高销售记录的产品被称为网站的“最畅销”产品。换句话说，具有最大销售量的可用商店的产品是“最畅销”的产品。

## 为什么在 Magento 2 中需要对“畅销”产品进行排序？

### 鼓励销售和转化

向网站的访问者和客户展示你的畅销产品总是好的。你展示的受欢迎的产品越多，它们被其他顾客购买的机会就越大。

### 快速购买决策

现代购物者在购物前有阅读评论的习惯。显然，商店最畅销的产品会有更多顾客的正面反馈和评级。这将有助于潜在的买家迅速做出决定，减少购物时间。

### 增强促销活动

当商店有畅销产品列表时，它可以在社交媒体上进行促销和活动时使用

## Magento 2 中实现按“畅销”产品排序的指南？

按畅销书排序块可以安装在所需的产品页面，按照下面的一套编码。

**第一步:创建一个 di.xml 文件**

```

<preference for="Magento\Catalog\Block\Product\ProductList\Toolbar" type="Company\Module\Block\Product\ProductList\Toolbar"/>
<preference for="Magento\Catalog\Model\Config" type="Company\Module\Model\Config"/> 
```

**Step 2: Create a configuration model**

```
class Config extends \Magento\Catalog\Model\Config
{
    public function getAttributeUsedForSortByArray()
    {
       $options = ['bestseller' => __('Best seller')];
        foreach ($this->getAttributesUsedForSortBy() as $attribute) {
            /* @var
 $attribute \Magento\Eav\Model\Entity\Attribute\AbstractAttribute */
            $options[$attribute->getAttributeCode()] = $attribute->getStoreLabel();
        }

       return $options;
    }
}

```

第三步:创建一个模块。

```
class Toolbar extends \Magento\Catalog\Block\Product\ProductList\Toolbar
{
    public function setCollection($collection)
    {
        if($this->getCurrentOrder()=="bestseller")
        {
              $collection->getSelect()->joinLeft( 
                'sales_order_item', 
                'e.entity_id = sales_order_item.product_id', 
                array('qty_ordered'=>'SUM(sales_order_item.qty_ordered)')) 
                ->group('e.entity_id') 
                ->order('qty_ordered '.$this->getCurrentDirectionReverse());
        }

        $this->_collection = $collection;

        $this->_collection->setCurPage($this->getCurrentPage());

        $limit = (int)$this->getLimit();
        if ($limit) {
            $this->_collection->setPageSize($limit);
        }
        if ($this->getCurrentOrder()) {
            $this->_collection->setOrder($this->getCurrentOrder(), $this->getCurrentDirection());
        }
        return $this;
    }

    public function getCurrentDirectionReverse() {
            if ($this->getCurrentDirection() == 'asc') {
                return 'desc';
            } elseif ($this->getCurrentDirection() == 'desc') {
                return 'asc';
            } else {
                return $this->getCurrentDirection();
            }
        }

}

```

## 想在你的商店里拥有“畅销书”分类功能吗？

如果你的电子商务商店被剥夺了“最畅销”产品或其他功能的排序，请与我们的专家团队 **[Magento 2 开发者](https://www.planetwebsolution.com/magento-development-services)** 交谈。