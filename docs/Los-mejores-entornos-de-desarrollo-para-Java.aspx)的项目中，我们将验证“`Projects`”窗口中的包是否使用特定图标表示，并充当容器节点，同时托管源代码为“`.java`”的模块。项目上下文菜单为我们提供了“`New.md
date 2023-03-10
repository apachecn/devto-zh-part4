# Java 套装软体:这些套装软体的用途、用途以及使用方式(含视讯)

> [https://dev . to/campus MVP/Java-en-which-son-to-to-to-use-y-to-use-con-video-11 E5](https://dev.to/campusmvp/paquetes-en-java-que-son-para-que-se-utilizan-y-como-se-usan-con-video-11e5)包

> 发布原创圣弗朗西斯科宪章

包是使用 Java**促进代码模块化的机制**。包可以包含一个或多个接口和类定义，通常作为文件分发。若要使用套件中的项目，您必须使用陈述式`import`将套件汇入目前的程式码模组。

Java 应用程序的功能通常在多个类中实现，这些类之间通常存在不同的关系。**班级分组为较高层单位，**分组，充当**类型隔离区**。每个代码模块在开头使用关键字“`package`”来确定它属于哪个包。如你所知，有了**条款`import`** ，任何代码模块都可以引用其他包中定义的类型。

让我们熟悉构建软件包的过程，以便从其他点引用，还将看到存储项目的“T0”软件包、类和文件系统之间的关系“”。

## 打包

Java 包只需在代码模块的开头添加关键字“`package`”即可生成，该代码模块定义了将成为其一部分的类。在使用“[”netbeans](https://www.campusmvp.es/recursos/post/Los-mejores-entornos-de-desarrollo-para-Java.aspx)的项目中，我们将验证“`Projects`”窗口中的包是否使用特定图标表示，并充当容器节点，同时托管源代码为“`.java`”的模块。项目上下文菜单为我们提供了“`New>Java Package`”选项，这是我们通常用于创建新软件包的选项:

[![Creación de un nuevo paquete en Netbeans](img/cd581cf71ee85d4ee7707ccf3821d315.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Juqagt_o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.campusmvp.es/recursos/image.axd%3Fpicture%3D/2019/3T/NewPackage.png)

> **注意:**:每次使用 NetBeans 创建新项目时，都会建议定义一个与项目同名的新软件包，用于托管代码模块。但是，在复杂项目中，可能需要“T2”创建附加包。

一个包除了包含类和接口等类型的定义外，还可以包含其他包，**从而形成容器的层次结构**。**子包、包含在其他**中的包的名称将由容器标识符后跟句点和子包名称组成。如果存在额外的层，则会添加以句点分隔的各个标识符，从而形成软件包的完整名称。

下图表示了一个名为“`campusmvp`”的包的内容，理论上对应于一个会计应用项目。在该第一层包中，我们有一个称为“`campusmvp.util`”的实用程序子包，它与会计应用程序没有直接联系。`campusmvp.conta`子套件将包含与该应用程式相关联的所有元素，而这些元素又分布在包含模型、检视等类别的第三层套件中。，按照**[MVC](https://es.wikipedia.org/wiki/Modelo%E2%80%93vista%E2%80%93controlador)**建筑模式。各类定义模块，分别命名为`Cuenta.java`、`Movimiento.java`、`Vista.java`等。，则以条款`package`开头，指明**其所属包的全名**:

[![Estructura jerárquica de paquetes](img/852b7874571a9846066d7616ddebc519.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cz2GQl05--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.campusmvp.es/recursos/image.axd%3Fpicture%3D/2019/3T/NestedPackages.png)

> **注意**:属于同一个包的所有类共享一个公共领域，那些没有明确指定另一种可见性的成员将属于该领域。

## 包裹进口

【java 包就像工具箱，每个工具箱都有不同的工具集合，对我们的工作有用，每次我们讨论新项目的开发时都可能需要访问这些工具。正是在这种情况下，我们才会使用条款 **`import`** ，以便**将另一个包的定义导入到当前范围内，并可以按常规程序使用**，创建对象，访问类服务等。

`import`子句可用于导入包中的特定元素，从而提供该元素的名称，后跟该元素的句点和标识符。例如，要导入包中的类`Math``java.lang`，从而可以访问常量`PI`及其提供的数学函数，只需如下行:

```
import java.lang.Math; 
```

导入包时，我们通常会对包中定义的许多类感兴趣。在这种情况下，我们可以使用上述语法分别导入它们，也可以使用以下替代方法。这将使我们能够使用`System`类，以及`System`类、`Thread`类以及`java.lang`包中定义的许多其他类:

```
import java.lang.\*; 
```

有时，像类别`Math`、**一样，我们汇入类别是为了直接存取其静态、常数和方法**，而不是从它们建立物件，这迫使我们不断使用语法`Clase.miembro`，例如`Math.PI`或【t】如果在导入类“`Math`”后，我们只是试图按名称引用这些成员，如下面图的上半部分所示，我们将会得到错误。在这种情况下，我们可以使用语法 **`import static paquete.clase.*`** ，其目的在于**将指定类别的静态成员**包括在当前范围内。这是在下图的下半部分:

[![Importación de paquetes](img/397fe8b2a06a275c7e90e2bc5b0d617f.png "Distintas alternativas para hacer referencia al contenido de paquetes Java")](https://res.cloudinary.com/practicaldev/image/fetch/s--4wsBeVSr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.campusmvp.es/recursos/image.axd%3Fpicture%3D/2019/3T/ImportStatic.png)

## 类、包与文件系统的关系

在 Java 中，**包和类定义与存储包含代码的模块的本地文件系统**之间存在密切关系。使用 NetBeans 等 IDE 时，我们可能会完全忽略这一点，因为您的选项会隐藏这些详细信息。但是，**我们知道这种关系很有意思**，因为这将使我们能够在必要时直接与 JDK 一起工作。

Java 项目中的每个代码模块只能包含一个公共类，该类的名称必须区分大小写，并且必须与模块的名称相匹配。也就是说，类标识符与其所在的文件之间存在直接对应关系。这不会影响可在同一模块中定义的嵌套类和其他非公共类。

包名称还设置包中包含的代码模块所在文件夹的名称。可以使用句点作为标识符不同部分的分隔符来组合包名称。每个缔约方将标识一个子目录，如下图所示:

[![Esquema sistema de archivos](img/8c25759df7e4f3dca24769e894c52efc.png "Esquema de la correspondencia entre el sistema de archivos y la definición de paquetes y clases")](https://res.cloudinary.com/practicaldev/image/fetch/s--KEQJ8pDi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.campusmvp.es/recursos/image.axd%3Fpicture%3D/2019/3T/FilesystemScheme.png)

从命令行直接使用 JDK 进行工作时，请注意编译和运行都将验证“**”是否满足包和类名称与目录结构和文件名“**”之间的匹配。如果在某个位置不符合此条件(如以下影像底部)，则会产生错误。

[![Relación entre paquete y sistema de archivos](img/c343618c62856b6b6bbc39d9259b1bbb.png "La clase Cuenta pertenece al paquete com.campusmvp.contabilidad, denominación que condiciona la jerarquía de carpetas en el sistema de archivos")](https://res.cloudinary.com/practicaldev/image/fetch/s--7TgwSxcJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.campusmvp.es/recursos/image.axd%3Fpicture%3D/2019/3T/PackageFilesystem.png)

> **注意**:一个包的目录结构可以在项目编译后全部存储在 JAR 文件中。它还将包含每个代码模块的类文件。

以下是从校园 MVP 的“T2”[【Java 编程课程】](https://www.campusmvp.es/recursos/catalogo/Product-Desarrollo-de-aplicaciones-con-la-plataforma-Java_231.aspx) 中直接摘录的上述内容的一段解释性实用视频:

[https://www.youtube.com/embed/x4BDc-VGeZY](https://www.youtube.com/embed/x4BDc-VGeZY)

> 发布原创圣弗朗西斯科宪章