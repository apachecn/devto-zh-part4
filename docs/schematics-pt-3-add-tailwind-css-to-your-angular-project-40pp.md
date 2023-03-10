# 原理图:添加顺风 CSS 到您的角度项目

> 原文：<https://dev.to/thisdotmedia/schematics-pt-3-add-tailwind-css-to-your-angular-project-40pp>

这是原理图系列的第 3 部分。

## TailwindCSS

最近我听说了很多关于 [Tailwind CSS](https://tailwindcss.com/) 的事情，我想尝试一下。Tailwind 是一个 PostCSS 插件，将它添加到 Angular 项目中并不是一项简单的任务。它包括几个步骤。
谷歌了一下，我找到了一些如何添加它的例子。一些选项包括使用额外的 CLI，或者在构建之前添加一个脚本来运行。但是，我更喜欢坚持使用 Angular CLI 已经提供的功能，以及扩展它的可能性。

> 此示例适用于角度为 8°或更高的情况。

我们需要做几件事来让这个工作。

*   **为开发和生产构建创建** webpack 配置文件。
*   **创建**一个顺风配置文件。
*   **更新**我们的全局样式文件并添加顺风样式
*   **更新** angular.json 以使用自定义构建器。
*   **更新** package.json 并添加所有必需的依赖项
*   **安装**依赖

这是创建逻辑示意图集合并自动完成该过程的绝佳机会。

创建一个新的 schematics 集合，移除不必要的文件，并创建两个名为`ng-add`和`ng-add-setup`
的原理图

```
schematics blank --name=tailwind-schematics
cd tailwind-schematics
rm -rf src/tailwind-schematics
schematics blank --name=ng-add
schematics blank --name=ng-add-setup 
```

Enter fullscreen mode Exit fullscreen mode

清理`collection.json`，并删除对已删除的顺风示意图的引用。

```
{  "$schema":  "../node_modules/@angular-devkit/schematics/collection-schema.json",  "schematics":  {  "ng-add":  {  "description":  "Adds tailwindCSS to an Angular project",  "factory":  "./ng-add/index#ngAdd",  "schema":  "./ng-add/schema.json"  },  "ng-add-setup":  {  "description":  "Setups project before installing dependencies",  "factory":  "./ng-add-setup/index#ngAddSetup",  "private":  true,  "schema":  "./ng-add-setup/schema.json"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`ng-add-setup`被标记为私有。我们将它列在我们的集合中，因为我们希望它可以通过`ng-add`原理图访问。

我们将把动作列表分成两个任务:第一个任务是创建和更新文件，另一个任务是安装我们的依赖项。
`ng-add`将负责按顺序运行任务。我们还将添加一个标志，以确定在做出更改后是否应该运行安装。
记得给你的示意图添加一个模式。

```
{  "$schema":  "http://json-schema.org/schema",  "id":  "tailwind-ng-add-schematic",  "title":  "Add tailwind to angular project",  "type":  "object",  "properties":  {  "project":  {  "type":  "string",  "description":  "The name of the project."  },  "skipInstall":  {  "type":  "boolean",  "description":  "Condition to run npm install"  }  },  "required":  [],  "additionalProperties":  false  } 
```

Enter fullscreen mode Exit fullscreen mode

```
export interface NgAddOptions {
  project: string;
  skipInstall: boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
export function ngAdd(options: NgAddOptions): Rule {
  return (tree: Tree, context: SchematicContext) => {
    // 1\. Do some checks before starting
    const workspace = getWorkspace(tree);
    const packageJson = getPackageJson(tree);
    const projectName = options.project || workspace.defaultProject;

    const coreVersion: string = packageJson.dependencies['@angular/core'];

    if (!coreVersion) {
      throw new SchematicsException(
        'Could not find @angular/core version in package.json.',
      );
    }

    const majorVersion: number = parseInt(
      coreVersion.split('.')[0].replace(/\D/g, ''),
      10,
    );

    if (majorVersion < 8) {
      throw new SchematicsException('Minimum version requirement not met.');
    }

   // 2\. run schematic that chain a set of schematics that will add and update files
    const setupId = context.addTask(
      new RunSchematicTask('ng-add-setup', { project: projectName }),
    );

   // 3\. Install dependencies (or skip installation)
    if(!options.skipInstall){
      context.addTask(new NodePackageInstallTask(), [setupId]);
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我在这里使用了几个实用函数来获取并解析`angular.json`和`package.json`。对于版本 0.0.1，我们将支持 Angular 8+，并确保该项目满足这一要求。
然后，我们将运行`ng-add-setup`原理图，在原理图完成后，我们将可选地运行`NodePackageInstall`任务。

大部分工作将由`ng-add-setup`处理。让我们设想一下它将如何工作。

```
export interface NgAddSetupOptions {
  project: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
export function ngAddSetup(options: NgAddSetupOptions): Rule {
    return chain([
      addWebpackConfigFiles(options),
      addTailwindConfigFile(options),
      updateStylesFile(options),
      updateAngularConfig(options),
      updatePackageJson(options),
    ])(tree, _context);
  };
}

function addWebpackConfigFiles(options): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    // ...create webpack config files (from template)
  }
}

function addTailwindConfigFile(options): Rule {
  return (tree: Tree, _context: SchematicContext) => {
   // ...create tailwind config files (from template)
  };
}

function updatePackageJson(options): Rule {
  return (tree: Tree, _context: SchematicContext): Tree => {
    // add dependencies and devdepencies to package.json
  };
}

function updateStylesFile(options): Rule {
  return (tree: Tree, _context: SchematicContext) => {
   // add @tailwind imports to global styles file
  };
}

function updateAngularConfig(options): Rule {
  return (tree: Tree, _context: SchematicContext) => {
   // modify angular.json to use custom webpack config and builders
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将从文件创建开始。正如我们之前看到的，我们将创建一个包含将要添加的文件的文件夹。

我们将我们的文件命名为 webpack.config.json、webpack-prod.config.json 和 tailwind.config.js

我在它们自己的文件夹`webpack`和`tailwind`中创建了这些文件。我更喜欢保持这种方式，以便以后阅读这些文件夹时更容易。

```
// files/webpack/webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\<%= stylesExt %>$/,
        use: [
          {
            loader: "postcss-loader",
            options: {
              plugins: [
                require("tailwindcss")("./tailwind.config.js"),
                require("autoprefixer")
              ]
            }
          }
        ]
      }
    ]
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
// files/webpack/webpack-prod.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\<%= stylesExt %>$/,
        use: [
          {
            loader: "postcss-loader",
            options: {
              plugins: [
                require("tailwindcss")("./tailwind.config.js"),
                require("@fullhuman/postcss-purgecss")({
                  content: [
                    "./src/**/*.component.html",
                    "./src/**/*.component.ts"
                  ],
                  defaultExtractor: content =>
                    content.match(/[A-Za-z0-9-_:/]+/g) || []
                }),
                require("autoprefixer")
              ]
            }
          }
        ]
      }
    ]
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们将样式扩展传递给配置模板，让它知道要查找什么文件。两个配置文件看起来非常相似，但是在生产中，我们将运行 PurgeCSS 来删除我们的应用程序中没有使用的所有代码。这是重要的一步。如果你不这样做，你的 CSS 文件将会很大。

```
// ...
function addWebpackConfigFiles(stylesExt: string, projectSrcRoot: string): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    const source: Source = url('files/webpack');
    return mergeWith(
      apply(source, [
        template({ stylesExt }),
        move(normalize(`${projectSrcRoot}/..`)),
      ]),
    )(tree, _context);
  };
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

我们的函数将读取 files webpack 文件夹，并将模板规则应用于每个文件，传递要在模板中使用的 stylesExtension 参数。然后，这些文件将被移动到我们的项目根文件夹中(一个工作区可以有多个项目，项目根不一定是工作区根)。

我们将对顺风配置文件做同样的事情。

```
// files/tailwind/tailwind.config.js
module.exports = {
  theme: {
    extend: {}
  },
  variants: {},
  plugins: []
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
// ...
function addTailwindConfigFile(projectSrcRoot: string): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    const source: Source = url('files/tailwind');
    return mergeWith(apply(source, [move(normalize(`${projectSrcRoot}/..`))]))(
      tree,
      _context,
    );
  };
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 TailwindCSS 的默认配置。我们可以稍后使用一些参数来调整它。
文件创建到此结束。让我们从文件更新开始。

```
// ...
function updatePackageJson(pkgJson: PackageJson): Rule {
  return (tree: Tree, _context: SchematicContext): Tree => {
    let customBuilderVersion: string = '';

    pkgJson.devDependencies = pkgJson.devDependencies || {};

    const builderVersion =
      pkgJson.devDependencies['@angular-devkit/build-angular'];

    if(builderVersion){
      const partialVersion = builderVersion
      .substring(
        builderVersion.indexOf('.') + 1,
        builderVersion.lastIndexOf('.'),
      );
      customBuilderVersion = `~${partialVersion[0]}${partialVersion[2]}.0`
    }

    pkgJson.devDependencies['@angular-builders/custom-webpack'] =
      pkgJson.devDependencies['@angular-builders/custom-webpack'] || customBuilderVersion || '~8.0.0';

    pkgJson.devDependencies['@angular-devkit/build-angular'] =
      builderVersion || '~0.800.0';

    pkgJson.devDependencies['@fullhuman/postcss-purgecss'] =
      pkgJson.devDependencies['@fullhuman/postcss-purgecss'] || '~1.2.0';

    pkgJson.devDependencies['tailwindcss'] =
      pkgJson.devDependencies['tailwindcss'] || '~1.1.2';

    tree.overwrite('package.json', JSON.stringify(pkgJson, null, 2));

    return tree;
  };
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

如果尚未添加所需的依赖项，我们将添加它们。`@angular-builders/custom-webpack`版本取决于`@angular-devkit/build-angular`。然后，我们用修改后的版本替换 package.json。当我们在`ng-add`上运行最后一个任务时，这些包将被安装(或不被安装)。

```
// ...
function updateStylesFile(projectSrcRoot: string, stylesExt: string): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    const file = tree.read(`${projectSrcRoot}/styles.${stylesExt}`);

    if (!file) {
      throw new SchematicsException('Style file not found.');
    }

    const fileContent = file.toString();

    const imports = [
      '@tailwind base;',
      '@tailwind components;',
      '@tailwind utilities;',
    ];

    const recorder = tree.beginUpdate(`${projectSrcRoot}/styles.${stylesExt}`);
    imports.forEach(imported => {
      if (!fileContent.includes(imported)) {
        recorder.insertLeft(0, `${imported}\n`);
      }
    });
    tree.commitUpdate(recorder);
    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

为了更新我们的样式文件，我们正在检查相应的顺风导入是否存在，并将其添加到文件的顶部。

> 我们依赖这种风格。<stylesext>存在。这是创建项目时的默认设置，我们可以确保在文件的顶部添加顺风导入。</stylesext>

最后更新的文件是`angular.json`。我们将使用定制的项目生成器替换项目生成器，并根据环境提供定制的 webpack 配置。

> 在本教程中，我们不会讨论定制构建器。我们使用它们来扩展我们的`build`和`serve`命令，根据环境使用添加的 webpack 配置。这需要能够预处理顺风导入(并清除生产中未使用的 CSS)。

```
// ...
function updateAngularConfig(
  workspace: Workspace,
  projectName: string,
  projectSrcRoot: string,
): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    workspace.projects[projectName].architect.build.builder =
      '@angular-builders/custom-webpack:browser';

    workspace.projects[
      projectName
    ].architect.build.options.customWebpackConfig = {
      path: path.join(projectSrcRoot, '..', 'webpack.config.js'),
    };

    workspace.projects[
      projectName
    ].architect.build.configurations.production.customWebpackConfig = {
      path: path.join(projectSrcRoot, '..', 'webpack-prod.config.js'),
    };

    workspace.projects[projectName].architect.serve.builder =
      '@angular-builders/custom-webpack:dev-server';
    tree.overwrite('angular.json', JSON.stringify(workspace, null, 2));
  };
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

我们最后的`ng-add-setup`原理图。

```
import {
  Rule,
  SchematicContext,
  Tree,
  chain,
  url,
  mergeWith,
  Source,
  template,
  apply,
  SchematicsException,
  move,
} from '@angular-devkit/schematics';

import * as path from 'path';

import { NgAddSetupOptions } from './schema';
import { normalize } from '@angular-devkit/core';
import { PackageJson, Workspace } from '../common/models';
import {
  getWorkspace,
  getProject,
  getProjectSrcRoot,
  getProjectStylesExt,
  getPackageJson,
} from '../common/utils';

export function ngAddSetup(options: NgAddSetupOptions): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    const workspace = getWorkspace(tree);
    const project = getProject(workspace, options.project);
    const root = getProjectSrcRoot(project);
    const stylesExt = getProjectStylesExt(project);
    const packageJson = getPackageJson(tree);

    return chain([
      addWebpackConfigFiles(stylesExt, root),
      addTailwindConfigFile(root),
      updateStylesFile(root, stylesExt),
      updateAngularConfig(workspace, options.project, root),
      updatePackageJson(packageJson),
    ])(tree, _context);
  };
}

function addWebpackConfigFiles(
  stylesExt: string,
  projectSrcRoot: string,
): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    const source: Source = url('files/webpack');
    return mergeWith(
      apply(source, [
        template({ stylesExt }),
        move(normalize(`${projectSrcRoot}/..`)),
      ]),
    )(tree, _context);
  };
}

function addTailwindConfigFile(projectSrcRoot: string): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    const source: Source = url('files/tailwind');
    return mergeWith(apply(source, [move(normalize(`${projectSrcRoot}/..`))]))(
      tree,
      _context,
    );
  };
}

function updatePackageJson(pkgJson: PackageJson): Rule {
  return (tree: Tree, _context: SchematicContext): Tree => {
    let customBuilderVersion: string = '';

    pkgJson.devDependencies = pkgJson.devDependencies || {};

    const builderVersion =
      pkgJson.devDependencies['@angular-devkit/build-angular'];

    if (builderVersion) {
      const partialVersion = builderVersion.substring(
        builderVersion.indexOf('.') + 1,
        builderVersion.lastIndexOf('.'),
      );
      customBuilderVersion = `~${partialVersion[0]}.${partialVersion[2]}.0`;
    }

    pkgJson.devDependencies['@angular-builders/custom-webpack'] =
      pkgJson.devDependencies['@angular-builders/custom-webpack'] ||
      customBuilderVersion ||
      '~8.0.0';

    pkgJson.devDependencies['@angular-devkit/build-angular'] =
      builderVersion || '~0.800.0';

    pkgJson.devDependencies['@fullhuman/postcss-purgecss'] =
      pkgJson.devDependencies['@fullhuman/postcss-purgecss'] || '~1.2.0';

    pkgJson.devDependencies['tailwindcss'] =
      pkgJson.devDependencies['tailwindcss'] || '~1.1.2';

    tree.overwrite('package.json', JSON.stringify(pkgJson, null, 2));

    return tree;
  };
}

function updateStylesFile(projectSrcRoot: string, stylesExt: string): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    const file = tree.read(`${projectSrcRoot}/styles.${stylesExt}`);

    if (!file) {
      throw new SchematicsException('Style file not found.');
    }

    const fileContent = file.toString();

    const imports = [
      '@tailwind base;',
      '@tailwind components;',
      '@tailwind utilities;',
    ];

    const recorder = tree.beginUpdate(`${projectSrcRoot}/styles.${stylesExt}`);
    imports.forEach(imported => {
      if (!fileContent.includes(imported)) {
        recorder.insertLeft(0, `${imported}\n`);
      }
    });
    tree.commitUpdate(recorder);
    return tree;
  };
}

function updateAngularConfig(
  workspace: Workspace,
  projectName: string,
  projectSrcRoot: string,
): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    workspace.projects[projectName].architect.build.builder =
      '@angular-builders/custom-webpack:browser';

    workspace.projects[
      projectName
    ].architect.build.options.customWebpackConfig = {
      path: path.join(projectSrcRoot, '..', 'webpack.config.js'),
    };

    workspace.projects[
      projectName
    ].architect.build.configurations.production.customWebpackConfig = {
      path: path.join(projectSrcRoot, '..', 'webpack-prod.config.js'),
    };

    workspace.projects[projectName].architect.serve.builder =
      '@angular-builders/custom-webpack:dev-server';
    tree.overwrite('angular.json', JSON.stringify(workspace, null, 2));
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

## 单元测试

我们需要单元测试来检查我们的原理图是否正常工作。
记住我们之前的教程，我们需要首先创建环境来运行原理图。
让我们从将`@schematics/angular`包添加到我们的 devDependencies 开始。我们将使用它来创建一个工作区和一个默认项目。

```
npm i --save-dev @schematics/angular 
```

Enter fullscreen mode Exit fullscreen mode

```
// ng-add/index.spec.ts
import {
  SchematicTestRunner,
  UnitTestTree,
} from '@angular-devkit/schematics/testing';
import * as path from 'path';
import { Tree } from '@angular-devkit/schematics';

const collectionPath = path.join(__dirname, '../collection.json');
const runner = new SchematicTestRunner('schematics', collectionPath);

describe('ng-add', () => {
  const workspaceOptions = {
    name: 'workspace',
    newProjectRoot: 'projects',
    version: '8.0.0',
  };

  const appOptions = {
    name: 'testApp',
    inlineStyle: false,
    inlineTemplate: false,
    routing: false,
    style: 'css',
    skipTests: false,
    skipPackageJson: false,
  };

  let sourceTree: UnitTestTree;

  describe('when in an angular workspace', () => {
    beforeEach(async () => {
      sourceTree = await runner
        .runExternalSchematicAsync(
          '@schematics/angular',
          'workspace',
          workspaceOptions,
        )
        .toPromise();
      sourceTree = await runner
        .runExternalSchematicAsync(
          '@schematics/angular',
          'application',
          appOptions,
          sourceTree,
        )
        .toPromise();
    });

    it('schedules two tasks by default', () => {
      runner.runSchematic('ng-add', { project: 'testApp' }, sourceTree);
      expect(runner.tasks.length).toBe(2);
      expect(runner.tasks.some(task => task.name === 'run-schematic')).toBe(
        true,
      );
      expect(runner.tasks.some(task => task.name === 'node-package')).toBe(
        true,
      );
    });

    it('will not install dependencies if skipInstall is true', () => {
      runner.runSchematic(
        'ng-add',
        { project: 'testApp', skipInstall: true },
        sourceTree,
      );
      expect(runner.tasks.length).toBe(1);
      expect(runner.tasks.some(task => task.name === 'run-schematic')).toBe(
        true,
      );
    });
  });

  describe('when not in an angular workspace', () => {
    it('should throw', () => {
      let errorMessage;
      try {
        runner.runSchematic('ng-add', {}, Tree.empty());
      } catch (e) {
        errorMessage = e.message;
      }
      expect(errorMessage).toMatch(/Could not find Angular workspace configuration/);
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们正在测试任务的数量，以及哪些任务正在运行。我们还将测试如果没有找到`angular.json`原理图会抛出。

我们还应该测试文件是由`ng-add-setup`创建和更新的。

```
// ng-add-setup/index.spec.ts
const collectionPath = path.join(__dirname, '../collection.json');

describe('ng-add-setup', () => {
  const workspaceOptions = {
    name: 'workspace',
    newProjectRoot: 'projects',
    version: '8.0.0',
  };

  const appOptions = {
    name: 'testApp',
    inlineStyle: false,
    inlineTemplate: false,
    routing: false,
    style: 'css',
    skipTests: false,
    skipPackageJson: false,
  };

  const runner = new SchematicTestRunner('schematics', collectionPath);

  let sourceTree: UnitTestTree;

  describe('CSS', () => {
    beforeEach(async () => {
      sourceTree = await runner
        .runExternalSchematicAsync(
          '@schematics/angular',
          'workspace',
          workspaceOptions,
        )
        .toPromise();
      sourceTree = await runner
        .runExternalSchematicAsync(
          '@schematics/angular',
          'application',
          appOptions,
          sourceTree,
        )
        .toPromise();
    });

    it('adds and updates files', () => {
      const tree = runner.runSchematic(
        'ng-add-setup',
        { project: 'testApp' },
        sourceTree,
      );

      expect(tree.files).toContain('/projects/testApp/tailwind.config.js');
      expect(tree.files).toContain('/projects/testApp/webpack-prod.config.js');
      expect(tree.files).toContain('/projects/testApp/webpack.config.js');
      expect(tree.files).toContain('/projects/testApp/src/styles.css');

      const stylesFile = tree.readContent('/projects/testApp/src/styles.css');

      expect(stylesFile).toContain('@tailwind base;');
      expect(stylesFile).toContain('@tailwind components;');
      expect(stylesFile).toContain('@tailwind utilities;');

      const workspace = JSON.parse(tree.readContent('angular.json'));
      const app = workspace.projects.testApp;

      expect(app.architect.build.builder).toBe(
        '@angular-builders/custom-webpack:browser',
      );

      expect(app.architect.build.options.customWebpackConfig.path).toBe(
        '/projects/testApp/webpack.config.js',
      );

      expect(
        app.architect.build.configurations.production.customWebpackConfig.path,
      ).toBe('/projects/testApp/webpack-prod.config.js');

      expect(app.architect.serve.builder).toBe(
        '@angular-builders/custom-webpack:dev-server',
      );
    });
  });

// test SCSS, SASS, etc
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们正在创建一个新项目，并运行我们的原理图。然后，我们测试这些文件是否已经被创建和更新，正如预期的那样。

## 本地测试

为了在本地测试原理图，我们可以创建一个新的角度项目。(`ng new <project_name>`)。一旦我们进入我们的项目，我们应该运行`ng add <path_to_collection>`。该命令将安装集合，并开始执行`ng-add`原理图。
`

# 最后的话

*   你可以在[这个库](https://github.com/flakolefluk/tailwindcss-schematics)中找到代码
*   今天就可以试试！。(预发布，使用风险自担)
*   `ng add @flakolefuk/tailwind-schematics.`
*   请随意投稿

## 相关博文/资源

*   [https://medium . com/@ bajz at . tamas/setup-tail wind-CSS-in-angular-8-project-6e 9264 e 97329](https://medium.com/@bajzat.tamas/setup-tailwind-css-in-angular-8-project-6e9264e97329)
*   [https://medium . com/@ mallim/tailwindcss-on-angular-8-A8 a 96 fef 7581](https://medium.com/@mallim/tailwindcss-on-angular-8-a8a96fef7581)
*   [https://tailwindcss.com/docs/using-with-preprocessors](https://tailwindcss.com/docs/using-with-preprocessors)

这篇文章是由 Ignacio Falk 写的，他是 T2 的一名软件工程师。

你可以在推特上关注他，电话是 [@flakolefluk](https://dev.to/flakolefluk) 。

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](https://thisdot.co) 查看我们的服务列表。