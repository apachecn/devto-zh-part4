# 在 VueJs 中从头开始创建砖石布局(和 CSS 网格)

> 原文：<https://dev.to/johnhalsey/creating-a-masonry-layout-from-scratch-in-vuejs-and-css-grid-4c22>

我用 Laravel 和 VueJS 写了自己的网站。没什么特别的，就是一个关于我和我的一些技能的页面，还有一个博客页面。最终，我打算为一些项目创建一个页面，展示我在业余时间创作的一些东西。

几天前，博客索引页面，嗯…狗屎，只是一个分页的帖子列表，没有什么令人兴奋的东西，布局也很糟糕。我想用砖石的感觉来装饰它，但不想导入任何包，所以尝试自己构建它。

这就是结果。点击这里查看[johnhalsey.co.uk/blog](https://johnhalsey.co.uk/blog)

[![](img/55aa101ca1d1b084832f05315352cd88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mLsGdfHd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/50g5rr2l0o25m6vpmprg.png)

那我是怎么做到的呢？

首先，我调用 api 从数据库中获取文章。我在 Laravel 资源中发回一个 JSON 响应(这里没有显示)。

```
export default {
    name: 'Posts',
    props: {
        category: {
            type: String
        }
    },
    data () {
        return {
            posts: []
        }
    },
    mounted () {
       this.getPosts()
    },
    methods: {
        getPosts () {
            let params = {}
            if(this.category != '') {
                params.category = this.category
            }
            window.axios.get('/api/posts', {params})
                .then(response => {
                    this.posts = response.data.data
                    this.calculateImageCount()
                })
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

砖石布局是通过 CSS Grid 和 javascript 计算每个帖子需要占用多少网格行来实现的，但它不能开始计算，直到所有的图像都加载并呈现在浏览器上，因为不是所有的帖子都有图像，所以我计算了有多少帖子有图像。

```
data () {
    return {   
        imageCounter: 0
    }
},
methods: {
    calculateImageCount () {
        for (let i = 0; i < this.posts.length; i++) {
            if (this.posts[i].media.featured.medium != '') {
                this.imageCounter++
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在模板中，当遍历帖子时，我标记每张图片的加载时间，并增加一个计数器。

```
<template>
    <div class="masonry">
        <div v-for="(post, key) in posts"
             :key="key"
             class="card"
        >
            <div class="card-content">
                <div v-if="post.media.featured.medium != ''">
                    <img :src="post.media.featured.medium" :alt="post.title" class="img-responsive" @load="rendered">
                </div>
                <div class="p-20">
                    <a :href="post.link">
                        <h3>{{ post.title }}</h3>
                    </a>
                    <a v-for="category in post.categories" :key="category.id" :href="'/blog?category=' + category.slug">{{ category.title }} <br></a>
                    <p class="font-12">Posted on {{ post.published_at }}</p>
                </div>
            </div>
        </div>
    </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

```
export default {
    data () {
        return {
            imagesCount: 0
        }
    },
    methods: {
        rendered () {
            this.imagesCount++
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我观察图像计数器，当它达到我所期望的图像数量时，我就可以计算出明信片的高度。

```
watch: {
    imagesCount: function () {
        if(this.imagesCount == this.imageCounter){
            this.resizeAllMasonryItems()
        }
    }
}, 
```

Enter fullscreen mode Exit fullscreen mode

然后奇迹发生了。现在所有的图片都已经加载完毕，`resizeAllMasonryItems()`方法遍历所有的帖子，并调用另一个方法来实际调整条目的大小，它通过对每张帖子卡应用一个`grid-row-end: span [dynamic number]`样式来做到这一点。

```
resizeAllMasonryItems () {
    // Get all item class objects in one list
    let allItems = document.getElementsByClassName('card');

    /*
     * Loop through the above list and execute the spanning function to
     * each list-item (i.e. each masonry item)
     */
    for (let i = 0; i < allItems.length; i++) {
        this.resizeMasonryItem(allItems[i]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

每一项都被传递到`resizeMasonryItem()`方法中。

```
resizeMasonryItem (item) {
    /* Get the grid object, its row-gap, and the size of its implicit rows */
    let grid = document.getElementsByClassName('masonry')[0],
        rowGap = parseInt(window.getComputedStyle(grid).getPropertyValue('grid-row-gap')),
        rowHeight = parseInt(window.getComputedStyle(grid).getPropertyValue('grid-auto-rows'));

    /*
     * Spanning for any brick = S
     * Grid's row-gap = G
     * Size of grid's implicitly create row-track = R
     * Height of item content = H
     * Net height of the item = H1 = H + G
     * Net height of the implicit row-track = T = G + R
     * S = H1 / T
     */

    let rowSpan = Math.ceil((item.querySelector('.card-content').getBoundingClientRect().height + rowGap) / (rowHeight + rowGap));

    /* Set the spanning as calculated above (S) */
    item.style.gridRowEnd = 'span ' + rowSpan;
}, 
```

Enter fullscreen mode Exit fullscreen mode

我使用 CSS Grid，用一些基本的 CSS 样式化父元素。

```
<style type="text/css">
    .masonry {
        display: grid;
        grid-gap: 15px;
        grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
        grid-auto-rows: 0;
    }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

最后，当创建组件时，我加载事件监听器并调整页面大小，以保持计算卡片高度。从技术上讲，我甚至不需要加载事件监听器，但有它似乎很好。

```
created () {
    let masonryEvents = ['load', 'resize'];
    let vm = this
    masonryEvents.forEach(function (event) {
        window.addEventListener(event, vm.resizeAllMasonryItems);
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

就这样，现在我有了一个非常酷的博客文章布局。整个事情看起来是这样的。它的反应也很快。

```
<template>
    <div class="masonry">
        <div v-for="(post, key) in posts"
             :key="key"
             class="card"
        >
            <div class="card-content">
                <div v-if="post.media.featured.medium != ''">
                    <img :src="post.media.featured.medium" :alt="post.title" class="img-responsive" @load="rendered">
                </div>
                <div class="p-20">
                    <a :href="post.link">
                        <h3>{{ post.title }}</h3>
                    </a>
                    <a v-for="category in post.categories" :key="category.id" :href="'/blog?category=' + category.slug">{{ category.title }} <br></a>
                    <p class="font-12">Posted on {{ post.published_at }}</p>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'Posts',
        props: {
            category: {
                type: String
            }
        },
        data () {
            return {
                posts: [],
                imageCounter: 0,
                imagesCount: 0
            }
        },
        mounted () {
            this.getPosts()
        },
        created () {
            let masonryEvents = ['load', 'resize'];
            let vm = this
            masonryEvents.forEach(function (event) {
                window.addEventListener(event, vm.resizeAllMasonryItems);
            });
        },
        watch: {
            imagesCount: function () {
                if(this.imagesCount == this.imageCounter){
                    this.resizeAllMasonryItems()
                }
            }
        },
        methods: {
            rendered () {
                this.imagesCount++
            },
            getPosts () {
                let params = {}
                if(this.category != '') {
                    params.category = this.category
                }
                window.axios.get('/api/posts', {params})
                    .then(response => {
                        this.posts = response.data.data
                        this.calculateImageCount()
                    })
            },
            calculateImageCount () {
                for (let i = 0; i < this.posts.length; i++) {
                    if (this.posts[i].media.featured.medium != '') {
                        this.imageCounter++
                    }
                }
            },
            resizeMasonryItem (item) {
                /* Get the grid object, its row-gap, and the size of its implicit rows */
                let grid = document.getElementsByClassName('masonry')[0],
                    rowGap = parseInt(window.getComputedStyle(grid).getPropertyValue('grid-row-gap')),
                    rowHeight = parseInt(window.getComputedStyle(grid).getPropertyValue('grid-auto-rows'));

                /*
                 * Spanning for any brick = S
                 * Grid's row-gap = G
                 * Size of grid's implicitly create row-track = R
                 * Height of item content = H
                 * Net height of the item = H1 = H + G
                 * Net height of the implicit row-track = T = G + R
                 * S = H1 / T
                 */

                let rowSpan = Math.ceil((item.querySelector('.card-content').getBoundingClientRect().height + rowGap) / (rowHeight + rowGap));

                /* Set the spanning as calculated above (S) */
                item.style.gridRowEnd = 'span ' + rowSpan;
            },
            resizeAllMasonryItems () {
                // Get all item class objects in one list
                let allItems = document.getElementsByClassName('card');

                /*
                 * Loop through the above list and execute the spanning function to
                 * each list-item (i.e. each masonry item)
                 */
                for (let i = 0; i < allItems.length; i++) {
                    this.resizeMasonryItem(allItems[i]);
                }
            }
        }
    }
</script>

<style lang="scss" type="text/css">
    .masonry {
        display: grid;
        grid-gap: 15px;
        grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
        grid-auto-rows: 0;
    }
</style> 
```

Enter fullscreen mode Exit fullscreen mode