# 示意图:构建模块

> 原文：<https://dev.to/thisdotmedia/schematics-building-blocks-pt-2-4b28>

这是原理图的第 2 部分:构建模块。如果你还没看过[第一部分](https://dev.to/thisdotmedia/schematics-building-blocks-2mg3)。我们将继续我们以前的工作。

## 链接示意图

作为一个例子，我将使用 Angular CLI 来生成组件。
如果你以前用过它，你会知道，当运行`ng g c my-component`时，会发生许多操作。

[![ng g component terminal output](img/1253622e777d34da8679b1e36774dddc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C-egWOF---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nqcpij1kt1i8z8k40zkf.png)

我们可以看到两件事正在发生。首先，创建一组文件，然后更新它所在的模块。

这两个操作可以分成两个原理图。

*   从模板创建文件
*   更新模块

让我们创建一个新的原理图。

```
schematics blank component 
```

Enter fullscreen mode Exit fullscreen mode

我们将从另外两个原理图合成这个原理图。请记住，一个文件可以包含多个工厂功能，只有添加到`collection.json`的原理图才可用。

```
import { Rule, SchematicContext, Tree, chain } from '@angular-devkit/schematics';

export function component(options: any): Rule {
  return (tree: Tree, context: SchematicContext) => {
    return chain([
      createFiles(options),
      updateModule(options)
    ])(tree, context);
  };
}

export function createFiles(_options: any): Rule {
  return (tree: Tree, context: SchematicContext) => {
    context.logger.info('Will create files from templates');
    // create files implementation
    return tree;
}
}

export function updateModule(_options: any): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    _context.logger.info('Will update module');
    // update module implementation
    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我跳过了一些实现细节，因为我们想专注于主函数(`component`)。从 schematics 导入的`chain`方法将允许我们连接 schematics。它们将一个接一个地按顺序运行。

如果我们现在构建并运行我们的原理图(`schematics .:component`)，我们将会看到以期望的顺序记录的消息。

## noop

基于一些用户输入，您可能想要跳过这个链中的某些步骤。通过导入同样由`schematics`包提供的 noop 方法，您可以很容易地添加这个功能。

```
export function component(options: any): Rule {
  return (tree: Tree, context: SchematicContext) => {
    return chain([
      createFiles(options),
      options.skipModule ? noop() : updateModule(options)
    ])(tree, context);
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，您可以链接多个原理图，并选择需要运行的原理图。

## 导入原理图

您可能想导入并扩展您的集合的其他原理图，就像我们在前面的例子中链接我们的函数一样。让我们创建一个新的原理图来看看它是如何工作的。

```
schematics blank extended-schematic 
```

Enter fullscreen mode Exit fullscreen mode

```
import { Rule, SchematicContext, Tree, chain, schematic } from '@angular-devkit/schematics';
import { createFromTemplate } from '../create-from-template';

export function extendedSchematic(options: any): Rule {

  return (tree: Tree, context: SchematicContext) => {
    return chain([
      createFromTemplate(options),
        extend()
    ])(tree, context)
  };
}

export function extend(): Rule {
  return (tree: Tree, context: SchematicContext) => {
    context.logger.info('Extending schematic');
    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们构建并测试它，但是忘记添加文件夹参数，它将失败。
如果你还记得我们之前的例子，一个原理图可能有一个定义了一组需求的模式，并添加了关于字段的额外信息，以及如何请求该数据(提示)。通过导入该功能，您将会丢失所有这些设置。导入内部原理图的适当方式是使用`schematic`方法。

```
import { Rule, SchematicContext, Tree, chain, schematic } from '@angular-devkit/schematics';

export function extendedSchematic(options: any): Rule {

  return (tree: Tree, context: SchematicContext) => {
    return chain([
      schematic('create-from-template', {
      ...options
    }),
    extend()
  ])(tree, context)
  };
}

export function extend(): Rule {
  return (tree: Tree, context: SchematicContext) => {
    context.logger.info('Extending schematic');
    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们运行我们的原理图，将会提示您(如果已设置)已经扩展的原理图的必需参数。验证和解析也将按预期工作。

## 扩展外部原理图

扩展我们自己的原理图是一个不错的功能，但是我们可能还需要扩展不属于我们集合的原理图。从前面的例子中我们知道，添加集合并导入我们想要扩展的原理图是不可能的。
为了解决这个问题，我们需要使用一个类似于之前使用的`schematic`函数的函数。这个功能就是`externalSchematic`。让我们看看它的实际效果。

```
schematics blank extend-external-schematic 
```

Enter fullscreen mode Exit fullscreen mode

```
import {
  Rule,
  SchematicContext,
  Tree,
  chain,
  externalSchematic
} from "@angular-devkit/schematics";

export function external(options: any): Rule {
  return (tree: Tree, context: SchematicContext) => {
    return chain([
      externalSchematic("@schematics/angular", "component", {... options}),
      extend()
    ])(tree, context);
  };
}

export function extend(): Rule {
  return (tree: Tree, context: SchematicContext) => {
    context.logger.info("Extending schematic");
    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要向外部原理图函数传递至少三个参数:我们将使用的包的名称、要运行的原理图名称和选项。
如果我们构建并运行原理图，我们会得到一个错误，因为包(@schematics/angular)没有安装，并且因为集合是在 angular 项目中创建的。

## 任务

当运行我们的原理图时，我们可能需要执行其他操作而不修改我们的树。例如，我们可能想要安装我们的依赖项或运行我们的 linter。`@angular-devkit/schematics`包附带了其中的一些任务。
让我们创建一个新的原理图。

```
schematic blank tasks 
```

Enter fullscreen mode Exit fullscreen mode

```
import { Rule, SchematicContext, Tree } from '@angular-devkit/schematics';
import { NodePackageInstallTask } from '@angular-devkit/schematics/tasks'

export function tasks(_options: any): Rule {
  return (tree: Tree, context: SchematicContext) => {
    context.addTask(new NodePackageInstallTask({ packageName: '@schematics/angular' }));
    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们正在向我们的上下文(`NodePackageInstallTask`)添加一个新任务，它将有效地运行我们首选的包管理器的`install`命令。
如果一个任务依赖于另一个要完成的任务，`addTask`接受一个依赖关系数组(其他任务 id)作为第二个参数。

```
import { Rule, SchematicContext, Tree } from '@angular-devkit/schematics';
import { NodePackageInstallTask, TslintFixTask } from '@angular-devkit/schematics/tasks'

export function tasks(_options: any): Rule {
  return (tree: Tree, context: SchematicContext) => {
    const taskId = context.addTask(new NodePackageInstallTask({ packageName: '@schematics/angular' }));
    context.addTask(new TslintFixTask({}), [taskId])
    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，`TsLintFixTask`不会运行，直到
`NodePackageInstallTask`完成，因为它被列为依赖项。

> 要使用全局安装的`schematics-cli`运行`tasks`原理图，请确保您已经全局安装了`tslint`和`typescript`，否则`TsLintFixTask`将会失败。

## 测试

到目前为止，我们已经在文件系统中完成了许多不同的操作，并且扩展了我们的原理图和外部原理图。然而，我们缺少准备就绪的原理图集合的一个重要部分。测试。我们如何测试原理图？
让我们从第一张原理图`create-file`和自动生成的测试文件开始。

```
import { Tree } from '@angular-devkit/schematics';
import { SchematicTestRunner } from '@angular-devkit/schematics/testing';
import * as path from 'path';

const collectionPath = path.join(__dirname, '../collection.json');

describe('create-file', () => {
  it('works', () => {
    const runner = new SchematicTestRunner('schematics', collectionPath);
    const tree = runner.runSchematic('create-file', {}, Tree.empty());

    expect(tree.files).toEqual([]);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们创建了一个测试运行器，并为它提供了集合模式的路径。然后我们在给定的树上运行我们的原理图。在本例中，是一棵空树。如果我们照现在这样运行这个测试，它将会失败。

[![Failed test](img/3a17ed8592eacf49c99a9df760dfe048.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1WNPVb__--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ii6g5kwd7uyqjh3ar9us.png)

记住，在创建模式时，我们在模式中添加了一个必需的`path`参数。现在我们知道测试失败了，让我们写一个测试来检查它是否失败，以及当它成功时写另一个测试。

```
// create-file/index.spec.ts
import { Tree } from '@angular-devkit/schematics';
import { SchematicTestRunner } from '@angular-devkit/schematics/testing';
import * as path from 'path';

const collectionPath = path.join(__dirname, '../collection.json');

describe('create-file', () => {
  it('Should throw if path argument is missing', () => {
    const runner = new SchematicTestRunner('schematics', collectionPath);
    let errorMessage;
    try {
      runner.runSchematic('create-file', {}, Tree.empty());
    } catch (e) {
      errorMessage = e.message;
    }
    expect(errorMessage).toMatch(/required property 'path'/);
  });

  it('Should create a file in the given path', () => {
    const runner = new SchematicTestRunner('schematics', collectionPath);
    const tree = runner.runSchematic('create-file', { path: 'my-file.ts' }, Tree.empty());
    expect(tree.files).toEqual(['/my-file.ts']);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

测试所有可能的错误。修改文件时，测试其内容。

```
// ts-ast/index.spec.ts
import { Tree } from '@angular-devkit/schematics';
import { SchematicTestRunner } from '@angular-devkit/schematics/testing';
import * as path from 'path';

const collectionPath = path.join(__dirname, '../collection.json');

describe('ts-ast', () => {
  it('Should throw if path argument is missing', () => {
    const runner = new SchematicTestRunner('schematics', collectionPath);
    let errorMessage;
    try {
      runner.runSchematic('ts-ast', {}, Tree.empty());
    } catch (e) {
      errorMessage = e.message;
    }
    expect(errorMessage).toMatch(/required property 'path'/);
  });

  it("Should throw if file in the given path does not exist", () => {
    const runner = new SchematicTestRunner("schematics", collectionPath);
    let errorMessage;
    try {
      runner.runSchematic("ts-ast", { path: "my-file.ts" }, Tree.empty());
    } catch (e) {
      errorMessage = e.message;
    }
    expect(errorMessage).toMatch(/File my-file.ts not found/);
  });

  it("Should throw if no interface is present", () => {
    const runner = new SchematicTestRunner("schematics", collectionPath);
    const sourceTree = Tree.empty();
    sourceTree.create('test.ts', 
      `export class MyClass { }`
    );
    let errorMessage;
    try {
      runner.runSchematic('ts-ast', { path: 'test.ts' }, sourceTree);
    } catch (e) {
      errorMessage = e.message;
    }
    expect(errorMessage).toMatch(/No Interface found/);
  });

  it('Should update a file in the given path', () => {
    const runner = new SchematicTestRunner('schematics', collectionPath);
    const sourceTree = Tree.empty();
    sourceTree.create('test.ts', 
      `export interface MyInterface {
        name: string;
      }`
    );
    const tree = runner.runSchematic('ts-ast', { path: 'test.ts' }, sourceTree);
    expect(tree.files).toEqual(['/test.ts']);
    expect(tree.readContent('/test.ts')).toEqual(
      `export interface MyInterface {
        first: string;
        name: string;
        last: string;
      }`
     );
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

您可以在[资源库](https://github.com/flakolefluk/schematics-tutorial)中找到所有测试

## 原理图和角度 CLI

到目前为止，我们使用的是没有角度 CLI 的原理图。原理图可以有任何名称，但有几个名称在与`ng`命令一起使用时有特殊含义。
例如，运行`ng add <package_name>`将下载包，将在`package.json`中的`schematics`键中检查集合引用，并将运行该集合的`ng-add`原理图。

让我们创建一个新的原理图。

```
schematics blank ng-add 
```

Enter fullscreen mode Exit fullscreen mode

这是我们第一次不得不考虑我们的原理图将如何与角度工作空间进行交互。我们必须考虑运行它需要什么。
在本例中，我们将对工作区`README.md`文件进行简单的修改

我们来看一下实现。

```
import { Rule, SchematicContext, Tree } from '@angular-devkit/schematics';

export function ngAdd(_options:any): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    return tree.overwrite('README.md', 'overwritten file');
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来非常简单，但是在测试它的时候，我们认为它应该在一个有角度的工作空间内运行。这是一个简单的例子，但是当修改项目时，这将变得更加明显。
我们可以手动创建这个新的角度工作空间，但有更好的方法。我们将使用`@schematics/angular`包创建一个工作区，就像 Angular CLI 一样。
让我们先安装软件包。

```
npm install --save-dev @schematics/angular 
```

Enter fullscreen mode Exit fullscreen mode

```
import { SchematicTestRunner } from '@angular-devkit/schematics/testing';
import * as path from 'path';
import { Tree } from '@angular-devkit/schematics';

const collectionPath = path.join(__dirname, '../collection.json');

describe('ng-add', () => {
  const workspaceOptions = {
    name: 'workspace',
    newProjectRoot: 'projects',
    version: '8.0.0',
  };

  const runner = new SchematicTestRunner('schematics', collectionPath);

  it('should throw if no readme is not found', async () => {
    let errorMessage;
    try{
      runner.runSchematic('ng-add', { }, Tree.empty());
    } catch(e){
      errorMessage = e.message;
    }
    expect(errorMessage).toMatch(/Path "\/README.md" does not exist./);

  });

  it('overwrite workspace README file', async () => {
    const sourceTree = await runner.runExternalSchematicAsync('@schematics/angular','workspace', workspaceOptions).toPromise();
    const tree = runner.runSchematic('ng-add', {}, sourceTree);
    expect(tree.files).toContain('/README.md');
    expect(tree.readContent('/README.md')).toMatch(/overwritten file/);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

第二个测试是为已安装的包运行外部原理图，以创建工作空间。然后，我们运行我们的`ng-add`原理图来修改包含角度工作空间的树。您可以使用`@schematics/angular`包做更多的事情来准备您的测试树，比如创建新的项目或组件。这是模仿真实项目的好方法。
我们之前的原理图非常普通，如果我们想在 angular 项目中运行它们，我们必须重建测试时预期使用它们的环境。

## 最后的话

*   你可以在这里找到代码
*   如果可能的话，把你的原理图分成更简单的。您可能需要在其他地方重用它们，并且它们总是可以被链接。
*   总是测试你的原理图，如果可以的话，重新创建一个能让它们运行得最好的环境。如果它们将在角度工作空间上运行，则创建它。如果有其他原理图可以完成这项任务，请使用它们。这是 schematics 的特点之一:避免重复的任务。
*   从其他地方导入时，请始终使用`schematic`和`externalShematic`功能。
*   在第 3 部分中，我们将创建一个原理图，将 TailwindCSS 添加到 Angular 项目中。

## 参考文献

*   [角度示意图](https://github.com/angular/angular-cli/tree/master/packages/schematics/angular)
*   [CDK 示意图](https://github.com/angular/components/tree/master/src/cdk)
*   [ngrx 原理图](https://github.com/ngrx/platform/tree/master/modules/schematics)

## 相关博文

*   [https://blog . angular . io/angular-schematics-unit-testing-3a 0a 9 aaab 186](https://blog.angular.io/angular-schematics-unit-testing-3a0a9aaab186)

这篇文章是由 Ignacio Falk 写的，他是 T2 的一名软件工程师。

你可以在推特上关注他，电话是 [@flakolefluk](https://dev.to/flakolefluk) 。

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](https://thisdot.co) 查看我们的服务列表。