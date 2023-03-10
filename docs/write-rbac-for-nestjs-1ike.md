# 为 NestJS 写 RBAC

> 原文：<https://dev.to/sergey_telpuk/write-rbac-for-nestjs-1ike>

你好。

最近，我遇到了为 NestJS 找到一个好的基于角色的访问控制(RBAC)解决方案的困难，所以我决定自己做一个:)。
完成的仓库是[这里是](https://github.com/sergey-telpuk/nestjs-rbac)

## 快速启动

要使用 RBAC，需要实现`IStorageRbac`

```
export interface IStorageRbac {
  roles: string[];
  permissions: object;
  grants: object;
  filters: { [key: string]: any | IFilterPermission };
} 
```

Enter fullscreen mode Exit fullscreen mode

### 举个例子:

```
export const RBACstorage: IStorageRbac = {
  roles: ['admin', 'user'],
  permissions: {
    permission1: ['create', 'update', 'delete'],
    permission2: ['create', 'update', 'delete'],
    permission3: ['filter1', 'filter2', RBAC_REQUEST_FILTER],
    permission4: ['create', 'update', 'delete'],
  },
  grants: {
    admin: [
      '&user',
      'permission1',
      'permission3',
    ],
    user: ['permission2', 'permission1@create', 'permission3@filter1'],
  },
  filters: {
    filter1: TestFilterOne,
    filter2: TestFilterTwo,
    [RBAC_REQUEST_FILTER]: RequestFilter,
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 存储由以下键组成:

`roles`:角色数组

`permissions`:内容动作的权限对象

`grants`:分配给角色权限的对象

`filters`:海关角色的对象

### 授予符号

`&`:通过另一个 grant 来扩展 grant，例如`admin`扩展`user` *(只支持一级继承)*

`@`:来自权限的特定动作，例如`permission1@update`

### 利用 RBAC 就像一个不可改变的储藏

```
import { Module } from '@nestjs/common';
import { RBAcModule } from 'nestjs-rbac';

@Module({
  imports: [
    RBAcModule.forRoot(RBAC),
  ],
  controllers: []
})
export class AppModule {} 
```

Enter fullscreen mode Exit fullscreen mode

### 利用 RBAC 像一个动态存储器

*有足够的实现 IDynamicStorageRbac 接口。*

```
import { Module } from '@nestjs/common';
import { RBAcModule } from 'nestjs-rbac';

@Module({
  imports: [
    RBAcModule.forDynamic(AsyncService),
  ],
  controllers: []
})
export class AppModule {}
// implement dynamic storage
import { IDynamicStorageRbac, IStorageRbac } from 'nestjs-rbac';
@Injectable()
export class AsyncService implements IDynamicStorageRbac {
  constructor(
    private readonly repository: AnyRepository
  ) {

  }
  async getRbac(): Promise<IStorageRbac> {
      return  await this.repository.getRbac(); //use any persistence storage for getting RBAC
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 用于路由器

```
import { RBAcPermissions, RBAcGuard } from 'nestjs-rbac';

@Controller()
export class RbacTestController {

  @RBAcPermissions('permission', 'permission@create')
  @UseGuards(
    AuthGuard, // need for using user into the request
    RBAcGuard,
  )
  @Get('/')
  async test1(): Promise<boolean> {
    return true;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用喜欢的服务

```
import { RbacService} from 'nestjs-rbac';

@Controller()
export class RbacTestController {

  constructor(
    private readonly rbac: RbacService
  ){}

  @Get('/')
  async test1(): Promise<boolean> {
    await this.rbac.getRole(role).can('permission', 'permission@create');
    return true;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用自定义过滤器

`filter`这是定制 RBAC 行为的绝佳机会。
创建`filter`需要实现`IFilterPermission`接口，这需要实现`can`方法，并与过滤器实现绑定一个关键过滤器，如下:

```
export const RBAC: IStorageRbac = {
  roles: ['role'],
  permissions: {
    permission1: ['filter1'],
  },
  grants: {
    role: [
      `permission1@filter1`
    ],
  },
  filters: {
    filter1: TestFilter,
  },
};  
//===================== implementing filter
import { IFilterPermission} from 'nestjs-rbac';

export class TestFilter implements IFilterPermission {

  can(params?: any[]): boolean {
    return params[0];
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

`ParamsFilter`用于将参数传递到特定过滤器的服务:

```
const filter = new ParamsFilter();
filter.setParam('filter1', some payload);

const res = await rbacService.getRole('admin', filter).can(
  'permission1@filter1',
); 
```

Enter fullscreen mode Exit fullscreen mode

此外，RBAC 有一个默认的过滤器`RBAC_REQUEST_FILTER`，它将`request`对象作为参数:

##### 举例:

```
//===================== filter
export class RequestFilter implements IFilterPermission {

  can(params?: any[]): boolean {
    return params[0].headers['test-header'] === 'test';
  }
}
//===================== storage
export const RBAC: IStorageRbac = {
  roles: ['role'],
  permissions: {
    permission1: ['filter1', 'filter2', RBAC_REQUEST_FILTER],
  },
  grants: {
    role: [
      `permission1@${RBAC_REQUEST_FILTER}`
    ],
  },
  filters: {
    [RBAC_REQUEST_FILTER]: RequestFilter,
  },
};  
//===================== using for routes
  @RBAcPermissions(`permission1@${RBAC_REQUEST_FILTER}`)
  @UseGuards(
    AuthGuard,
    RBAcGuard,
  )
  @Get('/')
  async test4(): Promise<boolean> {
    return true;
  } 
```

Enter fullscreen mode Exit fullscreen mode