# 用 JavaScript 创建组织结构图

> 原文：<https://dev.to/syncfusion/create-organizational-charts-in-javascript-33jb>

[组织结构图](https://en.wikipedia.org/wiki/Organizational_chart)是组织结构的图示。它们的目的是说明组织内职位的关系和相对等级。组织结构图也可以被称为**组织结构图**、**组织结构图**或**层级树或图表**。

您是否正在寻找一个 JavaScript 库来创建一个**组织结构图**而不费力并且非常有效？那么你来对地方了，因为 Syncfusion [**JavaScript 图库**](https://www.syncfusion.com/javascript-ui-controls/js-diagram) 通过简单的配置设置帮助你做到这一点。在这篇博客中，我将向您介绍使用 Syncfusion 图表库创建组织结构图的步骤。

## 从简单的组织结构图开始

让我们使用 [Syncfusion 图表库](https://www.syncfusion.com/javascript-ui-controls/js-diagram)创建一个简单的组织结构图，如下图所示。图表控件有一个高性能的布局算法来排列父元素和子元素。它可以在两到三秒钟内加载和布局多达 1，000 个节点和 1，000 个连接器。

图表控件支持可视化数据源中的组织结构图。这避免了必须手动向各个节点提供数据。数据源应该以 JSON 格式定义，并配置到图表中。

[![A Simple Org Chart](img/aabb7575a9e92e11e7ddd794038f1415.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---30GClyH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/05/Org-Chart.png)

*   请参考我们的帮助[文档](https://ej2.syncfusion.com/documentation/diagram/getting-started/?no-cache=1)来了解更多关于在 JavaScript 应用程序中集成图表控件所需的依赖脚本和主题文件。
*   将雇员信息定义为 JSON 数组
*   创建图表控件的实例。
*   将数据配置到所创建的图实例的 **dataSourceSettings** 属性中。
*   在图表控件中，屏幕截图中显示的矩形框表示节点，线条表示连接器。矩形框内的文本代表注释。
*   当每个节点被创建时，**data source settings****do binding**方法将被触发。您可以使用此方法来配置节点内部的员工信息。
*   因为所有节点都有相同的宽度和高度，所以可以通过**getnodefaults**方法定义所有节点的通用格式。同样，您可以通过 **getConnectorDefaults** 方法定义连接器的通用格式属性。
*   配置完数据源后，定义**布局类型**自动排列父子节点位置。

下面的代码示例包括前面讨论的所有步骤。完整样本可在此获得:[https://stackblitz.com/edit/1woeqw-u8dzc7](https://stackblitz.com/edit/1woeqw-u8dzc7)。

```
import {data} from './datasource.js';

var items = new ej.data.DataManager(data);

var diagram = new ej.diagrams.Diagram({
    width: "1000px",
    height: "600px",
    dataSourceSettings: {
        // set the unique field from data source
        id: 'id', 
        // set the field which is used to identify the reporting person
        parentId: 'manager', 
        // define the employee data
        dataManager: items,
        doBinding: function (node, data) {
            // You will get the employee information in data argument and bind that value directly to node's built-in properties.
            node.annotations = [{ content: data.role }];
            node.style = { fill: data.color };
        }
    },
    layout: {
        // set the layout type
        type: 'OrganizationalChart'
    },
    // set the common settings for node and connector
    getNodeDefaults: nodeDefaults,
    getConnectorDefaults: connectorDefaults,
    // hide the gridlines in the diagram
    snapSettings: { constraints: ej.diagrams.SnapConstraints.None }
});
diagram.appendTo('#diagram');

function nodeDefaults(node) {
    node.annotations[0].style.color = "white";
    node.width = 120; 
    return node;
}

function connectorDefaults(connector) {
    connector.type = 'Orthogonal';
    connector.targetDecorator = { shape: 'None' };
    return connector;
} 
```

## 功能和示例

### 组织结构图形状自定义

您可以轻松创建不同类型的组织结构图形状，并使用自定义 UI 设计来可视化它们。

setNodeTemplate 方法允许您为组织结构图定义一个自定义模板。有关详细信息，请参考下面的代码示例。

```
//Funtion to add the Template of the Node.
function setNodeTemplate(obj, diagram) {
    // create the stack panel
    var content = new ej.diagrams.StackPanel();
    content.id = obj.id + '_outerstack';
    content.orientation = 'Horizontal';
    content.style.strokeColor = 'gray';
    content.padding = { left: 5, right: 10, top: 5, bottom: 5 };

    // create the image element to map the image data from the data source
    var image = new ej.diagrams.ImageElement();
    image.id = obj.id + '_pic';
    image.width = 50; image.height = 50; image.style.strokeColor = 'none';
    image.source = obj.data.ImageUrl;

    // create the stack panel to append the text elements.
    var innerStack = new ej.diagrams.StackPanel();
    innerStack.style.strokeColor = 'none';
    innerStack.margin = { left: 5, right: 0, top: 0, bottom: 0 };
    innerStack.id = obj.id + '_innerstack';

    // create the text element to map the Name data from the data source
    var text = new ej.diagrams.TextElement();
    text.style.bold = true;
    text.id = obj.id + '_name';
    text.content = obj.data.Name;

    // create the text element to map the designation data from the data source
    var desigText = new ej.diagrams.TextElement();
    desigText.id = obj.id + '_desig';
    desigText.content = obj.data.Designation;

    // append the text elements
    innerStack.children = [text, desigText];

    // append the image and inner stack elements
    content.children = [image, innerStack];
    return content;
} 
```

[![Customized Org Chart](img/837b09085dc0d98327fcc9089ca02869.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zauGUCWJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/05/Org-Chart-Customization.png) 

<figure>

<figcaption>定制组织结构图</figcaption>

</figure>

完整的示例代码可以在这里找到:[https://stackblitz.com/edit/1woeqw-5ahyy6](https://stackblitz.com/edit/1woeqw-5ahyy6)

### 展开和折叠

使用节点的**展开**和**折叠图标**属性实现树形组织结构图的展开和折叠功能。下面的代码示例阐释了这一点。

```
function nodeDefaults(node) {
  ….
  node.expandIcon = { shape: 'Minus' };
  node.collapseIcon = { shape: 'Plus' };
  return node;
} 
```

[![Expand and Collapse Org Chart](img/eb1a0fff76f4cc6d0dad3f31bc266677.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ggNfhBbf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/05/Expand-and-Collapse-Org-Chart.gif) 

<figure>

<figcaption>展开和折叠组织结构图</figcaption>

</figure>

此功能的完整示例可在此处获得:[https://stackblitz.com/edit/1woeqw](https://stackblitz.com/edit/1woeqw)。

### 拖放(交互式编辑层次结构)

通过将子节点或父节点拖放到所需位置，您可以轻松地以交互方式修改组织结构图。要编辑组织结构图的层次结构，请在图表控件的 **drop** 方法中添加以下代码。

```
var diagram = new ej.diagrams.Diagram({
    ...    
    // trigger the drop event to update the org chart structure when drag and drop the child node.
    drop: drop
  });

function drop(args) {
    if (args.target && args.target instanceof ej.diagrams.Node) {
        var connector = diagram.getObject(args.element.inEdges[0]);
        connector.sourceID = args.target.id;
        diagram.dataBind();
        diagram.doLayout();
        // update your local data source when modifying org chart structure.
        updateDataSource(args.element, args.target);
    }
}

function updateDataSource(source, target) {
    var updateData = data.find(function(element) {
        return element.id === source.data.id;
    });
    if(updateData) {
      updateData.manager = target.data.id;
    }
} 
```

[![Drag and Drop nodes in Org Chart](img/1da307ba37b5cbc30b283a73aed6357f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H5_kSpn_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/05/Drag-and-Drop-Org-Chart.gif) 

<figure>

<figcaption>在组织结构图中拖拽节点</figcaption>

</figure>

完整样本可在此获得:[https://stackblitz.com/edit/1woeqw-a5dxag](https://stackblitz.com/edit/1woeqw-a5dxag)。

### 助手

图表控件支持在组织结构图中定义助手。助手是与父节点有不同关系的子项。它们被布置在树的专用部分。

布局的 **getLayoutInfo** 方法将在布局上定位每个节点时被触发，它将节点和树信息作为参数。使用此方法将节点指定为辅助节点。请参考下面的代码示例进行演示。

```
var diagram = new ej.diagrams.Diagram({
    ...
    layout: {
        type: 'OrganizationalChart',
        getLayoutInfo: function (node, options) {
            // you can get the children belongs to the parent node and decide the assistants from that children.
            if (node.data.role === 'General Manager') {
                options.assistants.push(options.children[2]);
                options.children.splice(2, 1);
            }
        }
    },    
}); 
```

[![Assistants in Org Chart](img/8604b0bfe42be329d95eda10d32ae85f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lZqgUjKh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/05/Assistants-Org-Chart.png) 

<figure>

<figcaption>组织结构图中的助手</figcaption>

</figure>

完整样本可在此获得:[https://stackblitz.com/edit/1woeqw-a81wbz](https://stackblitz.com/edit/1woeqw-a81wbz)。

### 方向和对齐

您可以根据需要以不同的方向排列组织结构图，例如从上到下、从左到右、从右到左和从下到上。组织结构图的叶级节点可以水平或垂直对齐。节点可以水平左对齐、右对齐或居中对齐，也可以垂直上对齐、下对齐或中间对齐。您可以自定义每个级别之间的水平和垂直间距。

请参考下面的代码示例来设置方向和自定义叶级节点对齐。

```
var diagram = new ej.diagrams.Diagram({
    ...
    layout: {
      type: 'OrganizationalChart',
      // set the layout orientation
      orientation: 'TopToBottom',
      // set the spacing between each level in horizontal and vertical direction
      horizontalSpacing: 50, verticalSpacing: 50,
      getLayoutInfo: function (node, options) {
          // set the leaf-level node alignment
          if (!options.hasSubTree) {
              options.orientation = 'Horizontal';
              options.type = 'Center';
          }
      }  
    },    
}); 
```

[![Orientation & Alignment in Org Chart](img/abf4d471f7857c4afdae6a9156228a43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sbLk6Nv6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/05/Orientation-Alignment-Org-Chart.png) 

<figure>

<figcaption>定位&在组织结构图中对齐</figcaption>

</figure>

完整样本可在此获得:[https://stackblitz.com/edit/1woeqw-o8njkk](https://stackblitz.com/edit/1woeqw-o8njkk)。

### 缩放和平移

在小屏幕上查看大型组织结构图可能相当困难。缩放和平移支持有助于提供更好的图表视图。

使用 Ctrl +鼠标滚轮操作来放大和缩小图表。若要激活图表控件中的缩放和平移支持，请使用下面的代码示例。

```
var diagram = new ej.diagrams.Diagram({
    // enable the pan tool
    tool: ej.diagrams.DiagramTools.ZoomPan
}); 
```

[![Zoom and pan org chart](img/364a47ff5dabe0ecc01daa433a78268c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ONBwctt5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/05/zoom-and-pan.gif) 

<figure>

<figcaption>缩放和平移组织结构图</figcaption>

</figure>

完整样本可在此获得:[https://stackblitz.com/edit/a2qdyg](https://stackblitz.com/edit/a2qdyg)。

### 出口

您可以轻松地将图表导出为不同的图像格式，如 PNG、JPEG、BMP 和 SVG。

请参考下面的代码示例和演示链接来探索这个特性。

```
diagram.exportDiagram({format: 'PNG', fileName: 'OrgChart'}); 
```

完整样本可在此获得:[https://stackblitz.com/edit/1woeqw-zcjbss](https://stackblitz.com/edit/1woeqw-zcjbss)。

### 惰性装载

在图表中加载非常大的数据集可能非常耗时。使用延迟加载 UI 虚拟化技术来仅加载位于视口中的对象。这允许图表在一秒钟内加载和显示大量数据。

要启用虚拟化功能，请使用下面的代码示例。

```
var diagram = new ej.diagrams.Diagram({
    // enable virtualization
    constraints: ej.diagrams.DiagramConstraints.Default | ej.diagrams.DiagramConstraints.Virtualization,
});
diagram.appendTo('#diagram'); 
```

完整样本可在此获得:[https://stackblitz.com/edit/hnqutl](https://stackblitz.com/edit/hnqutl)。

## 结论

在这篇博文中，我们看到了如何使用 Syncfusion JavaScript 图表库创建和定制组织图表。如果你想试试图片库，你可以下载我们的[免费试用版](https://www.syncfusion.com/downloads/essential-js2)。你可以访问 [GitHub](https://github.com/syncfusion/ej2-javascript-ui-controls) 中的图库源码，查看我们的[现场演示](https://ej2.syncfusion.com/demos/#/material/diagram/default-functionalities.html)和[文档](https://ej2.syncfusion.com/documentation/diagram/getting-started/?no-cache=1)获取详细解释。

如果你有任何问题，请在下面的评论区告诉我们。您也可以通过我们的[支持论坛](https://www.syncfusion.com/forums)、[直接跟踪](https://www.syncfusion.com/support/directtrac/incidents/)或[反馈门户](https://www.syncfusion.com/feedback/javascript)联系我们。我们随时乐意为您提供帮助！

帖子[用 JavaScript 创建组织结构图](https://www.syncfusion.com/blogs/post/create-organizational-charts-in-javascript.aspx)首先出现在[的 Syncfusion 博客](https://www.syncfusion.com/blogs)上。