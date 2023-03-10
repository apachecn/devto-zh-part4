# 在 JAVA 中解组 marshaled:XML 绑定

> 原文：<https://dev.to/lormenyo/unmarshalling-the-marshalled-xml-binding-in-java-2do0>

在我第一次软件工程实习期间，我的任务是构建一个 USSD 应用程序。由于 USSD 应用程序是非结构化的，因此应用程序的设计是由构建该应用程序的电信公司指定的。电信公司的 API 规定数据将以 XML(可扩展标记语言)的形式传输。因此，我的第一个挑战是弄清楚如何将 XML 转换成 Java 对象，以便能够在我的代码中操作它并向它添加逻辑。

[![USSD APPLICATION SCHEMA](img/2b68cff473402867747ee3f4bc5eb356.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ewu7yXYI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.pinimg.com/originals/66/80/db/6680db61a9dcad820393a709d542b1f1.png) 
有各种 java APIs 用于 XML 绑定；XML 的简单 API(SAX)、文档对象模型(DOM)、JiBx 和 XML 绑定的 Java 架构(JAXB)。

# XML 的简单 API(SAX)

它是 XML 文件的事件驱动解析器。它从上到下依次读取 XML，并向应用程序发送通知，一次一个元素和属性，直到使用回调关闭根元素。当 XML 文档被线性和顺序处理时，通常使用 SAX。因为它只以这种方式处理 XML 文件，所以不能随机访问 XML 的组件，您需要编写自己的代码来操作或跟踪解析的数据。在这里阅读更多关于 SAX 的内容

# 文档对象模型(DOM)

它允许应用程序轻松地访问和更改 XML 文件的样式、结构和内容。它主要用于 XML 中的信息需要多次使用的情况。使用 DOM 解析 XML 文件会返回一个包含 XML 不同元素的树结构，因此它非常适合操作文档结构。幸运的是，DOM 有几个函数可以用来处理文档的内容和结构。点击阅读更多关于 DOM [的信息](https://www.oracle.com/technetwork/java/dom-139036.html)

# JiBX

根据各种消息来源，JiBX 比 JAXB 等更快。JiBX 也用于 XML 绑定，但是与侧重于生成代码的 JAXB 不同，JiBX 侧重于映射数据。它使用绑定定义文档来确定如何将 XML 转换成 Java 对象，反之亦然。
在这里阅读更多关于 JiBX [的信息](http://jibx.sourceforge.net/)

# 用于 XML 绑定的 Java 架构(JAXB)

JAXB 是 web 服务中非常流行的 API。许多人认为它是上面讨论的两个 API 中的标准 API。JAXB 附带 Java 1.6，低于这个版本的 Java 版本需要 JAXB jar 文件，可以在这里找到。我非常同意这一点，因为与 SAX 和 DOM 不同，它们是只解析 XML 文档的低级 API，JAXB 将 XML 元素和属性转换成 Java 对象，反之亦然。这就引出了我的主要观点。JAXB 有两个有用的方法；一个用于将 Java 对象编组为 XML，另一个用于将 XML 解组回 Java 对象。

# 怎么整理出来的？？

基本上，将 Java 对象转换成 XML 称为编组。为了编组 XML，必须有一个 POJO(普通旧 Java 对象)。这个 Java 对象为将要形成的 XML 提供了一个模式。POJO 类的名称成为 XML 的根元素。JAXB 使用注释来表示 XML 的元素。

*@ xmlroot element(name = " namespace ")*–用于定义 XML 的根元素。它在顶级类中使用。name 属性是可选的。使用的默认根元素是 POJO 的类名。

*@ XML attribute*–用于定义根元素的属性。

*@ XML element*–这用于定义 POJO 类的属性，这些属性在编组后将成为 XML 树中根元素的子元素。

#### 代码为 POJO

```
package com.jaxb;

//import javax.xml.bind.annotation.XmlAttribute;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;

@XmlRootElement(name="USSDMenuRequest")
public class USSDMenuRequest{

    private  String sessionId;
    private  String messageid;
    private  String shortCode;
    private   String prompt;
    private   String timeStamp;

    @XmlElement(name="sessionId")
    public void setSessionId(String sessionId){
        this.sessionId = sessionId;
    }

     public String getSessionId(){
        return sessionId;
     }

  public String getMessageid(){
     return messageid;
}

    @XmlElement(name="messageid")
    public void setMessageid(String messageid){
        this.messageid = messageid;
}

    @XmlElement(name="shortCode")
    public void setShortCode(String shortCode){
        this.shortCode = shortCode;
}

    public String getShortCode(){
       return shortCode;
    }

    @XmlElement(name="prompt")
    public void setPrompt(String prompt){
        this.prompt = prompt;
}

    public String getKeyWord(){
       return keyWord;
}

   public String getTimeStamp(){
      return timeStamp;
   }

    } 
```

POJO 类的属性有 getter 和 setter 方法。getter 方法用于访问传递给 XML 子元素的值，而 setter 方法用于更改 XML 子元素的现有值。在上面的 POJO 中，USSDDynMenuRequest 是 XML 树的根元素，messageid、sessionId 等是子元素。

#### 为编组代码

```
-------------------------- MARSHALLING XML  ----------------------------------
  public static void marshalXML() {
    try {       

    System.out.println("Marshalling Java Object to XML-------");

    //creating the JAXB context
        JAXBContext jContext = JAXBContext.newInstance(USSDMenuRequest.class); 

    //create the marshaller object
        Marshaller marshallerObj = jContext.createMarshaller();

        //the XML should be properly indented and formatted
    marshallerObj.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);

    USSDDynMenuRequest Sendrequest= new USSDMenuRequest();

        Sendrequest.setShortCode("124");
        Sendrequest.setPrompt("Start");
    Sendrequest.setMessageid("0244708099");
    Sendrequest.setSessionId("789888886adh");

    //calling the marshall method
    marshallerObj.marshal(Sendrequest, new FileOutputStream("response.xml"));
        }
    catch(Exception e){
         e.printStackTrace();
            }
        } 
```

在上面的代码中，将使用 USSDDynMenuRequest.class 中描述的模式创建 response.xml 文件。XML 子元素的值将与各个类属性的 setter 方法提供的值相同。

# 解组

解组是 XML 到 Java 对象的转换。有各种方法可以做到这一点。最简单的方法是使用在线资源[(这里是](http://pojo.sodhanalibrary.com))，这几乎不涉及任何代码。您所要做的就是粘贴我们的 XML 内容和数据....你有你的 POJO。其他许多方法之一是使用 JAXBContext 类的 Unmarshaller 对象。

```
//-------------------------- UNMARSHALLING XML  -------------------------------
public static void unmarshalXML(File xmlFile) {
    try {       
    System.out.println("Unmarshalling XML to Java Object-------");

    //creating the JAXB context
    JAXBContext jContext = JAXBContext.newInstance(USSDMenuRequest.class); 

     //create the unmarshal object
     Unmarshaller unmarshallerObj = jContext.createUnmarshaller();

     //call the unmarshal method
     USSDMenuRequest request= (USSDMenuRequest) unmarshallerObj.unmarshal(xmlFile);

    request.setStarCode("124");
    request.setKeyWord("Start");

  System.out.println(request.getShortCode() + " " + request.getPrompt() );
    }
    catch(Exception e){
            e.printStackTrace();
        }
    } 
```

请点击查看完整代码

我希望你喜欢这篇文章。天气非常凉爽。试试看。在推特上关注我[@祝福 _ HL](https://twitter.com/blessed_hl?lang=en)T3】