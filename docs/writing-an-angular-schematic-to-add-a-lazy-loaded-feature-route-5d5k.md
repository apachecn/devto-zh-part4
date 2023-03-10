# 编写角度示意图以添加惰性加载的要素路径

> 原文：<https://dev.to/martzcodes/writing-an-angular-schematic-to-add-a-lazy-loaded-feature-route-5d5k>

#### 用角度示意图让生活更轻松…

<figure>[![](img/5e808db7cf8aecda3328c53226198090.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--57vJX4lL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxWdXSEYAlO1oVs3e_ImDow.png) 

<figcaption>【代码必修图】……</figcaption>

</figure>

#### 什么是角度示意图？

角度示意图使您能够自动生成格式一致的代码。欲了解更多信息，请参阅原理图上的官方角度指南。

[角度](https://angular.io/guide/schematics)

[Manfred Steyer](https://medium.com/u/acb3f1945710) 也有一个关于主题[的优秀系列](https://www.softwarearchitekt.at/post/2018/01/02/angular-cli-and-schematics-part-iii-typescripts-compiler-api.aspx)。

#### 目标

编写一个原理图，创建一个功能模块(带路由)、一个组件，将新的功能模块添加到 app-routing 模块(延迟加载)，以及(可选)添加预定义的防护和预定义的导航项目。

在本文中，我将提供用于生成项目的所有命令。

### 入门

这个项目的回购在这里:[github.com/martzcodes/medium-schematic](http://github.com/martzcodes/medium-schematic)

Angular 8 正式推出，所以我们将结合 [Nx](https://nx.dev) 使用它。

为了生成初始项目，我使用了:

```
npm init nx-workspace medium
# selected CSS, martzcodes, empty
ng add @nrwl/workspace
ng add @nrwl/angular # Adds Angular capabilities
ng g [@nrwl/angular](http://twitter.com/nrwl/angular):library core # store the guards and other stuff
ng g app qwerty --framework="angular" --routing --inline-style --inline-template --skip-tests # template application to be copied from
ng g guard dummy --project=core 
```

这个项目中使用了一些设计决策…我不一定会在实践中使用它们，但它简化了一些事情。在延迟加载路由的情况下，它们可以很好地在自己的库中维护，然后在多个应用程序之间包含和共享。对于像这样的演示项目来说，这有点过了，但是如果你有把所有东西都分成库的应用程序，同样的概念也适用。

运行 ng serve qwerty，确保应用程序以默认的 Nx 页面启动。

现在基本的框架已经搭建好了…让我们来调整一些东西。

生成的 DummyGuard 不做任何事情…让它返回 true。你可以在这里添加任何你想要的守卫逻辑…

```
import { Injectable } from '[@angular/core](http://twitter.com/angular/core)';
import { CanLoad, UrlSegment, Route } from '[@angular/router](http://twitter.com/angular/router)';
import { Observable } from 'rxjs';

[@Injectable](http://twitter.com/Injectable)({
  providedIn: 'root'
})
export class DummyGuard implements CanLoad {
  constructor() {}

canLoad(
    route: Route,
    segments: UrlSegment[]
  ): boolean | Promise<boolean> | Observable<boolean> {
    return true;
  }
} 
```

另外，在 libs/core/src/index.ts 中添加 export * from。/lib/dummy . guard '；

而且…我喜欢我的应用程序路由在我的主应用程序模块之外…所以让我们把它分开。

创建 apps/qwerty/src/app/app-routing . module . ts 并添加以下代码:

```
import { NgModule } from '[@angular/core](http://twitter.com/angular/core)';
import { Routes, RouterModule } from '[@angular/router](http://twitter.com/angular/router)';

const routes: Routes = [];

[@NgModule](http://twitter.com/NgModule)({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {} 
```

并在 apps/qwerty/src/app/app . module . ts:
中做相应的修改

```
import { BrowserModule } from '[@angular/platform-browser](http://twitter.com/angular/platform-browser)';
import { NgModule } from '[@angular/core](http://twitter.com/angular/core)';

import { AppComponent } from './app.component';
import { AppRoutingModule } from './app-routing.module'; // added
// unused imports removed

[@NgModule](http://twitter.com/NgModule)({
  declarations: [AppComponent],
  imports: [BrowserModule, AppRoutingModule], // updated
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {} 
```

现在，让我们让应用组件稍有不同…

```
import { Component } from '[@angular/core](http://twitter.com/angular/core)';

[@Component](http://twitter.com/Component)({
  selector: 'martzcodes-root',
  template: `
    <div style="text-align:center">
      <h1>Welcome to {{ title }}!</h1>
      <pre>{{ navigation | json }}</pre>
      <router-outlet></router-outlet>
</div>
  `,

styles: []
})
export class AppComponent {
  title = 'qwerty';
  navigation: any[];

constructor() {
    this.navigation = [];
  }
} 
```

ng serve qwerty 应该还能用…只是没什么好看的。

### 现在……我们来做个示意图。

运行 ng g 工作空间-逻辑示意图要素-路径

工作空间-原理图是 Nx 提供的原理图，可在工具/原理图/中创建原理图模板

在我们尝试之前，让我们调整一些东西…

对于 tools/schematics/feature-route/schema . JSON，使其看起来像这样:

```
{
  "$schema": "[http://json-schema.org/schema](http://json-schema.org/schema)",
  "id": "feature-route",
  "type": "object",
  "properties": {
    "project": {
      "type": "string",
      "description": "Project name",
      "x-prompt": "Which project?"
    },
    "feature": {
      "type": "string",
      "description": "Feature name",
      "x-prompt": "What's the feature route?"
    },
    "dummy": {
      "type": "boolean",
      "description": "Dummy Guard",
      "default": true,
      "x-prompt": "Should the route use the Dummy Guard?"
    },
    "navigation": {
      "type": "boolean",
      "description": "Navigation",
      "default": true,
      "x-prompt": "Should this be added to the site's navigation?"
    }
  },
  "required": ["project", "feature"]
} 
```

这避免了使用参数值，并使一切都成为交互式提示(就我个人而言)。

Nx 没有加 schema 接口…那我们就加一个吧。创建工具/示意图/要素-路径/方案. ts 并添加以下内容:

```
export default interface Schema {
  project: string;
  feature: string;
  dummy: string;
  navigation: boolean;
} 
```

现在让我们开始使用实际的原理图…工具/原理图/要素-路径/索引. ts 文件。

Nx 查找 index.ts 文件，并使用导出的默认函数来运行 schematics。其余部分遵循 schematics API 中的角度约定。刚开始有点光秃秃的，再延伸一点吧。

进口的就不要说了…这些都是…不用来回了。

```
import {
  chain,
  Rule,
  SchematicContext,
  Tree,
  SchematicsException,
  externalSchematic
} from '[@angular](http://twitter.com/angular)-devkit/schematics';
import { getWorkspacePath, readJsonInTree } from '[@nrwl/workspace](http://twitter.com/nrwl/workspace)';
import Schema from './schema';
import { strings } from '[@angular](http://twitter.com/angular)-devkit/core';
import \* as ts from 'typescript';
import {
  insertImport,
  getSourceNodes,
  InsertChange
} from '[@nrwl/workspace](http://twitter.com/nrwl/workspace)/src/utils/ast-utils'; 
```

现在…让我们定义一个规范化的模式，它将扩展先前定义的模式和一个函数，它将执行规范化

```
interface NormalizedSchema extends Schema {
  appProjectRoot: string;
  // Other things could go in here...
}

function normalizeOptions(host: Tree, options: Schema): NormalizedSchema {
  const appProjectRoot = `apps/${options.project}`;

return {
    ...options,
    appProjectRoot
  };
} 
```

这是一件很方便的事情…很容易扩展而不会弄乱你的输入模式。要使用它，我们需要首先使用我们在导出默认函数中创建的原始模式，然后扩展它…所以新的默认函数变成…

```
export default function(schema: Schema): Rule {
  return (host: Tree, context: SchematicContext) => {
    const options = normalizeOptions(host, schema);
    const angularJson = readJsonInTree(host, getWorkspacePath(host));
    if (!angularJson.projects[options.project]) {
      throw new SchematicsException(
        `Project ${options.project} is not in angular.json file!`
      );
    }
    return chain([
         // ...
    ]);
  };
} 
```

这也展示了一个抛出错误的例子。它接受输入界面提供的选项，将它们规范化，并使用它们继续前进。

现在，最初的示意图设置已经完成…让我们把它分解成更小的块…

#### 外部原理图

Nx 从运行 Nx 的库原理图的外部原理图开始。虽然我们确实想使用外部原理图…但我们不想创建另一个库(至少现在不想)。将其外部原理图替换为:

```
externalSchematic('[@schematics/angular](http://twitter.com/schematics/angular)', 'module', {
  project: options.project,
  name: `${options.feature}`,
  routing: true
}),
externalSchematic('[@schematics/angular](http://twitter.com/schematics/angular)', 'component', {
  project: options.project,
  name: `${options.feature}/${options.feature}-container`,
  changeDetection: 'OnPush'
}), 
```

#### 将特征组件添加到特征路线

现在我们有了一个包含路由和组件的模块…让我们向路由添加一个容器组件。我们需要为角度示意图创建一个规则，以便对要素路径文件进行两处更改。它需要导入功能组件，并将一条路线添加到路线列表中。从函数开始…

```
function addComponentToRoute(options: NormalizedSchema): Rule {
  return (host: Tree) => {
      return host;
    }
  };
} 
```

这不会做任何事情，但它是一个很好的开始片段。请注意，它的输入是我们之前规范化的选项。一旦完成，这将进入默认函数链。

首先让我们定义一些常数…

```
const featureRoutingPath = `${
    options.appProjectRoot
  }/src/app/${strings.dasherize(options.feature)}/${strings.dasherize(
    options.feature
  )}-routing.module.ts`;

const containerComponent = `${strings.capitalize(options.feature)}ContainerComponent`;

const route = `{
    path: '',
    pathMatch: 'full',
    component: ${containerComponent}
  }`; 
```

然后，我们将读入路由路径的类型脚本文件，并读取节点…

```
// tslint:disable-next-line
const featureRouting = host.read(featureRoutingPath)!.toString('utf-8');

const src = ts.createSourceFile(
  `${strings.dasherize(options.feature)}-routing.module.ts`,
  featureRouting,
  ts.ScriptTarget.Latest,
  true
);
const nodes = getSourceNodes(src); 
```

getSourceNodes 是由一些 [Nrwl.io](https://medium.com/u/2817fb68583) 实用程序提供的…它接收 typescript 源代码并输出抽象语法树(AST)中的节点。[这篇文章提供了一些关于使用 Schematics 和如何浏览 AST 的背景知识。](https://www.softwarearchitekt.at/post/2018/01/02/angular-cli-and-schematics-part-iii-typescripts-compiler-api.aspx)

例如…在这个文件中，我们希望找到 routes 数组。我们的下一步将是找到进行变量声明并具有名为 routes:
的标识符的节点

```
const routeNodes = nodes
  .filter((n: ts.Node) => {
    if (n.kind === ts.SyntaxKind.VariableDeclaration) {
      if (
        n.getChildren().findIndex(c => {
          return (
            c.kind === ts.SyntaxKind.Identifier && c.getText() === 'routes'
          );
        }) !== -1
      ) {
        return true;
      }
    }
    return false;
  })
  .map((n: ts.Node) => {
    const arrNodes = n
      .getChildren()
      .filter(c => (c.kind = ts.SyntaxKind.ArrayLiteralExpression));
    return arrNodes[arrNodes.length - 1];
  }); 
```

这是一个关键概念！这些类型的图案在角度示意图中被大量使用，也是一些更难使用的图案(没有很好的记录)。这部分代码获取节点列表，并将它们过滤成包含文本 routes 标识符的变量声明。然后我们映射到 ArrayLiteralExpression，它是实际的列表本身。

接下来，我们需要实际插入对文件的修改…

```
if (routeNodes.length === 1) {
  const navigation: ts.ArrayLiteralExpression = routeNodes[0] as ts.ArrayLiteralExpression;
  const pos = navigation.getStart() + 1;
  const fullText = navigation.getFullText();
  let toInsert = '';
  if (navigation.elements.length > 0) {
    if (fullText.match(/\r\n/)) {
      toInsert = `${fullText.match(/\r\n(\r?)\s*/)[0]}${route},`;
    } else {
      toInsert = `${route},`;
    }
  } else {
    toInsert = `${route}`;
  }

const recorder = host.beginUpdate(featureRoutingPath);
  recorder.insertRight(pos, toInsert);

const componentChange = insertImport(
    src,
    featureRoutingPath,
    `${containerComponent}`,
    `./${strings.dasherize(options.feature)}-container/${strings.dasherize(
      options.feature
    )}-container.component`,
    false
  );
  if (componentChange instanceof InsertChange) {
    recorder.insertLeft(
      (componentChange as InsertChange).pos,
      (componentChange as InsertChange).toAdd
    );
  }

host.commitUpdate(recorder);
  return host;
} 
```

我们检查以确保只找到了一个节点(实际上，如果找到了多个变量，我们可能会抛出一个错误，但现在这样做是可行的)。将进行两项更新。我们需要为组件添加导入语句，然后添加使用导入组件的实际路由。Nrwl.io 提供了一个 insertImport 方法，您需要在其中提供一些基本信息，它将生成添加导入所需的更改。

接下来，我们找到数组节点的起始位置(基本上是文档中的字符位置)。查看是否已经存在任何其他路由，然后编写插入内容。一些角度示意图的具体代码是记录仪的创作。host.beginUpdate 是 Angular Schematics 跟踪文件更改的方式。record.insertRight 添加更改，host.commitUpdate 确认更改，因此当原理图实际执行更改时，它会跟随这些更改。

#### 添加特征路由到 App 路由器(惰性加载)

我们现在在特征模块中有一条路线，但是无法查看该路线。我们需要更改 app-routing.module.ts 文件来添加一个延迟加载的路由，并检查我们是否希望该特性使用虚拟保护…这归结为可选地添加一个导入并将一个路由注入到一个路由列表中。这两项我们都已经做过了！该函数最终看起来像这样:

```
function addRouteToApp(options: NormalizedSchema): Rule {
  return (host: Tree) => {
    const appRoutingPath = `${
      options.appProjectRoot
    }/src/app/app-routing.module.ts`;

// tslint:disable-next-line
    const appRouting = host.read(appRoutingPath)!.toString('utf-8');

const src = ts.createSourceFile(
      'app-routing.module.ts',
      appRouting,
      ts.ScriptTarget.Latest,
      true
    );

const route = `{
      path: '${options.feature}',
      loadChildren: './${strings.dasherize(
        options.feature
      )}/${strings.dasherize(options.feature)}.module#${strings.capitalize(
      options.feature
    )}Module'${
      options.dummy
        ? `,
      canLoad: [DummyGuard]`
        : ''
    }
    }`;

const nodes = getSourceNodes(src);
    const routeNodes = nodes
      .filter((n: ts.Node) => {
        if (n.kind === ts.SyntaxKind.VariableDeclaration) {
          if (
            n.getChildren().findIndex(c => {
              return (
                c.kind === ts.SyntaxKind.Identifier && c.getText() === 'routes'
              );
            }) !== -1
          ) {
            return true;
          }
        }
        return false;
      })
      .map((n: ts.Node) => {
        const arrNodes = n
          .getChildren()
          .filter(c => (c.kind = ts.SyntaxKind.ArrayLiteralExpression));
        return arrNodes[arrNodes.length - 1];
      });

if (routeNodes.length === 1) {
      const navigation: ts.ArrayLiteralExpression = routeNodes[0] as ts.ArrayLiteralExpression;
      const pos = navigation.getStart() + 1;
      const fullText = navigation.getFullText();
      let toInsert = '';
      if (navigation.elements.length > 0) {
        if (fullText.match(/\r\n/)) {
          toInsert = `${fullText.match(/\r\n(\r?)\s*/)[0]}${route},`;
        } else {
          toInsert = `${route},`;
        }
      } else {
        toInsert = `${route}`;
      }

const recorder = host.beginUpdate(appRoutingPath);
      recorder.insertRight(pos, toInsert);

if (options.dummy) {
        const authChange = insertImport(
          src,
          `${options.appProjectRoot}/src/app/app-routing.module.ts`,
          'DummyGuard',
          '[@martzcodes/core](http://twitter.com/martzcodes/core)',
          false
        );
        if (authChange instanceof InsertChange) {
          recorder.insertLeft(
            (authChange as InsertChange).pos,
            (authChange as InsertChange).toAdd
          );
        }
      }

host.commitUpdate(recorder);

return host;
    }
  };
} 
```

这里没有什么特别有趣的东西。对于这条路线，我使用的是 Angular 7 版本的延迟加载，而不是新的 Angular 8 导入方法，所以这至少向后兼容 Angular 7。

#### 向 App 组件添加导航项

接下来…添加导航项目。这可能没有前两节有趣…它只是在组件的导航列表中添加了一个导航项对象。值得注意的是，如果导航输入设置为 false，它会跳过这条规则。函数是这样的:

```
function addNavigation(options: NormalizedSchema): Rule {
  return (host: Tree) => {
    if (!options.navigation) {
      return host;
    }
    const componentPath = `${options.appProjectRoot}/src/app/app.component.ts`;
    const navPath = `{name: '${strings.capitalize(
      options.feature
    )}', router: '/${options.feature}', unsecure: ${
      options.dummy ? 'false' : 'true'
    }}`;
    // tslint:disable-next-line
    const appComponent = host.read(componentPath)!.toString('utf-8');

const src = ts.createSourceFile(
      'app.component.ts',
      appComponent,
      ts.ScriptTarget.Latest,
      true
    );

const nodes = getSourceNodes(src);
    const navNodes = nodes
      .filter((n: ts.Node) => {
        if (n.kind === ts.SyntaxKind.BinaryExpression) {
          if (
            n.getChildren().findIndex(c => {
              return (
                c.kind === ts.SyntaxKind.PropertyAccessExpression &&
                c.getText() === 'this.navigation'
              );
            }) !== -1
          ) {
            return true;
          }
        }
        return false;
      })
      .map((n: ts.Node) => {
        const arrNodes = n
          .getChildren()
          .filter(c => (c.kind = ts.SyntaxKind.ArrayLiteralExpression));
        return arrNodes[arrNodes.length - 1];
      });

if (navNodes.length === 1) {
      const navigation: ts.ArrayLiteralExpression = navNodes[0] as ts.ArrayLiteralExpression;
      const pos = navigation.getEnd() - 1;
      const fullText = navigation.getFullText();
      let toInsert = '';
      if (navigation.elements.length > 0) {
        if (fullText.match(/\r\n/)) {
          toInsert = `,${fullText.match(/\r\n(\r?)\s*/)[0]}${navPath}`;
        } else {
          toInsert = `, ${navPath}`;
        }
      } else {
        toInsert = `${navPath}`;
      }

const recorder = host.beginUpdate(componentPath);
      recorder.insertLeft(pos, toInsert);
      host.commitUpdate(recorder);
    }

return host;
  };
} 
```

#### 最后一步

最后…将规则添加到外部原理图之后的默认功能链中…

```
return chain([
  externalSchematic('[@schematics/angular](http://twitter.com/schematics/angular)', 'module', {
    project: options.project,
    name: `${options.feature}`,
    routing: true
  }),
  externalSchematic('[@schematics/angular](http://twitter.com/schematics/angular)', 'component', {
    project: options.project,
    name: `${options.feature}/${options.feature}-container`,
    changeDetection: 'OnPush'
  }),
  addComponentToRoute(options),
  addRouteToApp(options),
  addNavigation(options)
]); 
```

现在我们可以运行原理图了。运行 npm 运行工作空间-逻辑示意图要素-路径并回答问题…例如:

```
$ npm run workspace-schematic feature-route

> medium@0.0.0 workspace-schematic C:\dev\medium
> nx workspace-schematic "feature-route"

? Which project? qwerty
? What's the feature route? asdf
? Should the route use the Dummy Guard? Yes
? Should this be added to the site's navigation? Yes
create apps/qwerty/src/app/asdf/asdf-routing.module.ts (419 bytes)
create apps/qwerty/src/app/asdf/asdf.module.ts (377 bytes)
create apps/qwerty/src/app/asdf/asdf-container/asdf-container.component.html (33 bytes)
create apps/qwerty/src/app/asdf/asdf-container/asdf-container.component.spec.ts (678 bytes)
create apps/qwerty/src/app/asdf/asdf-container/asdf-container.component.ts (383 bytes)
create apps/qwerty/src/app/asdf/asdf-container/asdf-container.component.css (0 bytes)
update apps/qwerty/src/app/app-routing.module.ts (405 bytes)
update apps/qwerty/src/app/app.component.ts (440 bytes) 
```

现在，您已经自动创建了一个带有可选警卫和导航项目的延迟加载功能路线！

### 常见错误

<figure>[![](img/099b605741d260013137dc0b9dd4609d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dtU6sJ9N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQ5sfwgbNF-8pFFcGl0hYCA.jpeg) 

<figcaption>不计算</figcaption>

</figure>

运行 Nx schematics 时，我经常会遇到这个错误…

```
$ npm run workspace-schematic feature-route

> medium@0.0.0 workspace-schematic C:\dev\medium
> nx workspace-schematic "feature-route"

C:\dev\medium\node\_modules\yargs\yargs.js:1133
      else throw err
           ^

Error: ENOTEMPTY: directory not empty, rmdir 'C:\dev\medium\dist\out-tsc\tools\schematics'
    at Object.rmdirSync (fs.js:684:3)
    at rmkidsSync (C:\dev\medium\node\_modules\fs-extra\lib\remove\rimraf.js:304:27)
    at rmdirSync (C:\dev\medium\node\_modules\fs-extra\lib\remove\rimraf.js:281:7)
    at rimrafSync (C:\dev\medium\node\_modules\fs-extra\lib\remove\rimraf.js:252:7)
    at options.readdirSync.forEach.f (C:\dev\medium\node\_modules\fs-extra\lib\remove\rimraf.js:291:39)
    at Array.forEach (<anonymous>)
    at rmkidsSync (C:\dev\medium\node\_modules\fs-extra\lib\remove\rimraf.js:291:26)
    at rmdirSync (C:\dev\medium\node\_modules\fs-extra\lib\remove\rimraf.js:281:7)
    at Object.rimrafSync [as removeSync] (C:\dev\medium\node\_modules\fs-extra\lib\remove\rimraf.js:252:7)
    at compileTools (C:\dev\medium\node\_modules\[@nrwl](http://twitter.com/nrwl)\workspace\src\command-line\workspace-schematic.js:77:16)
npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! medium@0.0.0 workspace-schematic: `nx workspace-schematic "feature-route"`
npm ERR! Exit status 1
npm ERR!
npm ERR! Failed at the medium@0.0.0 workspace-schematic script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in: 
```

在运行原理图之前运行 rm -rf dist/out-tsc 有助于解决这个问题。我认为这个错误是由于我的 windows 电脑上的病毒扫描程序造成的？_(ツ)_/