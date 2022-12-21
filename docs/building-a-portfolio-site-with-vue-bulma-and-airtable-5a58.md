# 用 Vue、布尔玛和 Airtable 建立一个投资组合网站

> 原文：<https://dev.to/markjohnson303/building-a-portfolio-site-with-vue-bulma-and-airtable-5a58>

我离开 full-stack bootcamp 已经大约 3 年了，这是我第一次需要建立一个投资组合网站。我想做一些相对简单的东西，可以很容易地更新，并且随着时间的推移很容易扩展和改进。

这个教程完美吗？见鬼不！这是我写的第一个教程，我一直在自学 Vue，所以有些部分我肯定可以做得更好(如果你有不同的做法，请在评论中告诉我)。也就是说，我知道这可能对外面的人有帮助！

你可以在 github 上看到我整个作品集的代码，就是从这个起点创建的，这里:[https://github.com/markjohnson303/portfolio](https://github.com/markjohnson303/portfolio)

一个完成的例子在 [hellomark.dev](https://hellomark.dev/) 中，但它是一个正在进行的工作，您可能会看到一些与这里描述的不同的东西。

## 工具

**[Vue.js:](https://vuejs.org/)** 我为这个项目选择了 Vue，因为它是我最熟悉的框架。有些人可能会说这对于像这样的小项目来说太大了，对你来说，可能是这样。它对我来说很好，因为它足够舒适和灵活，适合我将来用它做什么。这也是我希望在我的下一个角色中使用的，为什么不呢！

布尔玛:在这个项目之前，我没有使用过布尔玛，但我想要的东西，可以让我很快得到网站，然后随着时间的推移，改善造型容易。布尔玛很容易学习，但很容易建立。它没有世界上最大的组件库，但它拥有的是坚实的基础。

**[Airtable:](https://airtable.com/)** 我一直想在一个项目中使用 Airtable。根据 Airtable 的说法，它是“部分电子表格，部分数据库”，可以灵活地用于各种用途。我在这里用它作为 CMS，因为它真的很容易使用，并且有一个很棒的 API 和很棒的文档(那是为你的数据库定制的)。现在它已经设置好了，我可以在网站上用它做各种有趣的事情。而且是免费的！

## 入门

您需要做的第一件事是设置您的 Vue 项目。我们将使用 Vue CLI 来搭建项目。确保您安装了 vue 和 Vue CLI:

`$ npm install -g vue`
T1】

然后创建您的项目:
`$ vue create portfolio`

然后火起来:
`$ npm run serve`

Vue CLI 提供了我们需要的大量文件和文件夹，为您提供了一个非常有用的起点。我们将在此基础上继续努力。

让我们也添加我们的 CSS 框架，布尔玛，现在。
`$ npm install --s bulma`

并将 Sass 样式表添加到我们的`App.vue`文件

```
<style lang="sass">
@import "~bulma/bulma.sass"
</style> 
```

Enter fullscreen mode Exit fullscreen mode

您可以在此处对导入上方的布尔玛默认值进行任何调整。

我们将安装 Axios(用于与我们的 Airtable API 一起工作)
`$ npm install --s axios`

我们需要 [VueSimpleMarkdown](https://www.npmjs.com/package/vue-simple-markdown) ,这样我们就可以用 Markdown 来撰写和设计我们的帖子。

`$ npm install -s vue-simple-markdown`

在`main.js`中，我们将输入:

```
import VueSimpleMarkdown from 'vue-simple-markdown'
import 'vue-simple-markdown/dist/vue-simple-markdown.css'

Vue.use(VueSimpleMarkdown) 
```

Enter fullscreen mode Exit fullscreen mode

## 设置我们的路线

我们将有这个网站的 5 个主要路线:关于，联系，主页，项目，和项目。让我们在`src/router.js`中设置这些。

```
import Vue from "vue";
import Router from "vue-router";
import Home from "./views/Home.vue";
import About from "./views/About.vue";
import Contacts from "./views/Contact.vue";
import Projects from "./views/Projects.vue"
import Project from "./views/Project.vue"

Vue.use(Router);

export default new Router({
  mode: "history",
  base: process.env.BASE_URL,
  routes: [
    {
      path: "/",
      name: "home",
      component: Home
    },
    {
      path: "/about",
      name: "about",
      component: About
    },
    {
      path: "/contact",
      name: "contact",
      component: Contact
    },
    {
      path: "/projects",
      name: "projects",
      component: Projects
    },
    {
      path: "/project/:slug",
      name: "project",
      component: Project
    }
  ]
});
} 
```

Enter fullscreen mode Exit fullscreen mode

出来的奇数是`path: "/project/:slug"`。稍后，我们将使用这条路径显示基于 slug 的 Airtable 中的一个项目。

我们还将为`src/views`中的每个组件创建一个空组件，例如这里的空组件`Contact.vue`。我们稍后会把这些填进去。

```
<template>
  <div>

  </div> </template> 
<script>

export default {
  name: "contact",
};

</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 添加页眉和页脚

让我们添加我们的页眉(带导航)和页脚，一点点的风格，和一点点 Vue 的魔力，使它在移动上工作。我们将把这段代码放在`App.vue`中，这样它将出现在每个视图中。

```
<template>
  <div id="app">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <nav class="navbar" role="navigation" aria-label="main navigation">
      <div class="navbar-brand">
        <router-link class="navbar-item" to="/">
          <img src="./assets/name-mark.jpg" width="112" height="28">
        </router-link> 
        <a role="button" class="navbar-burger burger" aria-label="menu" aria-expanded="false" data-target="navbarBasicExample" :class="{ 'is-active': showNav }" @click="showNav = !showNav">
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
        </a>
      </div> 
      <div id="navbarBasicExample" class="navbar-menu" :class="{ 'is-active': showNav }">
        <div class="navbar-start">

        </div> 
        <div class="navbar-end">
          <router-link to="/" class="navbar-item">
            Home
          </router-link>
          <router-link to="/about" class="navbar-item">
           About
          </router-link>
          <router-link to="/projects" class="navbar-item">
           Projects
          </router-link>
          <router-link to="/contact" class="navbar-item">
           Contact
          </router-link>
       </div>
     </div>
   </nav>
   <router-view />
   <footer class="footer">
    <div class="content has-text-centered">
      <p>
        Built by Mark Johnson with Vue.js, Bulma, and Airtable.
      </p>
    </div>
  </footer> </div> </template> 
<script>
  export default {
    name: "App",
    data() {
      return{
        showNav: false
      }
    },
  };
</script> 
<style type="text/css">
#app {
  min-height: 100vh;
  overflow: hidden;
  display: block;
  position: relative;
  padding-bottom: 168px; /* height of your footer */
}
footer {
 position: absolute;
 bottom: 0;
 width: 100%;
}
</style> 
<style lang="sass">
@import "~bulma/bulma.sass"
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 静态页面

“关于”、“主页”和“联系人”页面上没有任何动态内容，所以您可以随意添加您喜欢的任何内容。例如，这是我对主页所做的。我在这里把它保持得非常简单，但是你可以随心所欲地修饰它。

```
<template>
  <div>
    <div class="hero is-cover is-relative is-fullheight-with-navbar is-primary">
        <div class="hero-body">
            <div class="container">
                <h1 class="title is-1">Hello, I'm Mark.</h1>
                <h2 class="subtitle is-3">A customer focused, entrepreneurially minded web developer.</h2>
            </div>
        </div>
    </div>
  </div>
</template>

<script>

export default {
  name: "home",
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 构建项目页面

项目页面是事情开始变得有趣的地方。我们将从 Airtable 中提取信息，并显示每个项目的摘要卡。

### 设置 Airtable

在 Airtable 上创建一个名为“项目”的新基地。创建以下字段:“标题”(单行文本)、“slug”(单行文本)、“正文”(长文本)、“图像”(附件)、“发布日期”(日期)、“发布”(复选框)、“摘录”(单行文本)。

瞧啊。你有一个简单的 CMS！用几行虚拟数据填充它，这样您就可以看到您正在处理什么。确保“鼻涕虫”是独一无二的！我们将在后面的步骤中使用它来构建我们的 url。

### 建立展示项目的卡片

我们将创建一个组件来显示我们的项目摘要。它还可以重复使用，这样你以后就可以用同样的东西创建一个博客了！

在`src/components`中创建一个名为`PostCard.vue`的新文件。填写如下:

```
<template>
    <div class="post-card">
        <div class="card">
            <div class="card-image">
                <figure class="image is-square">
                    <img :src="image" alt="Placeholder image">
                </figure>
            </div>
            <div class="card-content">
                <div class="media">
                    <div class="media-content">
                        <p class="title is-4">{{title}}</p>
                        <p class="subtitle is-6">{{date}}</p>
                    </div>
                </div>
                <div class="content">
                    <p>{{snippet}}</p>
                    <router-link :to="'/project/'+slug" class="button is-fullwidth">View Project</router-link>
                </div>
            </div>
        </div>
    </div> </template> 
<script>
    export default {
        name: "PostCard",
        props: {
            title: String,
            date: String,
            snippet: String,
            image: String,
            slug: String
        }
    };
</script> 
```

Enter fullscreen mode Exit fullscreen mode

在我们从 Airtable 的 API 获得项目后，我们将从项目页面引入道具。他们将在模板中填入内容和完整项目视图的链接。

### 创建服务以引入项目

让我们建立到 Airtable API 的连接。在`src/services`做一个目录，在里面放一个名为`ProjectsService.js`的文件。

在项目服务中，我们将使用 Axios 调用 Airtable API 并获取所有项目。您的文件应该如下所示:

```
import axios from 'axios'

const Axios = axios.create({
  baseURL: "https://api.airtable.com/v0/[YOUR APP ID]/Projects"
});

Axios.defaults.headers.common = {'Authorization': `Bearer ` + process.env.VUE_APP_AIRTABLEKEY}

export default{
  getProjects() {
    return Axios.get()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你需要用你的 Airtable 底座的 ID 来设置`baseURL`。在 Airtable 的 URL 中找到它。

您还需要从 Airtable 添加您的 API 密钥。我把我的放在 Vue 项目根目录下的一个名为`.env.local`的文件中。该文件列在中。gitignore，这样你就不会冒险暴露它。文件里只有这个:`VUE_APP_AIRTABLEKEY=[YOUR API KEY]`。

最后，我们导出一个函数，该函数调用 baseURL 中 API 端点上的 get。

### 显示项目卡片

我们将在项目视图中显示我们项目的卡片。在您的`Projects.vue`模板中:

```
<template>
    <div>
        <section class="hero is-medium is-primary is-bold">
            <div class="hero-body">
                <div class="container">
                    <h1 class="title is-2">
                        Projects that I have built
                    </h1>
                </div>
            </div>
        </section>
        <section class="section">
            <div class="container is-fluid">
                <div class="columns is-multiline">
                        <div class="column is-one-third" v-for="project in projects">
                            <post-card v-bind="project"></post-card>
                        </div>              
                </div>
            </div>
        </section>
    </div> </template> 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的是`v-for="project in projects"`，我们为每个项目创建了一个`post-card`的实例，并通过`v-bind`传递项目细节。

模板的脚本部分如下所示:

```
<script>
    import ProjectsService from '@/services/ProjectsService'
    import PostCard from '@/components/PostCard'
    export default {
        name: "projects",
        components: {
            PostCard
        },
        data() {
            return{
                airtableResponse: []
            }
        },
        mounted: function () {
            let self = this
            async function getProjects() {
                try{
                    const response = await ProjectsService.getProjects()
                    console.log(response)
                    self.airtableResponse = response.data.records

                }catch(err){
                    console.log(err)
                }
                }
              getProjects()         
        },
        computed: {
            projects(){
                let self = this
                let projectList = []
                for (var i = 0; i < self.airtableResponse.length; i++) {
                    if (self.airtableResponse[i].fields.Published){
                        let project = {
                            title: self.airtableResponse[i].fields.Title,
                            date: self.airtableResponse[i].fields["Date Published"],
                            snippet: self.airtableResponse[i].fields.Excerpt,
                            image: self.airtableResponse[i].fields.Image[0].url,
                            slug: self.airtableResponse[i].fields.slug
                        }
                        projectList.push(project)
                    }
                }
                return projectList
            }
        }
    };
</script> 
```

Enter fullscreen mode Exit fullscreen mode

从上往下看，事情是这样的:

*   导入我们之前创建的 ProjectsService 和明信片
*   设置一个变量来保存来自 air 表的响应
*   当组件安装好后，调用我们在服务中设置的端点。
*   将我们需要的响应部分放在 airtableResponse 变量中。
*   用一个名为“Projects”的数组创建 computed property，用一个对象表示 Airtable 响应中的每个已发布项目和我们需要的每个卡片的变量。

如果一切顺利，您应该能够加载`/projects`并在表格中看到您在 Airtable 中创建的每个项目

## 克里特项目视图

还记得我们路由器设置中的这段代码吗？

```
{
 path: "/project/:slug",
 name: "project",
 component: Project
} 
```

Enter fullscreen mode Exit fullscreen mode

它将使我们可以访问我们的项目组件中的 slug，并将其传递到我们的项目服务中，这样我们就可以使用该 slug Airtable 检索该项目的所有信息。

让我们在`ProjectsService.js` :
中添加一个调用

```
getProject(slug) {
  return Axios.get("?filterByFormula={Slug}='" + slug + "'")
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里使用 Airtable 的 API 的特性来搜索包含该 slug 的帖子并返回它。

现在让我们创建我们的项目视图模板:

```
<template>
    <div>
        <section class="hero is-medium is-primary is-bold">
            <div class="hero-body">
                <div class="container">
                    <h1 class="title is-2">
                        {{project.title}}
                    </h1>
                    <h2 class="subtitle is-4">
                        {{project.snippet}}
                    </h2>
                </div>
            </div>
        </section>
        <section class="section">
            <div class="container is-fluid">
                <div class="columns">
                    <div class="column is-two-thirds">
                        <vue-simple-markdown :source="project.body"></vue-simple-markdown>
                    </div>
                    <div class="column is-one-third">
                        <div class="columns is-multiline">
                            <div class="column is-full" v-for="image in project.images">
                                <img :src="image.url"/>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </section>
    </div> </template> 
```

Enter fullscreen mode Exit fullscreen mode

这个模板使用了我们之前安装的 VueSimpleMarkdown 插件。这意味着您可以在 Airtable 的 body 字段中使用 markdown 来设计您的项目。我们在左边的一列中显示尸体，在右边显示该物品的所有图像。

最后，项目组件的脚本部分:

```
<script>
    import ProjectsService from '@/services/ProjectsService'
    import PostCard from '@/components/PostCard'
    export default {
        name: "project",
        components: {
            PostCard
        },
        data() {
            return{
                airtableResponse: []
            }
        },
        mounted: function () {
            let self = this
            console.log("here 1")
            async function getProject() {
                try{
                    const response = await ProjectsService.getProject(self.$route.params.slug)
                    console.log(response)
                    self.airtableResponse = response.data.records

                }catch(err){
                    console.log(err)
                }
            }
            getProject()            
        },
        computed: {
            project(){
                let self = this
                if (self.airtableResponse[0]){
                    let thisProject = {
                        title: self.airtableResponse[0].fields.Title,
                        snippet: self.airtableResponse[0].fields.Excerpt,
                        images: self.airtableResponse[0].fields.Image,
                        body: self.airtableResponse[0].fields.Body
                    }
                    return thisProject
                }
            }
        }
    };
</script> 
```

Enter fullscreen mode Exit fullscreen mode

类似于 Projects 视图，但是这次我们将 slug 传递到`getProject`调用中。如果子弹是独一无二的，我们只能得到一个回应。

前往/projects/[your-slug]观看您的项目实况！

## 结论

咻。太多了！现在我们完成了，我们有了一个简单的 CMS，它在一个用 Vue 构建的站点上显示实时数据，并带有布尔玛风格。相当酷！

我将会尝试一些样式，添加一些新的特性，并清理一些东西，所以请关注 [hellomark.dev](https://hellomark.dev/) 看看接下来会发生什么！

让我知道你对本教程的看法和你的任何问题！