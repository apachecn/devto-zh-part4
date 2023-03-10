# ★如何使用 Laravel，Inertia，TypeScript 处理前端授权

> 原文：<https://dev.to/freekmurze/how-to-handle-front-end-authorization-using-laravel-inertia-and-typescript-349>

最近 [Jeffrey Way](https://twitter.com/jeffrey_way) 在 [Laracasts](https://laracasts.com) 上发布了[一个名为【前端授权头脑风暴】](https://laracasts.com/series/whatcha-working-on/episodes/36)的视频。在该视频中，他展示了向前端传递授权结果的三种方式。

目前我正在做一个大项目，这个项目使用了[惯性](https://reinink.ca/articles/introducing-inertia-js)、[反应](https://reactjs.org/)和[打字稿](https://www.typescriptlang.org/)。在这篇博文中，我不会详细介绍这些内容，但是我想向您展示我们使用这些技术将授权(和路由)传递到前端。

## 使用策略

在我正在使用的应用程序中，有团队和项目。一个团队拥有一个项目。客人也可以访问项目。

在我们的应用程序中，我们所有的授权检查都是使用 [Laravel 策略](https://laravel.com/docs/master/eloquent-resources)进行的。这是项目的政策。

```
namespace App\Domain\Project\Policies;

use App\Domain\Project\Models\Project;
use App\Domain\Team\Models\User;
use Illuminate\Auth\Access\HandlesAuthorization;

class ProjectPolicy
{
    public const ADMINISTER = 'administer';
    public const LEAVE = 'leave';

    use HandlesAuthorization;

    public function administer(User $user, Project $project)
    {
        if (! $user->hasTeamWithProject($project)) {
            return false;
        }

        return $user->isAdminOrOwner($project->team);
    }

    public function leave(User $user, Project $project)
    {
        return $user->isGuestOnProject($project);
    }
} 
```

不要介意那些方法中的逻辑，那不是重点。请注意，对于我们策略中的每个方法，都有一个同名的常量。在一个小项目中，你不会这样做。但是这段代码取自一个相当大的应用程序，其中有许多策略，每个策略都有不同的方法。通过在上设置一个常量，可以像这样进行门检查:

```
Gate::allows(ProjectPolicy::ADMINISTER, $project); 
```

为什么开始输入`ProjectPolicy`任何像样的 IDE 都会向您显示您的策略中的常量。

[![](img/6df82a7ac8adc6f5a0029e7f5606e56e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dMFGRekW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/auth-frontend/1.png)

使用常量还有一个好处，那就是更改策略方法的名称变得很容易。只需更改方法名，并使用您的 IDE 对常量执行重命名重构。一个像样的 IDE 可以更新常量的所有用法。

## 利用资源

在我们的应用中，我们使用[惯性](https://github.com/inertiajs)。这是一个非常酷的软件包集合， [Jonathan Reinink](https://twitter.com/reinink) 目前正在构建。如果你想了解更多关于这个项目的信息，请阅读[这篇博文](https://reinink.ca/articles/introducing-inertia-js)。

利用惯性，每个页面都是它自己的反应(或 Vue 组件)。所以在我们的应用中，Blade 不用于渲染任何东西。因此，在呈现 React powered 视图时，我们不能使用服务器端逻辑。

这是我们的`ProjectsIndexController`的样子:

```
namespace App\Http\App\Controllers\Projects;

use App\Http\App\Resources\Project\ProjectResource;
use Inertia\Inertia;

class ProjectsIndexController
{
    public function __invoke()
    {
        $projects = $this->getProjectsForCurrentUser();

        return Inertia::render('projects.index', [
            'projects' => ProjectResource::collection($projects),
        ]);
    }
} 
```

这里重要的一点是项目集合被传递给`ProjectResource`，它是一个 [API 资源](https://laravel.com/docs/master/eloquent-resources)。Laravel 中的 API 资源是一个专门的类，用于将雄辩的模型转换为 API 响应。我们来看看那个`ProjectResource`。

```
namespace App\Http\App\Resources\Project;

use App\Domain\Project\Policies\ProjectPolicy;
use App\Http\App\Controllers\Projects\Settings\DeleteProjectController;
use App\Http\App\Controllers\Projects\Settings\LeaveProjectController;
use Illuminate\Http\Resources\Json\JsonResource;

class ProjectResource extends JsonResource
{
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            // ... other attributes redacted for brevity
            'can' => [
                ProjectPolicy::ADMINISTER => auth()->user()->can(ProjectPolicy::ADMINISTER, $this->resource),
                ProjectPolicy::LEAVE => auth()->user()->can(ProjectPolicy::LEAVE, $this->resource)
            ],
            'links' => [
                'edit' => action(EditProjectController::class, $this->resource),
                'delete' => action([DeleteProjectController::class, 'show'], $this->resource),
                'leave' => action([LeaveProjectController::class, 'show'], $this->resource),
            ],
        ];
    }
} 
```

在所有资源中，我们使用`can`键来放置前端感兴趣的授权结果。数组中每个条目的键是策略方法的名称，值是检查的结果:`true`或`false`。

前端感兴趣的路线放在`links`键中。前端可以使用路由来呈现到细节屏幕、删除端点等的链接。我们可以使用`action`助手和完全限定类名，因为我们已经[移除了默认的控制器名称空间](https://freek.dev/1324-simplifying-controllers#controllers-are-better-off-without-a-default-namespace)。

## 在前端

在这个项目中，我们使用 TypeScript 来定义自定义类型。每个 API 资源都有一个匹配的类型。下面是`Project` :
的类型定义

```
declare type Project = {
    id: number;
    name: string;
    can: {
        administer: boolean;
        leave: boolean;
    };
    links: {
        show: string;
        delete: string;
        leave: string;
    };
}; 
```

下面是呈现项目列表的`project.index` React 组件。

```
import React from 'react';
import Layout from 'app/views/layouts/Layout';

import {can} from "app/util";

type Props = {
    projects: Array<Project>;
};

export default function index({projects}: Props) {
    return (
        <Layout title="Projects">
            <table>
                <th>
                    <td>Name</td>
                    <td> </td>
                </th>
                {projects.map(project => (
                    <tr>
                        <td>{project.name}</td>
                        <td>{can('administer', project) && <a href={project.links.edit}>Edit</a>}</td>
                        <td>{can('leave', project) && <a href={project.links.leave}>Leave</a>}</td>
                    </tr>

                ))}
            </table>
        </Layout>
    );
} 
```

让我们来看看这里正在发生的事情。还记得我们传给`Inertia::render`的那些`projects`吗？在幕后，惰性会负责将这些项目作为`projects`道具传递给上面的 React 组件。使用 TypeScript，我们明确地说`projects`道具是一个`Project`对象的数组。

```
type Props = {
    projects: Array<Project>;
};

export default function index({projects}: Props) {

// ... 
```

支持 TypeScript 的 ide 现在可以自动完成一个`Project`对象的属性。因此，当输入`project.links`时，IDE 可以向我们显示可用的链接:

[![](img/ab7143333e7cb12d1491abeafd17bf08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2mD83ctM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/auth-frontend/2.png)

让我们把注意力转向`can`方法。它是由我的同事 [Seb](https://sebastiandedeyne.com/) 创建的。这是它的定义:

```
export function can<T extends Authorizable>(ability: keyof T['can'] & string, authorizable: T) {
    return authorizable.can[ability];
} 
```

该函数将检查作为第二个参数传递的对象的`can`属性是否包含作为第一个参数传递的键。准备就绪后，`can('administer', project)`将返回一个布尔值(授权检查的结果)。如果我们试图使用不存在的检查，IDE 会警告我们。

[![](img/d7bec5fc0a51d8c492e3fa0d4dda9f00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LCDB66ie--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/auth-frontend/3.png)

## 关闭思绪

我希望您喜欢这个关于我们如何将授权检查(和路由)传递到前端的演练。本质上，我们向 API 资源添加了一个`can`和`links`条目。在前端，我们使用 TypeScript 来启用自动完成和错误检测。