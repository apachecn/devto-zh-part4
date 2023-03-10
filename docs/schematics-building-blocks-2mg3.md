# 示意图:构建模块

> 原文：<https://dev.to/thisdotmedia/schematics-building-blocks-2mg3>

## [T1】简介](#intro)

Angular 生态系统为开发人员提供的最强大的工具之一是 Angular CLI。您是否曾经想过，每次点击`ng generate`命令时，组件、服务和许多其他组件是如何创建的？CLI 使用 schematics，特别是默认的 Schematics 集合`@schematics/angular`。在本文中，我们将探索使用 schematics 时的一些关键概念，了解其基本操作，并使用 schematics CLI 从头构建我们自己的逻辑示意图集合。

## 树

Schematics 让我们在虚拟文件系统上操作，称为`Tree`。运行原理图时，我们将能够对其进行一系列转换(创建、更新或删除文件)，并最终应用(或不应用)这些更改。

## 创建我们的第一个原理图

要开始创建我们的第一个逻辑示意图，我们将从安装 Schematics CLI 开始，这将有助于我们搭建逻辑示意图集合。

```
// install CLI
npm install -g @angular-devkit/schematics-cli

//create collection
schematics blank my-collection // or schematics blank --name=my-collection 
```

Enter fullscreen mode Exit fullscreen mode

`schematics`命令有两种工作方式，这取决于它的使用场合。如果您不在 schematics 项目文件夹中，它将创建一个具有基本结构的新文件夹并安装其所有依赖项，否则，它将向集合中添加一个新的逻辑示意图。
让我们来看看我们的项目结构。

[![](img/de146561eb0758a85f15b134a46e2777.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dC6qnK5M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7a5zmp5g4datyh85ka7y.png)

`collection.json`包含我们的 schematics 集合信息，展示集合将具有的 schematics，并链接到适当的工厂方法。在这个文件中可以进行更多的配置，比如添加别名，但是我们将在后面讨论。

```
{  "$schema":  "../node_modules/@angular-devkit/schematics/collection-schema.json",  "schematics":  {  "my-collection":  {  "description":  "A blank schematic.",  "factory":  "./my-collection/index#myCollection"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

创建新集合时，默认原理图与该集合同名。原理图应该至少导出一个规则工厂函数。我们的新原理图有一个简短的描述和一个包含原理图的文件路径，后跟一个 hashtag 和调用时使用的工厂函数。

```
// src/my-collection/index.ts
import { Rule, SchematicContext, Tree } from "@angular-devkit/schematics";

export function myCollection(_options: any): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

`index.ts`导出单个函数，一个规则工厂函数。一个`Rule`是一个函数，给定一个`Tree`和一个`SchematicContext`，将返回一个新的`Tree`。
在上面的示例中，没有应用任何转换。

> 您可以从单个文件中导出多个函数，也可以导出单个默认函数，并省略`collection.json`上工厂路径的最后一部分。

## 建造和运行

在运行原理图之前，我们需要构建集合。为了构建和运行我们的原理图，我们将使用两个命令。

```
// build
npm run build

// run
schematics .:my-collection 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们使用`npm run build`构建应用程序，然后使用 Schematics CLI 运行原理图。我们告诉 CLI 运行当前收藏文件夹中的 my-collection(`schematics \<path\>:\<schematic-name\>`)。

> 记得在测试之前编译你的集合。我建议在开发时运行带有`watch`标志的命令。(`npm run build -- --watch`)

## 测井

目前，我们的原理图没有做任何事情。日志信息可以为用户提供有用的信息，或者在调试时帮助我们。让我们更改原理图来记录一些信息。

```
// src/my-collection/index.ts
export function myCollection(_options: any): Rule {
  return (tree: Tree, context: SchematicContext) => {
    context.logger.info('Info message');
    context.logger.warn('Warn message');
    context.logger.error('Error message');

    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在运行原理图(记住先构建)，我们将得到一个漂亮的彩色输出。

[![](img/6a8cc209b5f606d916e7134b2a0ebc66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nLS1mqSn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9zayvcdae0lsi02hsvl6.png)

## 创建文件

现在我们知道如何记录一些信息，我们应该开始修改我们的树。我们将首先创建一个原理图来添加一个新文件。

```
schematics blank create-file 
```

Enter fullscreen mode Exit fullscreen mode

我们的新原理图已经添加到集合中，您会注意到创建了一个新文件夹，并且修改了`collection.json`以包含新原理图。我们现在可以更新 createFile 方法来修改树。

```
// src/create-file/index.ts
import { Rule, SchematicContext, Tree } from "@angular-devkit/schematics";

export function createFile(_options: any): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    tree.create("test.ts", "File created from schematic!");
    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的规则是获取一棵树，然后在该树的根部创建一个新文件`test.ts`,然后返回修改后的树。

构建并运行。

```
schematics .:create-file 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/29800aaa28427580f549b20c38debb8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4VIVaDBw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o0bujs9i1go8tw01jxcd.png)

成功？如果你查看当前文件夹，你将找不到期望的文件。这是因为当从相对路径调用时，我们的原理图将默认在调试模式下运行。这意味着它不会对文件系统进行修改。要进行更改，将`--debug=false`选项添加到命令中。再试一次。`test.js`最终被创建，其中包含所需的内容。请在继续之前删除该文件。

> 如果我们再次运行该命令，它将会失败，因为`create`不会覆盖一个已存在的文件。

## 自变量和模式

目前我们的示意图非常有限。无论如何，它总是创建相同的文件。如果能给它传递一些参数不是更好吗？为了做到这一点，我们将定义一个模式。让我们在 create-file 原理图文件夹中创建一个`schema.json`文件。我们还将创建一个接口`schema.ts`,它与我们在。json 文件。

```
{  "$schema":  "http://json-schema.org/schema",  "id":  "my-collection-create-file",  "title":  "Creates a file using the given path",  "type":  "object",  "properties":  {  "path":  {  "type":  "string",  "description":  "The path of the file to create."  }  },  "required":  ["path"]  } 
```

Enter fullscreen mode Exit fullscreen mode

```
// src/create-file/schema.ts
export interface CreateFileOptions{
  path:string;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在`schema.json`上声明了一个名为`path`的新参数，并将其命名为`required`。
让我们将模式和接口添加到我们的原理图函数和集合模式中。

```
import { Rule, SchematicContext, Tree } from '@angular-devkit/schematics';
import { CreateFileOptions } from './schema';

export function createFile(options: CreateFileOptions): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    tree.create(options.path, "File created from schematic!");
    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

```
{  "$schema":  "../node_modules/@angular-devkit/schematics/collection-schema.json",  "schematics":  {  "my-collection":  {  "description":  "A blank schematic.",  "factory":  "./my-collection/index#myCollection"  },  "create-file":  {  "description":  "A blank schematic.",  "factory":  "./create-file/index#createFile",  "schema":  "./create-file/schema.json"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

通过创建一个接口，我们将类型推断添加到了我们的原理图工厂函数的选项参数中。必须将模式属性添加到`collection.json`中的示意图声明中，并带有模式文件的路径。
如果未输入`path`参数，该模式将告知原理图 CLI 失败。

让我们建立并运行它。

```
schematics .:create-file 
```

Enter fullscreen mode Exit fullscreen mode

如果我们运行原理图而不传递路径参数，它将会失败。

[![](img/ff69425cc743535cfea9ee535f5f2fb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gNxIb6tM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/os0i7hj8heetvammuq2f.png)

让我们传递一个参数再运行一次。

```
schematics .:create-file --path=test-path.ts 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/e970d96ddfe299fd939f3a782243de8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BoChlaD7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ebv20l6l34w2t5dr1svj.png)

即使模式和接口不是必需的。它们提供有用的验证、解析和类型检查。当使用`--help`标志运行时，`ng generate`命令也将读取模式并显示可用的参数。

## 提示用户和别名

可能很难记住一个原理图能得到的所有论点。我们可以通过提示所需的参数来使我们的原理图更加用户友好。我们还可以通过在原理图中添加别名来缩短命令。我们将通过向我们的模式添加一些属性来做到这一点。

首先，让我们将`x-prompt`添加到`schema.json`中的 path 属性中。如果缺少参数，将提示用户输入其值。

```
{  "$schema":  "http://json-schema.org/schema",  "id":  "my-collection-create-file",  "title":  "Creates a file using the given path",  "type":  "object",  "properties":  {  "path":  {  "type":  "string",  "description":  "The path of the file to create.",  "x-prompt":  "Enter the file path:",  }  },  "required":  ["path"]  } 
```

Enter fullscreen mode Exit fullscreen mode

要创建别名，将别名属性添加到`collection.json`
中的原理图

```
{  "$schema":  "../node_modules/@angular-devkit/schematics/collection-schema.json",  "schematics":  {  "my-collection":  {  "description":  "A blank schematic.",  "factory":  "./my-collection/index#myCollection"  },  "create-file":  {  "description":  "A blank schematic.",  "factory":  "./create-file/index#createFile",  "schema":  "./create-file/schema.json",  "aliases":  ["cf"]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以使用新的别名`cf`运行我们的原理图，如果我们忘记添加`path`参数，系统会提示我们添加。

```
schematics .:cf 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/ae4f7d7fc4c26e3eb7d721462bf631e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sXY_abyH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ezpdjdlv17lzs5amjna.png)

## 模板

传递参数来改变要创建的文件的路径是没问题的，但是我们的内容总是相同的。将内容作为参数传递是不实际的，因为它会变得非常复杂。幸运的是，我们可以在处理这类内容时创建模板。模板只不过是占位符文件，将在我们的树中被复制、移动或修改。让我们创建一个新的原理图，并应用一些我们以前见过的概念。

```
schematics blank create-from-template 
```

Enter fullscreen mode Exit fullscreen mode

`collection.json`(部分)

```
{  "$schema":  "../node_modules/@angular-devkit/schematics/collection-schema.json",  "schematics":  {  "create-from-template":  {  "description":  "A blank schematic.",  "factory":  "./create-from-template/index#createFromTemplate",  "schema":  "./create-from-template/schema.json",  "aliases":  ["cft"]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

```
// src/create-from-template/schema.ts
export interface CreateFromTemplateOptions {
  folder: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
//  src/create-from-template/schema.json  {  "$schema":  "http://json-schema.org/schema",  "id":  "my-collection-create-from-template",  "title":  "Creates files in the given folder",  "type":  "object",  "properties":  {  "folder":  {  "type":  "string",  "description":  "The destination folder of the files to create.",  "x-prompt":"Enter the destination folder:"  }  },  "required":  ["folder"]  } 
```

Enter fullscreen mode Exit fullscreen mode

要创建我们的模板，我们必须在我们的原理图文件夹中创建一个`/files`文件夹，并放置要复制的文件。

[![](img/6852e6547be7a06c15360d4af5ad92d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c_mNhnGt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9kdpw9j5w3jmqra7zb67.png)

> 您可以使用任何文件夹名称，只要它被编译器忽略。默认情况下`/files`被忽略

我们在文件夹中添加了两个文件。现在我们可以在原理图中使用它们。

```
// src/create-from-template/index.ts
import {
  Rule,
  SchematicContext,
  Tree,
  Source,
  url,
  mergeWith,
  move,
  apply
} from "@angular-devkit/schematics";
import { CreateFromTemplateOptions } from "./schema";
import { normalize } from "@angular-devkit/core";

export function createFromTemplate(options: CreateFromTemplateOptions): Rule {
  return (tree: Tree, context: SchematicContext) => {
    const source: Source = url("./files");
    const transformedSource: Source = apply(source, [
      move(normalize(options.folder))
    ]);

    return mergeWith(transformedSource)(tree, context);
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了很多事。首先，我们使用返回一个`Source`的`url`函数从文件夹中读取数据。然后，我们将一组规则应用到源文件中的每个文件。在这个例子中，我们从根级别移动我们的文件，在作为参数给定的文件夹中。最后，我们将修改后的源代码与初始树合并。

构建并运行。

[![](img/18658fbbce2b445fd26a64a4847b6ff4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6nQ6slhP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0sw67muc5nq4mvlhcatp.png)

我们的文件已经从`/files`文件夹复制到`/my-folder`

## 动态内容

让我们后退一步，想想原理图的真实例子。当我们使用 Angular CLI 创建组件时，会创建一组文件和一个文件夹。这些文件根据用户的输入改变它们的名称和内容。我们如何才能实现类似的目标？
我们将利用`angular-devkit/schematics`提供的`template`函数，并将其应用于我们的源。

```
// ...imports
export function createFromTemplate(options: CreateFromTemplateOptions): Rule {
  return (tree: Tree, context: SchematicContext) => {
// ...
    const transformedSource: Source = apply(source, [
      template({
        filaname: options.folder,
        ...strings // dasherize, classify, camelize, etc
      }),
      move(normalize(folder))
    ]);

    return mergeWith(transformedSource)(tree, context);
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

将一个对象作为参数，并使它的所有属性对文件名和模板可用。我们正在传递无格式的文件夹名和一组转换字符串的方法。
为了测试这一点，我们将创建两个新文件。

```
// files/__filename@dasherize__.ts
export class <%= classify(filename) %> {
    constructor(){}
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- files/__filename@dasherize__.html -->
<ul>
  <li><%= dasherize(filename) %></li>
  <li><%= camelize(filename) %></li>
  <li><%= capitalize(filename) %></li>
  <li><%= underscore(filename) %></li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

看看这些文件名。`__`是默认的开始和结束分隔符，`@`将把参数(在符号之前)传递给函数(在符号之后)。在本例中，文件名将作为参数传递给 dasherize 函数，返回值后面跟有文件扩展名。
在我们的模板中，我们将继续使用字符串函数和文件名属性。
再次...构建并运行。

```
schematics .:cft --folder=very-complexFolder_name 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/3b50af8a717d400adc0fdc0c44970cc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_EqMVLE1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vsu0dg3mnggf4n21rd88.png)

我们的文件名已经按照我们想要的方式格式化了。让我们看看文件的内容。(您需要运行带有- debug=false 的命令来实际进行更改并能够看到这些更改)

```
<!-- very-complex-flder-name.html -->
<ul>
  <li>very-complex-folder-name</li>
  <li>veryComplexFolderName</li>
  <li>Very-complexFolder_name</li>
  <li>very_complex_folder_name</li>
</ul 
```

Enter fullscreen mode Exit fullscreen mode

```
// very-complex-folder-name.ts
export class VeryComplexFolderName {
    constructor(){}
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的原理图开始变得更有用了！

## 删除文件

接下来是删除文件。

让我们添加一个新的原理图。

```
schematics blank --name=remove-file 
```

Enter fullscreen mode Exit fullscreen mode

`collection.json`

```
{  "$schema":  "../node_modules/@angular-devkit/schematics/collection-schema.json",  "schematics":  {  "remove-file":  {  "description":  "Removes a file",  "factory":  "./remove-file/index#removeFile",  "schema":  "./remove-file/schema.json",  "aliases":  ["rm"]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

`schema.json`

```
{  "$schema":  "http://json-schema.org/schema",  "id":  "my-collection-remove-file",  "title":  "Deletes a file using the given path",  "type":  "object",  "properties":  {  "path":  {  "type":  "string",  "description":  "The path of the file to remove.",  "x-prompt":"Enter the file path:"  }  },  "required":  ["path"]  } 
```

Enter fullscreen mode Exit fullscreen mode

```
// src/remove-file/schema.ts
export interface RemoveFileOptions {
  path: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// src/remove-file/index.ts
import { Rule, SchematicContext, Tree } from '@angular-devkit/schematics';
import { RemoveFileOptions } from './schema';

export function removeFile(options: RemoveFileOptions): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    tree.delete(options.path);
    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

在运行原理图之前，请确保您试图删除的文件存在。请记住，默认情况下，我们将在调试模式下运行，因此不会应用任何更改。

```
schematics .:rm --path=src/collection.json 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/86f2faf34b7554d5cf7a16d12c8fe125.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HOFqJyZ3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lfc077h252v1gt4jfjme.png)

## 更新文件

删除文件似乎比创建文件简单得多。我把文件更新放在最后，因为(在我看来)它涉及最复杂的操作，这取决于什么样的文件和你如何更新它们。它可以简单到在顶部(或底部)添加几行，也可以复杂到必须使用类型脚本语言 AST(抽象语法树)来确定在哪里以及如何执行更新。

让我们创建一个新的原理图。

```
schematics blank overwrite-file 
```

Enter fullscreen mode Exit fullscreen mode

```
// src/overwrite-file
export function overwriteFile(options: OverwriteFileOptions): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    const buffer = tree.read(options.path);
    const content = buffer ? buffer.toString() : '';
    const comment = `// ¯\_(ツ)_/¯\n`;
    if(!content.includes(comment)){
      tree.overwrite(options.path, comment + content)
    }
    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我省略了模式创建和集合更新，因为这与我们对 schematics 的其余部分所做的类似。我们的函数从用户给定的路径读取文件。将该缓冲区转换为字符串，检查是否已经添加了注释，如果没有，则预先添加注释。然后我们用更新的内容覆盖文件。我们可以添加一些检查来避免向. json 文件添加注释(并使其无效)，但这超出了本教程的范围。这不是更新文件的唯一方法。

```
schematics blank update-recorder 
```

Enter fullscreen mode Exit fullscreen mode

```
export function updateRecorder(options: RecorderOptions): Rule {
  return (tree: Tree, _context: SchematicContext) => {

    const comment = '// ᕙ(⇀‸↼‶)ᕗ\n';

    const updateRecorder: UpdateRecorder = tree.beginUpdate(options.path);

    updateRecorder.insertLeft(0, comment);
    updateRecorder.insertLeft(0, comment);
    updateRecorder.insertLeft(0, comment);
    updateRecorder.insertLeft(0, comment);

    tree.commitUpdate(updateRecorder);

    return tree;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

这与我们之前的原理图类似，但工作原理略有不同。首先，我们读取我们的文件，然后我们开始更新插入值(`string`或`Buffer`)到给定位置的左边或右边。这些更改只有在提交后才会应用到树中。
这里有趣的部分是给定的插入值的位置，以及如何确定我们要在哪里进行修改。在这个例子中，这并不重要，因为我们在一开始就添加了注释，但是现在我们将看到一个更复杂的场景。不要忘记构建和运行！

## 使用键入脚本 AST

假设我们想读取一个 typescript 文件，获取在其上声明的第一个接口，并在开头和结尾分别添加一个属性。我们可以试着把所有东西都当成文本来读，然后找到正确的字符。我们可以利用 TypeScript AST 来导航我们的文件，不用考虑字符，而是考虑有意义的节点。

```
schematics blank ts-ast 
```

Enter fullscreen mode Exit fullscreen mode

```
import { Rule, SchematicContext, Tree, SchematicsException } from '@angular-devkit/schematics';
import * as ts from 'typescript';

export function tsAst(options: TsAstOptions): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    const buffer = tree.read(options.path);
    if(!buffer){
      throw new SchematicsException(`File ${options.path} not found.`);
    } 

    const source = ts.createSourceFile(options.path, buffer.toString(), ts.ScriptTarget.Latest, true);
    const nodes = getSourceNodes(source);

    const interfaceDeclaration = nodes.find(n=>n.kind === ts.SyntaxKind.InterfaceDeclaration);
    if(!interfaceDeclaration){
      throw new SchematicsException(`No Interface found`);
    }

    const [openBrace, closeBrace] = [
      interfaceDeclaration!.getChildren().find(n=>n.kind===ts.SyntaxKind.OpenBraceToken),
      interfaceDeclaration!.getChildren().slice().reverse().find(n=>n.kind===ts.SyntaxKind.CloseBraceToken),
    ]

    const text = interfaceDeclaration!.getText();
    let indentation;
    const matches = text.match(/\r?\n\s*/);
    if (matches && matches.length > 0) {
      indentation = matches[0]
    } else {
      indentation= ''
    }

    const recorder = tree.beginUpdate(options.path);
    recorder.insertRight(openBrace!.end, `${indentation}first: string;`);
    recorder.insertLeft(closeBrace!.pos, `${indentation}last: string;`);
    tree.commitUpdate(recorder);
    return tree;
  };
}

// taken from angular schematics. returns an array of Nodes
function getSourceNodes(sourceFile: ts.SourceFile): ts.Node[] {
  const nodes: ts.Node[] = [sourceFile];
  const result = [];

  while (nodes.length > 0) {
    const node = nodes.shift();

    if (node) {
      result.push(node);
      if (node.getChildCount(sourceFile) >= 0) {
        nodes.unshift(...node.getChildren());
      }
    }
  }

  return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们一步一步地检查这个文件。首先我们确保文件存在，否则，我们抛出一个错误。然后使用 typescript 编译器，我们读取文件并从 AST 获取所有节点。
然后我们寻找 InterfaceDeclaration 类型的第一个节点。类型列表很长，但是这个将返回接口的整个声明，直到右括号。对于每个节点，我们得到开始和结束的位置。(还记得`insertLeft`和`insertRight`的方法吗？)
我们还没有到那一步，我们可以从这个孤立的节点上获取更多的信息。我们将从获取它的子节点开始，获取第一个出现的左括号(`{`)和最后一个右括号(`}`)。这些节点还包含开始和结束位置。空白在我们的语法树中没有“意义”，所以我们将采用不同的方法。我们将从接口声明中获取文本，并获取空白作为确定缩进的方法。这是为了美观和可读性，一个新的行就可以了。现在是开始录音的时候了，插入到我们的左大括号的右边和右大括号的左边。请记住，在我们调用`commitUpdate`方法之前，不会提交任何更改。即使我们在左大括号后面插入了一些东西，我们的右大括号位置仍然和以前一样，我们可以安全地插入到它的左边。

创建一个测试文件，并运行原理图进行测试。

```
interface TestInterface {
  aProperty: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

运行原理图后，您应该有一个类似于下面的更新文件。

```
interface TestInterface {
  first:string;
  aProperty: string;
  last:string;
} 
```

Enter fullscreen mode Exit fullscreen mode

成功！

# 最后的话

*   尽管 schematics 在 Angular 生态系统中被广泛使用，但它们并不局限于此。事实上，我们到目前为止所做的一切都是在 angular 项目之外使用的。

*   在本系列的下一部分中，我们将了解任务以及如何测试、扩展逻辑示意图以及如何运行一系列逻辑示意图。

*   在第 3 部分中，我们将创建一个“真实世界”的原理图集合，将 TailwindCSS 添加到 Angular 项目中。

*   你可以在[这个库](https://github.com/flakolefluk/schematics-tutorial)中找到最终代码

## 参考文献

*   [角度文件](https://angular.io/guide/schematics)
*   [CDK 示意图](https://github.com/angular/components/tree/master/src/cdk)
*   [ngrx 原理图](https://github.com/ngrx/platform/tree/master/modules/schematics)

## 相关博文/书籍

*   [示意图:使用 CLI 生成自定义角度代码](https://leanpub.com/angular-schematics)
*   [https://blog . angular . io/schematics-an-introduction-DC 1 DFB C2 a2 b 2](https://blog.angular.io/schematics-an-introduction-dc1dfbc2a2b2)
*   [https://medium . com/@ tomastrajan/total-guide-to-custom-angular-schematics-5c 50 cf 90 CD B4](https://medium.com/@tomastrajan/total-guide-to-custom-angular-schematics-5c50cf90cdb4)
*   [https://brianlove . com/2018/12/11/angular-schematics-tutorial/](https://brianflove.com/2018/12/11/angular-schematics-tutorial/)

这篇文章是由 Ignacio Falk 写的，他是 T2 的一名软件工程师。

你可以在推特上关注他，电话是 [@flakolefluk](https://dev.to/flakolefluk) 。

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](https://thisdot.co) 查看我们的服务列表。