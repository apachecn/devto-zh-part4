# 角色授权和角色权限管理

> 原文：<https://dev.to/amiryousefi_/laravel-authorization-and-roles-permission-management-172a>

* * *

You can read this post on Medium,
[https://medium.com/swlh/laravel-authorization-and-roles-permission-management-6d8f2043ea20?source=friends_link&sk=8c0dbf347d4d765ac2b276e4a53c5a4f](https://medium.com/swlh/laravel-authorization-and-roles-permission-management-6d8f2043ea20?source=friends_link&sk=8c0dbf347d4d765ac2b276e4a53c5a4f)

* * *

In many web projects, we have different user roles interacting with the system. Each role has its own permission. Every feature of the system can be enabled or disabled for these roles. We can define users permissions in our codes and check if they are authorized to do the requested action or not. A better way, mostly in more flexible systems, is to create a role and authorization management system. I’ll explain how to implement a Laravel authorization system and define users permission based on their roles.

* * *

In this post, firstly we manage users in groups we called roles. Every role has different permissions. In order to avoid permissions conflict, we assume each user has only one role. Secondly, Laravel authorization implemented by a middleware. This middleware checks for the user’s role permission and authorizes user requests.

### 创建角色和权限

为了实现 Laravel 授权，我们将创建角色和权限表。为了给用户分配角色，我们创建了一个角色表。角色迁移表就像这样简单:

```
Schema::create(‘roles’, function (Blueprint $table) {
 $table->increments(‘id’);
 $table->string(‘name’);
 $table->string(‘description’)->nullable();
 $table->timestamps();
});

```

我们有角色的 ID 和名称。所有用户都将以这些角色进行管理。还有一个描述字段，因为您可能需要一个简短的角色注释来描述每个角色。之后，我们在用户表中添加一个外键 role_id。将该字段添加到默认用户模型有助于我们进行 Laravel 授权。

```
$table->unsignedInteger(‘role_id’)->index();
$table->foreign(‘role_id’)->references(‘id’)->on(‘roles’);

```

* * *

Now let’s talk about the permissions table. Every request leads to a method of a controller. So we store a list of all methods and their controller’s name in the permissions table. Later, we explain how we gather this list and how we check users authorization in Laravel by this permissions table.

```
Schema::create(‘permissions’, function (Blueprint $table) {
 $table->increments(‘id’);
 $table->string(‘name’)->nullable();
 $table->string(‘key’)->nullable();
 $table->string(‘controller’);
 $table->string(‘method’);
 $table->timestamps();
});

```

最后，在角色和权限之间创建了一种关系。

```
Schema::create(‘permission_role’, function (Blueprint $table) {
 $table->unsignedInteger(‘permission_id’);
 $table->unsignedInteger(‘role_id’);
$table->foreign(‘permission_id’)
 ->references(‘id’)
 ->on(‘permissions’)
 ->onDelete(‘cascade’);
$table->foreign(‘role_id’)
 ->references(‘id’)
 ->on(‘roles’)
 ->onDelete(‘cascade’);
$table->primary([‘permission_id’, ‘role_id’]);
});

```

我们创建了一个完整的用户->角色->权限架构。之后，访问列表将存储在这些表中。因此，我们可以通过对照这个列表检查请求来轻松实现 Laravel 授权。

有关创建表的更多信息，请阅读 Laravel 迁移文档。

* * *

### 为用户权限创建访问列表

这篇文章的全部目的是关于动态的。尤其是在角色类型不同的系统中。我们需要在系统中创建一个权限列表。此外，该列表必须随着系统的发展而更新。控制器和方法列表很好地代表了系统中的所有权限。每条路线都通向一个控制者的方法。因此，使用 routes 列表制作一个权限列表是个好主意。为了做到这一点，我使用了 Laravel 数据库播种器。首先，让我们写一个角色播种器。它创建了我们需要的基本角色，并将它们存储在角色表中。运行 artisan 命令将为您创建 roles seeder:

`php artisan make:seeder RolesTableSeeder`

在这个 RolesTableSeeder 中，我们创建了我们的基本角色:

```
DB::table(‘roles’)->insert([
 [‘name’ => ‘admin’],
 [‘name’ => ‘operator’],
 [‘name’ => ‘customer’],
]);

```

您可以根据需要添加任意数量的角色。此外，您可以在需要新角色时从您的网站创建新角色。

* * *

The second step is to create an authorization list for each role. we create another Laravel seeder in which populate permissions table:

`php artisan make:seeder PermissionTableSeeder`

首先，我们得到所有路由列表。然后，我们检查数据库是否已经存储了许可。之后，如果这个权限不在表中，我们在权限表中插入新的权限。毕竟，我们将所有权限都赋予了管理员角色。

```
$permission_ids = []; // an empty array of stored permission IDs
// iterate though all routes
foreach (Route::getRoutes()->getRoutes() as $key => $route)
{
 // get route action
 $action = $route->getActionname();
// separating controller and method
 $_action = explode(‘@’,$action);

 $controller = $_action[0];
 $method = end($_action);

 // check if this permission is already exists
 $permission_check = Permission::where(
         [‘controller’=>$controller,’method’=>$method]
     )->first();
 if(!$permission_check){
   $permission = new Permission;
   $permission->controller = $controller;
   $permission->method = $method;
   $permission->save();
   // add stored permission id in array
   $permission_ids[] = $permission->id;
 }
}
// find admin role.
$admin_role = Role::where(‘name’,’admin’)->first();
// atache all permissions to admin role
$admin_role->permissions()->attach($permission_ids);

```

### 使用中间件进行授权

Laravel 中的每个请求都要经过中间件。知道创建角色中间件将会进行 Laravel 授权。您可以手动或通过 artisan 命令创建中间件:

`php artisan make:middleware RolesAuth`

在这个中间件中，我们获得登录用户的所有权限。然后，我们检查请求的操作是否在权限列表中。如果在权限列表中找不到请求的操作，将返回 403 错误响应。

```
// get user role permissions
$role = Role::findOrFail(auth()->user()->role_id);
$permissions = $role->permissions;
// get requested action
$actionName = class_basename($request->route()->getActionname());
// check if requested action is in permissions list
foreach ($permissions as $permission)
{
 $_namespaces_chunks = explode(‘\\’, $permission->controller);
 $controller = end($_namespaces_chunks);
 if ($actionName == $controller . ‘@’ . $permission->method)
 {
   // authorized request
   return $next($request);
 }
}
// none authorized request
return response(‘Unauthorized Action’, 403);

```

最后，你可以在 Laravel 注册这个中间件，根据你的需求来使用。