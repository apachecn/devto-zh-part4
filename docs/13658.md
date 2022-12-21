# 第 025 集-整合身份服务器 4 -第 5 部分-前端-ASP.NET 核心:从 0 到过度杀戮

> 原文：<https://dev.to/joaofbantunes/episode-025-integrating-identityserver4-part-5-frontend-asp-net-core-from-0-to-overkill-155h>

在这一集里，我们来看看我们的前端单页面应用程序，以及为处理用户认证所做的更改。我们还将研究一种处理向 BFF 发出的请求中的 CSRF 令牌的方法。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/V0ukOYOaYkE](https://www.youtube.com/embed/V0ukOYOaYkE)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

最后，让我们总结一下应用程序中身份验证/授权的集成。

我们从给 auth 服务添加 IdentityServer4 开始([第 022 集](https://blog.codingmilitia.com/2019/06/13/aspnet-022-from-zero-to-overkill-integrating-identityserver4-part2-auth-service))。然后准备了群组管理 API 要求访问令牌(《T2》第 023 集)。在那之后，我们继续设置 web 前端的 BFF 来验证用户，使用 OpenID Connect 与 auth 服务集成，并在对群组管理服务的每个请求中包含用户的访问令牌([第 024 集](https://blog.codingmilitia.com/2019/06/22/aspnet-024-from-zero-to-overkill-integrating-identityserver4-part4-back-for-front))。

在这一集里，我们将通过调整前端单页面应用程序来处理经过身份验证和未经身份验证的用户，最终完成所有的集成——至少是大部分的集成，因为我们以后可能会重新讨论一些主题。

## 处理认证和未认证用户

在上一节中，我们向 BFF 添加了两个端点，SPA 可以用它们来处理身份验证:一个用来获取已验证用户的信息(如果她已通过身份验证)，另一个用来登录(重定向到 auth 服务)。现在，我们将让 SPA 与它们一起工作，首先获取当前用户的信息(如果已经过身份验证)。

### 获取当前用户的信息

为了获得当前用户的信息，我们需要向 BFF 发出请求。我们将以类似于在组端点的情况下所做的方式来实现它:创建一个接口和服务实现来访问端点，然后创建所需的位来与 [Vuex](https://vuex.vuejs.org/) 一起工作，将用户信息存储在客户端应用程序状态中，使用动作、突变和 getters 来处理它，就像我们在前面关于前端 SPA 的章节中看到的那样。

#### 向 web API 发出请求

让我们从发出请求的服务开始。在`src\data`文件夹中，我们创建一个名为`auth`的新文件夹。在那里我们添加了一个额外的文件夹，名为`models`，我们将在那里放置 BFF 响应的表示。

现在让我们开始创建类和接口，从`AuthInfoModel`开始。

`src\data\auth\models\auth-info-model.ts`

```
export interface AuthInfoModel {
    name: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么，现在我们只返回用户名，就像我们在上一集实现 BFF 位时看到的那样。

`src\data\auth\auth-endpont.ts`

```
import { AuthInfoModel } from './models/auth-info-model';

export interface AuthEndpoint {
    getAuthInfo(): Promise<AuthInfoModel | null>;
} 
```

Enter fullscreen mode Exit fullscreen mode

此时我们只有一个端点提供 auth info，所以我们在接口中有一个方法来获取该信息。

`src\data\auth\auth-service.ts`

```
import axios from 'axios';
import { AuthInfoModel } from './models/auth-info-model';
import { AuthEndpoint } from './auth-endpoint';
import { BaseService } from '../base-service';

export class AuthService extends BaseService implements AuthEndpoint {
    private readonly baseUrl: string = '/api/auth';

    public async getAuthInfo(): Promise<AuthInfoModel | null> {
        try {
            const response = await axios.get(`${this.baseUrl}/info`);
            return response.data;
        } catch (error) {
            // if we get a 401, the user isn't logged in
            if (error.response.status === 401) {
                return null;
            }
            throw error;
        }

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在`AuthService`类中，是我们实现对 BFF 的请求的地方。与我们最初开始实施 SPA 时看到的没有太大不同，只是有几件事情值得注意:

*   由于我们在 BFF 中的控制器被配置为当用户没有登录时用 401 来响应，我们正在处理这个问题，所以我们可以返回`null`并且这个方法的调用者将假设用户没有登录(我们马上会看到这一点)。
*   该类扩展了另一个名为`BaseService`的类。这是为了保护 CSRF 而创建的，我们会在这篇文章的相应部分看到更多。

#### 将请求与存储整合

准备好向 web API 发出请求的服务后，我们可以将它集成到我们的 Vuex 商店中，就像我们集成组管理 API 一样。我们将创建一个新的商店模块，在`src\store\modules`中创建一个名为`auth`的新文件夹。在那里我们将添加一些新的文件:`state.ts`、`mutations.ts`、`actions.ts`、`getters.ts`和`index.ts`。

让我们从`state.ts`中的状态定义开始。在那里，我们将存储当前用户的用户名和一些附加数据:用户是否登录以及其信息是否已经被加载。

`src\store\modules\auth\state.ts`

```
export interface AuthState {
    loggedIn: boolean;
    loaded: boolean;
    username: string | null;
} 
```

Enter fullscreen mode Exit fullscreen mode

`loggedIn`将从服务以 401 响应的事实中推断出来。`loaded`只是让应用程序知道它是否已经发出了请求，所以它不需要再次发出请求。

现在让我们看看突变，它将被用来改变我们上面定义的状态。

`src\store\modules\auth\mutations.ts`

```
import { AuthState } from './state';
import { MutationTree } from 'vuex';
import { AuthInfoModel } from '@/data/auth/models/auth-info-model';

export const mutations: MutationTree<AuthState> = {
    setUser(state: AuthState, authInfo: AuthInfoModel): void {
        state.loggedIn = true;
        state.loaded = true;
        state.username = authInfo.name;
    },
    setAnonymousUser(state: AuthState): void {
        state.loggedIn = false;
        state.loaded = true;
        state.username = null;
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这些突变将被请求用户信息的操作使用，因此根据响应它将做不同的事情。在这两种情况下，随着请求的发出和信息的初始化，`loaded`标志被设置为真。然后`loggedIn`和`username`将依赖于服务器的响应，相应地设置信息。

对于动作，我们创建一个名为`loadInfo`的动作，它使用我们之前创建的服务发出请求，最后调用正确的变异。

`src\store\modules\auth\actions.ts`

```
import { ActionTree } from 'vuex';
import { RootState } from '@/store/state';
import { AuthEndpoint } from '@/data/auth/auth-endpoint';
import { AuthState } from './state';

export const types = {
    LOAD_INFO: 'auth/loadInfo'
};

export const makeActions = (authEndpoint: AuthEndpoint): ActionTree<AuthState, RootState> => {
    return {
        async loadInfo({ commit }): Promise<void> {
            const authInfo = await authEndpoint.getAuthInfo();
            if (!!authInfo) {
                commit('setUser', authInfo);
            } else {
                commit('setAnonymousUser');
            }
        }
    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

快速喊出创建 actions 对象的工厂方法的用法，这样我们就可以注入`AuthEndpoint`的实现。

现在我将删除`getters.ts`和`index.ts`文件的源代码，因为它们大同小异，不值得大惊小怪。

`src\store\modules\auth\getters.ts`

```
import { AuthState } from './state';
import { GetterTree } from 'vuex';
import { RootState } from '@/store/state';

export const types = {
    INFO: 'auth/info'
};

export const getters: GetterTree<AuthState, RootState> = {
    info(state: AuthState): AuthState {
        return { ...state };
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

`src\store\modules\auth\index.ts`

```
import { Module } from 'vuex';
import { RootState } from '@/store/state';
import { makeActions } from './actions';
import { mutations } from './mutations';
import { AuthService } from '@/data/auth/auth-service';
import { AuthState } from './state';
import { getters } from './getters';

export const auth: Module<AuthState, RootState> = {
    namespaced: true,
    actions: makeActions(new AuthService()),
    mutations,
    getters,
    state: {
        loggedIn: false,
        loaded: false,
        username: null
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

同样，在`src\store\index.ts`中我们需要添加新的模块:

`src\store\index.ts`

```
// ...
import { auth } from './modules/auth';

const options: StoreOptions<RootState> = {
  state: {},
  modules: {
    auth,
    groups
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 加载用户信息，限制路线访问

我们已经准备好了服务和存储，但是还缺少一些非常重要的东西——实际上是完成请求和获取信息。我们可以在任何组件上这样做，例如在主组件(`App.vue`)中，但是这样做的一个好地方是在路由器配置中。这样，我们可以确保在加载路线时加载信息。此外，我们可以做一些调整，使一些路线只能由认证用户访问-隐藏用户界面的链接是不够的😉。

让我们来看看路由器配置代码并浏览一遍。
T2`src\router.ts`

```
// ...
import store from './store';
import * as authActions from './store/modules/auth/actions';
import * as authGetters from './store/modules/auth/getters';

Vue.use(Router);

const router = new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    // ...
    {
      path: '/groups',
      name: 'groups',
      component: () => import('./views/Groups.vue'),
      meta: { requiresAuthentication: true }
    },
    // ...
  ],
});

router.beforeEach(async (to, from, next) => {
  if (!store.getters[authGetters.types.INFO].loaded) {
    await store.dispatch(authActions.types.LOAD_INFO);
  }
  if (to.matched.some(record => record.meta.requiresAuthentication)
    && !store.getters[authGetters.types.INFO].loggedIn) {
    window.location.href = `/api/auth/login?returnUrl=${window.location.href}`;
  } else {
    next();
  }
});

export default router; 
```

Enter fullscreen mode Exit fullscreen mode

我们在上面看到的很多东西与我们最初创建 SPA 时看到的一样，但是我们添加了一些新东西。

让我们从调用`router.beforeEach`开始。通过这个调用，我们配置了一些在访问任何路线之前运行的代码——这被称为导航保护。作为参数，我们得到我们要导航到的路线(`to`)、我们从哪里来的路线(`from`)和一个`next`函数，如果我们想让导航继续进行，就调用这个函数。

在本例中，我们使用 guard 做两件事:确保用户信息被加载，然后确保用户可以访问该路线。

为了确保用户信息被加载，守卫使用 getter 来检查`loaded`标志。如果是`false`,它会分派动作来加载所需的信息。这可以确保在访问页面之前，加载所需的信息。在这种情况下，它只会发生一次，但我们可以使用这种方法来确保加载其他信息，这取决于我们正在访问的路线。为了使用它而不是拦截所有的路由导航，在路由配置中，我们可以使用`beforeEnter`属性添加一个守卫。我们可能会在下一集用到它，但是现在我们只需要`router.beforeEach`。

在确保我们拥有做出路由访问决策的数据后，我们可以添加该逻辑。将来，我们可能需要知道的不仅仅是用户是否登录(例如，一个管理员用户可以访问不同的路由)，但是现在已经足够了。

为了让授权逻辑工作，在`groups`路由的配置中，我们用一个标志来设置`meta`属性，该标志指示路由是否需要经过身份验证的用户。这个`requiresAuthentication`不是路由器内置的东西，我们可以简单地在`meta`属性中设置我们想要的任何东西。

在 navigation guard 中，我们可以使用前面提到的标志来检查`to`路线是否需要验证，如果需要，我们将检查用户信息。如果用户登录了，我们调用`next`继续，如果没有，我们重定向到 BFF 登录端点。

### 使用户界面适应认证/未认证用户

好了，到目前为止，我们得到了用户的信息，确保它在页面出现之前被加载，并且不允许未经验证的用户访问需要它的页面。在这方面，剩下要做的是调整应用程序顶部的导航菜单，根据用户状态显示正确的链接。这个菜单是`App.vue`组件的一部分，以便我们在需要的地方做一些修改。

让我们直接进入代码。

`src\App.vue`

```
<template>
  <div id="app">
    <template v-if="isAuthInfoLoaded">
      <div id="nav">
        <router-link to="/">Home</router-link>
        | <router-link to="/about">About</router-link>
        <template v-if="isUserLoggedIn">
        | <router-link to="/groups">Groups</router-link>
        </template>
        <template v-else>
        | <a v-bind:href="loginUrl">Login</a>
        </template>
      </div>
      <router-view/>
    </template>
    <template v-else>
      Loading...
    </template>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import { State, namespace } from 'vuex-class';
import { AuthInfoViewModel } from './models/auth-info-view-model';

const authModule = namespace('auth');

@Component
export default class App extends Vue {
  @authModule.Getter('info') private authInfo!: AuthInfoViewModel;

  public get isUserLoggedIn(): boolean {
    return !!this.authInfo ? this.authInfo.loggedIn : false;
  }

  public get isAuthInfoLoaded(): boolean {
    return !!this.authInfo ? this.authInfo.loaded : false;
  }

  public get loginUrl(): string {
    return `/api/auth/login?returnUrl=${window.location.href}`;
  }
}
</script>

<style lang="scss">
/* css didn't change*/
</style> 
```

Enter fullscreen mode Exit fullscreen mode

这里与我们开始使用 Vue.js 时已经看到的没有什么真正的不同——我们使用 getter 访问商店的 auth 模块，基于它生成逻辑，并且在标记中有一个`template`显示访问组页面的链接，或者如果用户没有通过身份验证，还有一个登录链接。此外，当我们没有从用户信息端点获得响应时，会显示一些加载信息。

## 支持跨站请求伪造令牌

因此，大部分工作已经完成，将用户信息集成到应用程序中。只缺少一点，那就是保护应用程序免受跨站点请求伪造攻击。这是一个快速的任务，因为我们在 BFF 中已经准备好了所有的东西，另外在客户端, [axios](https://github.com/axios/axios) 将完成大部分的工作。

如果您还记得上一集，在 BFF 中，我们希望得到一个带有防伪标记的头，以确保是我们的应用程序代码发出了请求。当我们在 Razor Pages 中这样做时，它是自动的，但现在我们在 SPA 环境中，我们有一些工作要做。为此，在 BFF 中，我们设置了一个 cookie，客户端应用程序可以访问这个 cookie，并将其作为头部添加到请求中。

为此，在整个过程中简化服务实现，我们可以创建一个`BaseService`类，我们可以在端点实现中继承它。

`src\data\base-service.ts`

```
import { AxiosRequestConfig } from 'axios';

export class BaseService {
    protected getAxiosConfig(): AxiosRequestConfig {
        return { xsrfHeaderName: 'X-XSRF-TOKEN', xsrfCookieName: 'XSRF-TOKEN'};
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是创建了一个`AxiosRequestConfig`，其中我们告诉 axios 它必须添加令牌的头的名称，以及它可以从中获取所述令牌的 cookie 的名称。

现在我们可以转到`GroupService`，从`BaseService`继承并使用那个配置。

`src\data\groups\groups-service.ts`

```
// ...

export class GroupsService extends BaseService implements GroupsEndpoint {
    private readonly baseUrl: string = '/api/groups';

    public async getAll(): Promise<GroupModel[]> {
        const response = await axios.get(this.baseUrl);
        return response.data;
    }

    public async getById(id: number): Promise<GroupModel> {
        const response = await axios.get(`${this.baseUrl}/${id}`);
        return response.data;
    }

    public async add(group: GroupModel): Promise<GroupModel> {
        const response = await axios.post(this.baseUrl, group, this.getAxiosConfig());
        return response.data;
    }

    public async update(group: GroupModel): Promise<GroupModel> {
        const response = await axios.put(`${this.baseUrl}/${group.id}`, group, this.getAxiosConfig());
        return response.data;
    }

    public async remove(id: number): Promise<void> {
        const response = await axios.delete(`${this.baseUrl}/${id}`, this.getAxiosConfig());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，在所有非 GET 请求中，我们传递配置，然后 axios 会处理剩下的，我们这边不需要额外的逻辑。

## 其他

这是关于在整个 PlayBall 应用程序中集成身份验证和授权的子系列的总结。

在这一集里，我们将对这种身份验证集成进行最后的润色，调整客户端 Vue.js 应用程序以使用用户信息端点，根据用户的状态以不同的方式呈现自己，并确保 CSRF 令牌包含在 API 请求中。

我们将来肯定会再次讨论这个子系列的一些主题，但是现在看来，这是一个很好的概述。

帖子中的链接:

*   [Vuex](https://vuex.vuejs.org/)
*   [跨站点请求伪造(CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))
*   [轴](https://github.com/axios/axios)
*   [第 022 集-整合身份服务器 4 -第二部-认证服务-ASP.NET 核心:从 0 到矫枉过正](https://blog.codingmilitia.com/2019/06/13/aspnet-022-from-zero-to-overkill-integrating-identityserver4-part2-auth-service)
*   [第 023 集-整合身份服务器 4 -第 3 部分-API-ASP.NET 核心:从 0 到过度杀伤](https://blog.codingmilitia.com/2019/06/16/aspnet-023-from-zero-to-overkill-integrating-identityserver4-part3-api)
*   [第 024 集——整合身份服务器 4——第四部——为正面反击——ASP.NET 核心:从 0 到过度杀伤](https://blog.codingmilitia.com/2019/06/22/aspnet-024-from-zero-to-overkill-integrating-identityserver4-part4-back-for-front)

这个子系列帖子的源代码分散在[“编码民兵:ASP.NET 核心-从 0 到过度杀戮”组织](https://github.com/AspNetCoreFromZeroToOverkill)的一堆仓库中，标记为`episode021`。

*   [认证](https://github.com/AspNetCoreFromZeroToOverkill/Auth/tree/episode021)
*   [群组管理](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/tree/episode021)
*   [web 前端](https://github.com/AspNetCoreFromZeroToOverkill/WebFrontend/tree/episode021)

感谢分享和反馈！

谢谢你的来访，西阿兹！