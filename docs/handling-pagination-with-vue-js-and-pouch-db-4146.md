# 用 Vue JS 和 Pouch DB 处理分页

> 原文：<https://dev.to/obbap/handling-pagination-with-vue-js-and-pouch-db-4146>

# [t1【导言】](#introduction-%EF%B8%8F)

当来自服务器的响应仅来自一个请求时，我们必须在前端分页，这就更容易处理了。这是因为我们的所有数据都在客户端，所以我们只在特定页面上显示一系列索引。因此，如果响应是一个 400 项的数组，我们可以每页显示 20 项。但是如果由于某种原因，来自服务器的响应不能出现在一个请求中，我们将需要使用分页或无限滚动。这取决于用例。那么分页的性能问题是什么呢？我们如何解决这些问题？让我们开始吧。

# 问题🍉

假设我们有一个端点，它返回一个负载，比如:

```
{
    totalNumber: 1000,
    data: [0,....,20],
    pageNumber: 1
} 
```

从对象中，我们可以看到我们总共有多少数据，以及我们所在的特定页面，因此我们知道我们拥有的页码范围。如果每个响应返回一个包含 20 个元素的数组，那么我们有(1000 / 20)个页面。

| one | Two | three | ... | Fifty |

对于分页，如果我们单击“1”，它会获取前二十个元素。第 2 页获取接下来的 20 个元素，回到第 1 页再次获取前 20 个元素。所以来来回回真的会很慢，而且非常麻烦。

# PouchDB:一个解决方案

为了处理客户端(浏览器)上的存储，我们有本地存储、会话存储、索引数据库、cookies 等。[小袋 DB](https://pouchdb.com/) 实际上是在引擎盖下使用 IndexedDB。它主要用于离线应用程序，与实时数据库自动同步，很可能是 Couch DB。

我们将使用 Vue JS 来解释这是如何工作的。首先，我们将安装所需的软件包。

```
npm i --save vuejs-paginate pouchdb-browser pouch-vue pouchdb-find pouchdb-live-find axios

vue create pouchdb-app 
```

在 main.js 文件中，我们将安装并初始化数据库。

```
// main.js 
import Vue from 'vue';
import Paginate from 'vuejs-paginate';
import PouchDB from 'pouchdb-browser';
import * as pouchVue from 'pouch-vue';

PouchDB.plugin(require('pouchdb-find'));
PouchDB.plugin(require('pouchdb-live-find'));

Vue.component('paginate', Paginate);

Vue.use(pouchVue, {
    pouch: PouchDB, 
    defaultDB: 'users_database' //You can give it any name 
});

// ............. 
```

转到 vue 文件，在那里我们显示了所有的用户。假设我们的端点的响应是这样的。

```
{
    total:'',
    pageNumber:'',
    users:[
        {
            firstname: '',
            lastname:''
        },
        {
            firstname: '',
            lastname:''
        },
    ]
} 
```

我们可以在 vue 文件上显示我们的第一批用户。

```
<!-- users.vue -->
<div>
    <!--
        getUsers: {
            users: [],
            total: '' ,
            pageNumber: '' 
        }
    -->
    <v-data-table
        :headers="headers"
        :items="getUsers.users"  
        v-if="getUsers.users"
        hide-actions
    >
        <template slot="items" slot-scope="props">
        <td>{{ props.item.firstname}} {{props.item.lastname}}</td>
        </template>
    </v-data-table>
    <paginate
            :page-count="pageCount"
            :click-handler="fetchNext"
            :prev-text="'Prev'"
            :next-text="'Next'"
        />
</div> 
```

首先，有一个表显示所有用户的名字和姓氏。此表来自[年](https://vuetifyjs.com/en/)月。

其次，我们有 ***分页*** 组件，它有 ***页面计数*** 属性，这显示了我们拥有的分页页面的数量。如果响应总共有 400 个用户，并且我们在每个请求中收到 20 个用户，那么页面计数将是(400 / 20) 20。***click-handler***prop，接受一个当任何页面被点击时运行的函数。 ***上一页*** 和 ***下一页*** 道具只接受上一页和下一页要显示的文字。

[![The flow](img/dad2ec527403db277288d371d02ee193.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fj2gEb-i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/pbaba/image/upload/v1561991050/RBE8Xtr5PTvu_1_jnf18u.jpg)

使用上图，我们将创建一个 ***getAllUsers*** 动作，该动作获取第一批用户，将他们提交到 state，然后将他们存储在 PouchDB 中。我们也可以使用**T5 从我们的单个文件组件(SFC)访问 Pouch DB。$荷包** 。这将在 ***users.vue*** 文件中的 ***created()*** 生命周期钩子上完成。

```
//....
<script>
    //...users.vue
    import {mapActions, mapGetters} from 'vuex'; //Using vuex for our store
    export default {
        data(){
            return {
                headers: [
                    {
                        text: 'Name',
                        value: 'firstname',
                        align: 'left'
                    }
                ]
            }
        },
        created(){
            this.getAllUsers({
                pageNumber: 1, // Fetch first page
                pouch: this.$pouch //pass pouch db reference to action
            })
        },
        methods:{
            ...mapActions(['getAllUsers'])
        },
        computed:{
            ...mapGetters(['getUsers'])
        }
    }
</script> 
```

现在我们将编写 getAllUsers 动作，以及完成我们的存储所需的其他元素。

```
//store.js
import Vue from 'vue';

//mutation type
const UPDATE_ALL_USERS = 'UPDATE_ALL_USERS';

//state
const state = {
    allUsers: null,
};

const getters = {
    getUsers: state => state.allUsers
}

const actions = {
    getAllUsers({commit}, data){
        // retrieve the pouch db reference and page number we just sent
        const {pageNumber, pouch} = data;

        //Using axios
        Vue.axios.get(`allUsers/?page=${pageNumber}`)
            .then(res =>{
                console.log('data retrieved');
                /*
                    res: {
                        users:
                        total:
                        pageNumber:
                    }
                */
                // store data in pouch db
                pouch.put({
                    _id: `${pageNumber}`,
                    users: res.data.users
                })
                .then(()=>{
                    console.log('your data has been stored');
                })
                .catch(e => console.log(e))
            });
        commit(UPDATE_ALL_USERS, res.data);
    }
}

const mutations = {
    [UPDATE_ALL_USERS](state, data){
        state.allUsers = data;
    }
} 
```

因此，在它从服务器获取特定的一批用户后，它将它缓存在 pouch db 中，用 ***pageNumber*** 变量作为 id，用户作为实际数据。

最后，我们需要编写 fetchNext 函数，它在每次特定页面被点击时发生。因此，如果页面被点击，我们将检查我们的缓存，如果它在那里，显示结果，如果没有调用 getAllUsers 操作，它将从服务器获取，然后缓存。

```
<!--users.vue-->
<script>
//.... This is a continuation of the users.vue file above
    methods: {
        fetchNext(event){
            // event is the particular number clicked
            this.$pouch.get(`${event}`)
                .then((doc) => {
                    //if it is found, store in the cachedUsers variable
                    this.cachedUsers = doc.users;
                })
                .catch(e => {
                    //if that page wasn't found, run the getAllUsers function
                    if(e.name === 'not_found'){
                        return this.getAllUsers({
                            pageNumber: event,
                            pouch: this.$pouch
                        })
                    }
                })
        }
    }
</script> 
```

我们需要 ***cachedUsers*** 变量，因为 ***getUsers*** 变量只是一个 getter，如果在我们的缓存中找到用户，我们需要能够将我们的变量设置为该值。

```
<script>
    //.....
    computed:{
        ...mapGetters(['getUsers']),
        cachedUsers: {
            get(){
                // return the getter
                return this.getUsers
            },
            set(value){
                //use the setter
                this.getUsers.users = value;
            }
        },
        pageCount(){
            // get the total number of users and the amount of users per page to get the number of pages.
            const {total, users} = this.cachedUsers;
            //fix to one decimal space
            const numberOfPages = (Number(total) / users.length).toFixed(1);
            //if there is a decimal value, increase the number of pages by 1.
            return Number(numberOfPages.split('.')[1]) > 0
                ? Math.round(Number(numberOfPages)) + 1
                : Math.round(Number(numberOfPages));
        }
    }
</script> 
```

# [t1【结论】](#conclusion)

这只是展示了我们可以在前端处理分页的方法之一，它也确保了事情的速度。最近帮了我一个忙，什么框架都可以写。理解这个概念很重要，我希望你做到了。感谢阅读！

这篇文章最初发表在我的博客上