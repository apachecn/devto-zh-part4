# 类星体-商店模型结合模式-第一部分

> 原文：<https://dev.to/tobymosque/quasar-store-model-bind-pattern-part-i-lkh>

# 1。问题是

当你开始使用`Quasar`和`SSR`模式时，一个常见的问题是你被迫使用`preFetch`和每个`Page`的专用商店模块。因此，即使像这样的单个 SFC 也会变得有些冗长和乏味。

**src/pages/Person/index . vue .***

```
<template>
  <div class="row q-col-gutter-sm">
    <q-input class="col col-6" label="Name" v-model="name" />
    <q-input class="col col-6" label="Surname" v-model="surname" />
  </div>
</template>
<script src="./Index.vue.js"></script> 
```

```
export default {
  data () {
    return {
      name: '',
      surname: ''
    }
  },
  mounted () {
    let { id } = this.$route.params
    // query the person somewhere
  }
} 
```

会变得冗长:

**src/store/person . js**T2】

```
export default {
  namespaced: true,
  state () {
    return {
      name: '',
      surname: ''
    }
  },
  mutations: {
    name (state, value) { state.name = value },
    surname (state, value) { state.surname = value }
  },
  actions: {
    initialize ({ context }) {
      // query the person somewhere
    }
  }
} 
```

**src/pages/Person/index . vue .***

```
<template>
  <div class="row q-col-gutter-sm">
    <q-input class="col col-6" label="Name" v-model="name" />
    <q-input class="col col-6" label="Surname" v-model="surname" />
  </div>
</template>
<script src="./Index.vue.js"></script> 
```

```
import Module from 'src/store/person'
import { mapActions } from 'vuex'
const moduleName = 'person'
export default {
  preFetch ({ store, currentRoute }) {
    store.registerModule(moduleName, Module)
    return store.dispatch(`${moduleName}/initialize`, currentRoute.params.id)
  },
  mounted () {
    if (!this.$store.state[moduleName]) {
      this.$store.registerModule(moduleName, Module, { preserveState: true })
      this.$store.dispatch(`${moduleName}/initialize`, this.$route.params.id)
    }
  },
  destroyed () {
    this.$store.unregisterModule(moduleName)
  },
  computed: {
    name: {
      get () { return this.$store.state[moduleName].name },
      set (value) { this.$store.commit(`${moduleName}/name`, value) }
    },
    surname: {
      get () { return this.$store.state[moduleName].name },
      set (value) { this.$store.commit(`${moduleName}/name`, value) }
    }
  }
} 
```

因此，如果您想要管理您的字段(重命名、创建或删除)，而不是编辑您的数据挂钩，您将需要编辑状态、变异名称、变异本身、计算(挂钩)名称、计算获取和计算集。

# 2。公用事业带

我们需要创建一些实用方法来映射状态、突变和计算属性。

**src/utils/mapper.js**

```
import Vue from 'vue'

export function createMutations (Model) {
  const keys = Object.keys(new Model())
  const mutations = keys.reduce((mutations, key) => {
    mutations[key] = function (state, value) {
      Vue.set(state, key, value)
    }
    return mutations
  }, {})
  return mutations
}

export const mapState = function (module, properties) {
  var props = {}
  if (Array.isArray(properties)) {
    properties.forEach(property => {
      props[property] = {
        get () {
          return this.$store.state[module][property]
        },
        set (value) {
          this.$store.commit(`${module}/${property}`, value)
        }
      }
    })
  } else {
    Object.keys(properties).forEach(key => {
      var property = properties[key]
      props[key] = {
        get () { return this.$store.state[module][property] },
        set (value) { this.$store.commit(`${module}/${property}`, value) }
      }
    })
  }
  return props
}

export const mapGetters = function (module, properties) {
  var props = {}
  if (Array.isArray(properties)) {
    properties.forEach(property => {
      props[property] = {
        get () {
          return this.$store.getters[`${module}/${property}`]
        },
        set (value) {
          this.$store.commit(`${module}/${property}`, value)
        }
      }
    })
  } else {
    Object.keys(properties).forEach(key => {
      var property = properties[key]
      props[key] = {
        get () { return this.$store.getters[`${module}/${property}`] },
        set (value) { this.$store.commit(`${module}/${property}`, value) }
      }
    })
  }
  return props
} 
```

`createMutations`将把一个对象的字段映射到一个像存储突变那样结构化的对象。

`mapState`有一个类似于原始`vuex's mapState`的签名，但是它也将把`state`和`mutation`都映射到一个`computed property`。

`mapGetters`有一个类似于原始`vuex's mapGetters`的签名，但是它也将把`getter`和`mutation`都映射到一个`computed property`。

# 3。建议的解决方案-存储模型绑定模式

现在，我们不再直接在`store's state`中定义我们的`data structure`，而是创建一个`Class Model`来保存它。

**src/models/person.js**

```
export default class Person {
  name = ''
  surname = ''
} 
```

现在，让我们更新我们的商店。：

**src/store/person . js**T2】

```
import Model from 'src/store/person'
export default {
  namespaced: true,
  state () {
    return new Model()
  },
  mutations: {
    ...createMutations(Model)
  },
  actions: {
    initialize ({ context }) {
      // query the person somewhere
    }
  }
} 
```

如果你进一步观察上面的`store`，你会注意到这是非常普通的，所以我们现在可以用它来搭建我们将来要创建的商店。

现在，我们需要更新页面本身:

**src/pages/Person/index . vue . js**

```
import Module from 'src/store/person'
import Model from 'src/models/person'
import { mapState } from 'src/utils/mapper'

const moduleName = 'person'
const keys = Object.keys(new Model())

export default {
  preFetch ({ store, currentRoute }) {
    store.registerModule(moduleName, Module)
    return store.dispatch(`${moduleName}/initialize`, currentRoute.params.id)
  },
  mounted () {
    if (!this.$store.state[moduleName]) {
      this.$store.registerModule(moduleName, Module, { preserveState: true })
      this.$store.dispatch(`${moduleName}/initialize`, this.$route.params.id)
    }
  },
  destroyed () {
    this.$store.unregisterModule(moduleName)
  },
  computed: {
    ...mapState(moduleName, keys)
  }
} 
```

与之前的版本相比，`Page`看起来仍然很冗长，但是和`store`一样，`Page`非常通用，所以我们可以用它来搭建其他的`Pages`。

现在，如果我们需要编辑我们的数据结构，我们不需要在两个文件中进行多次编辑。我们只需要编辑`Class Model`。

在下一篇文章中，我们将讨论一个非常特殊的情况，`collections`，又名`arrays`。