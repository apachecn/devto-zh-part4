# 创造。带有 Vue.js 的 docx 文件

> 原文：<https://dev.to/omarmorales/create-docx-files-with-vue-js-3701>

最近我一直在做一个基于 web 的应用程序(前端使用 Vue.js ),最终结果是一个详细的报告。一切都很好，但是当我与应用程序的所有者分享最终结果时，他们认为如果他们能够将所有这些信息下载到某种格式的. docx 文档中，那该多好啊。我以前从来没有做过这样的事情，但我想这应该不会太难。所以我开始在网上寻找一些 javascript 工具来帮助我解决这个需求，所以我找到了 [docx](https://docx.js.org) ，一个很容易生成的工具。带有 JS/TS 的 docx 文件，带有可靠的 API。
你可以在任何 Javascript 库(React.js，Angular，Vue.js)上使用 docx，但这次我想分享我在 Vue.js 上使用 docx 的经验，所以如果有人和我处于相同的情况，可以用这篇文章作为参考。话不多说，我们开始吧！

首先你必须在你的组件中安装 docx 和 [FileSaver.js](https://github.com/eligrey/FileSaver.js) (在客户端保存文件的解决方案)。我用了 npm，但是你也可以用纱线或者任何你想要的东西。

```
 npm install --save docx file-saver 
```

Enter fullscreen mode Exit fullscreen mode

然后将包导入到组件中，如下所示:

```
 <script>
  import { WidthType, BorderStyle, Document, Paragraph, Packer, TextRun } 
  from "docx";
  import { saveAs } from 'file-saver';
  export default {
    components: {
      Document, Paragraph, Packer, TextRun, saveAs, BorderStyle, WidthType
    },
    data: () => ({

    }),
    methods: {

    },
    created(){

    }
  }
 </script> 
```

Enter fullscreen mode Exit fullscreen mode

然后，创建一个方法，这样当用户单击按钮时。docx 文件自动生成。

```
 <template>
  <div class="btn btn-link float-right" @click="exportDocx">
    <i class="far fa-file-word"></i>
    Generate .docx file
  </div>

  </template>
  <script>
  import { WidthType, BorderStyle, Document, Paragraph, Packer, TextRun } 
  from "docx";
  import { saveAs } from 'file-saver';
  export default {
    components: {
      Document, Paragraph, Packer, TextRun, saveAs, BorderStyle, WidthType
    },
    data: () => ({
      state: {
        name: 'San Luis Potosi'
      } 
    }),
    methods: {
      // Create a new Document an save it in a variable
      let doc = new Document();

      // Add paragraph in the document
      let title = new Paragraph(`Detailed Report for ${this.state.name}`).title().center();

      // To export into a .docx file
      let packer = new Packer();

      packer.toBlob(doc).then(blob => {
        saveAs(blob, "detailed_report.docx");

         // using sweet alert for notification
         toast({
          type: 'success',
          title: 'Document created!'
         })
      });

    },
    created(){

    }
  }
 </script> 
```

Enter fullscreen mode Exit fullscreen mode

docx 允许您添加文本、图像、表格、项目符号、编号等等...在我的例子中，我使用文本作为标题和内容；添加了 base64 图像；要点来组织数据和表格，所以我会给你一个如何用这些资源创建文档的例子，如果你需要稍微复杂一点的东西，你可以随时查看[文档](https://docx.js.org)来获得更多信息。

```
 <template>
  <div class="btn btn-link float-right" @click="exportDocx">
    <i class="far fa-file-word"></i>
    Generate .docx file
  </div>

  </template>
  <script>
  import { WidthType, BorderStyle, Document, Paragraph, Packer, TextRun } 
  from "docx";
  import { saveAs } from 'file-saver';
  export default {
    components: {
      Document, Paragraph, Packer, TextRun, saveAs, BorderStyle, WidthType
    },
    data: () => ({
      state: {
        name: 'San Luis Potosi',
        map: 'data:image/png;base64',
        municipalities: [
          {name:'San Luis Potosi', population: 824000}, 
          {name:'Rio Verde', population: 160000},
          {name:'Cd Valles', population: 176000},
          {name:'Matehuala', population:82726}
        ],
        tourist_attractions: [
          'Tamtoc', 'Sótano de las Golondrinas', 'Cascada de Tamul' 
        ]
      }
    }),
    methods: {
      // Create a new Document an save it in a variable
      let doc = new Document();

      // Add paragraph in the document
      doc.addParagraph(new Paragraph(`Detailed Report for ${this.state.name}`).title().center());

      // Add heading for map
      doc.addParagraph(new Paragraph(`State Map`).heading1().thematicBreak().center());

      // Add map image
      doc.createImage(this.state.map, 600, 250, {});

      // Add heading for attractions
      doc.addParagraph(new Paragraph(`Tourist Attractions`).heading1().thematicBreak().center());

      // Bullet points
      for (let attraction of this.state.tourist_attractions) {
        doc.addParagraph(new Paragraph(attraction).bullet());
      }

      // Add heading for municipalities
      doc.addParagraph(new Paragraph(`Municipalities`).heading1().thematicBreak().center());

      // Create table
      let municipalities_table = doc.createTable({
        rows: this.state.municipalities.length+1,
        columns: 2,
        width: 100,
        widthUnitType: WidthType.AUTO,
        columnWidths: [2934, 2934],
      });
      municipalities_table.getCell(0, 0).addParagraph(new Paragraph("Name"));
      municipalities_table.getCell(0, 1).addParagraph(new Paragraph("Population"));

      for (let [index, municipality] of this.state.municipalities.entries()) {
        municipalities_table.getCell(index+1, 0).addParagraph(new Paragraph(municipality.name));
        municipalities_table.getCell(index+1, 1).addParagraph(new Paragraph(municipality.population));
      }

      // To export into a .docx file
      let packer = new Packer();

      packer.toBlob(doc).then(blob => {
        saveAs(blob, "detailed_report.docx");

         // using sweet alert for notification
         toast({
          type: 'success',
          title: 'Document created!'
         })
      });

    },
    created(){

    }
  }
 </script> 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，创建一个简单而强大的组件。当用户单击按钮时，会生成 docx 文件。

真的希望这篇文章是有用的，如果是这样，请分享并告诉我你是否知道另一种方法，直到下一篇文章。