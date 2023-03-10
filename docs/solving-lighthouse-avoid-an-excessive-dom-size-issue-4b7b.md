# 解决灯塔“避免过大的 DOM 大小”的问题

> 原文：<https://dev.to/lilianaziolek/solving-lighthouse-avoid-an-excessive-dom-size-issue-4b7b>

最近，我们开始关注 OSBO 的表现。由于页面大多是在我们还不太了解前端开发的时候建立的(英国的意思是:*我们不知道自己在做什么*)，再加上我们没有对性能进行任何主动监控，各种问题显然设法溜了进来。

# 如果你不知道 Lighthouse，先去看看

有很多关于如何启动 Lighthouse 的文章，它们包含了许多非常有用的建议，所以我在这里不再重复。但是有一个问题的建议不是特别友好:“避免过大的 DOM 大小”。在我们的例子中，甚至我们的主页和注册页面也有大约 3500 个 DOM 节点，考虑到它们相当简单，这听起来有些过分。我们努力理解所有这些节点是从哪里来的。所有的建议都围绕着“避免创建太多的 DOM 节点”——但是我就是找不到任何有用的信息来告诉我如何找到节点是在哪里创建的(逻辑上是在我的代码库中)。我的代码的哪一部分有问题？在知道需要优化哪些组件之前，很难进行优化。

因此，我很快开发了一个工具来帮助我们找到“DOM 瓶颈”。因为我仍然喜欢 Java(或者说:这是我最擅长的工具)，所以它是用 Java 写的——对不起，伙计们；)

# 找到要修剪的 DOM 分支

这个原理实际上非常简单，就像如果你突然用完了硬盘上的空间，你会四处寻找硬盘上所有空间的去向。你找到最大的文件夹。然后是最大文件夹里的最大文件夹。如此等等，直到你看到一些可疑的东西——一个比你通常预期的要大的文件夹。

为了在不花费太多时间编写工具本身的情况下做到这一点(最终花费了我大约 30 分钟)，我决定使用 JSoup(解析来自我们网站的 DOM 树)和 Jackson -来很好地打印结果，因为我可以很容易地在 IntelliJ 中折叠/展开 JSON(有用的提示:打开任何。json 文件，并按下 **CTRL-ALT-L** 来很好地缩进一行 JSON)。

完整的结果在 [Github Repo](https://github.com/lilianaziolek/blog-examples/tree/master/lighhouse-performance-issues) 中(我有一种感觉，我们可能需要更多的东西来帮助我们完成灯塔报告)。项目中有两个类:

OsboDomNode -一个代表 DOM 的类，表示我们所关心的:孩子(和祖父母)的总数...子节点)，以及关于直接子节点的一些基本统计信息。它使用递归函数来聚合每个 DOM 元素中的节点总数。

```
package online.onestopbeauty.blog.examples.lighthouse.dom;

import com.fasterxml.jackson.annotation.JsonIgnore;
import com.fasterxml.jackson.annotation.JsonPropertyOrder;
import lombok.*;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;

import java.util.List;
import java.util.Map;
import java.util.Optional;
import java.util.stream.Collectors;

import static java.util.Collections.emptyList;
import static java.util.Comparator.naturalOrder;
import static java.util.stream.Collectors.groupingBy;

@Data
@Builder
@JsonPropertyOrder({ "description", "type", "allChildNodesCount", "childNodesSummary" })
public class OsboDomNode {

    private final String type;
    private final String description;
    @JsonIgnore
    @Singular
    private final List<OsboDomNode> childNodes;

    @Getter(AccessLevel.NONE)
    private Integer allChildNodesCount;

    public int getAllChildNodesCount() {
        if (allChildNodesCount == null) {
            allChildNodesCount = this.childNodes.size() + this.childNodes.stream().mapToInt(OsboDomNode::getAllChildNodesCount).sum();
        }
        return allChildNodesCount;
    }

    public List<String> getChildNodesSummary() {
        Integer allChildNodesCount = this.getAllChildNodesCount();
        return this.childNodes.stream().map(child -> percentageInChild(child, allChildNodesCount)).collect(Collectors.toList());
    }

    public List<OsboDomNode> getNodesWithHighestNumberOfChildren() {
        Map<Integer, List<OsboDomNode>> nodesWithChildCount = childNodes.stream().collect(groupingBy(OsboDomNode::getAllChildNodesCount));
        Optional<Integer> maxNodes = nodesWithChildCount.keySet().stream().max(naturalOrder());
        if (maxNodes.isPresent()) {
            return nodesWithChildCount.get(maxNodes.get());
        } else {
            return emptyList();
        }
    }

    private String percentageInChild(OsboDomNode child, Integer allChildNodesCount) {
        double percentage = 100.0 * child.getAllChildNodesCount() / allChildNodesCount;
        return String.format("%d [%.2f%%] in %s %s", child.getAllChildNodesCount(), percentage, child.type, child.description);
    }

    public static OsboDomNode fromElement(Element element) {
        OsboDomNode.OsboDomNodeBuilder builder = OsboDomNode.builder();
        builder.type(element.tag().getName() + "[" + element.siblingIndex() + "]");
        builder.description(element.attributes().toString());

        Elements children = element.children();
        children.forEach(child -> builder.childNode(OsboDomNode.fromElement(child)));
        return builder.build();
    }
} 
```

OsboPerfHelper -一个简单的 runner，你输入你的网站的 URL(甚至可以是 localhost)，它关闭，读取 DOM 结构，然后我们把它输入 OsboDomNode 进行分析。

```
package online.onestopbeauty.blog.examples.lighthouse.dom;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;

import java.io.File;
import java.io.IOException;

public class OsboPerfHelper {

    private static final ObjectMapper OBJECT_MAPPER = new ObjectMapper();

    public static void main(String[] args) throws IOException {
        String osboUrl = "http://localhost:8081";
        Document doc = Jsoup.connect(osboUrl).get();
        Element body = doc.body();
        OsboDomNode osboDomNode = OsboDomNode.fromElement(body);
        System.out.println((Integer) osboDomNode.getAllChildNodesCount());
        printJson(osboDomNode);

    }

    private static void printJson(OsboDomNode osboDomNode) throws IOException {
// System.out.println(OBJECT_MAPPER.writeValueAsString(osboDomNode));
        File resultFile = new File("domNode.json");
        OBJECT_MAPPER.writeValue(resultFile, osboDomNode);
        System.out.println("Written JSON result into " + resultFile.getAbsolutePath());
    }

} 
```

各自的 build.gradle 文件

```
plugins {
    id 'java'
}

group 'online.onestopbeauty.blog.examples'
version '1.0-SNAPSHOT'

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
// https://mvnrepository.com/artifact/org.jsoup/jsoup
    compile group: 'org.jsoup', name: 'jsoup', version: '1.12.1'
// https://mvnrepository.com/artifact/org.projectlombok/lombok
    compileOnly group: 'org.projectlombok', name: 'lombok', version: '1.18.8'
// https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind
    compile group: 'com.fasterxml.jackson.core', name: 'jackson-databind', version: '2.9.9'

    testCompile group: 'junit', name: 'junit', version: '4.12'
} 
```

哦，是的，我用 Lombok 来表示构造函数、构建器和其他样板文件(getters 等)。)——只是因为 Lombok 很牛逼，是我在任何 Java 项目中总是第一个添加的东西。只要记住添加 Lombok 插件并在 IntelliJ 中打开注释处理，否则你会得到编译错误。

# 我们的真实世界体验

那么，当我们在现场版上运行时，情况看起来如何呢？前几层节点看起来相当健康，主体和直接子节点各包含大约 99%的节点(只是几层包装器，没什么可担心的)。但是后来我发现了一些可疑的东西(这里要提醒一下在组件中使用有意义的类名——这样故障排除就容易多了):

```
{
"description": " class=\"application--wrap\"",
"type": "div[0]",
"allChildNodesCount": 3401,
"childNodesSummary": [
  "[39.05] in div[2] class=\"layout\" data-v-3a808de6",
  "[56.40] in main[4] class=\"v-content\" style=\"padding-top:0px;padding-right:0px;padding-bottom:56px;padding-left:0px;\"",
  "[4.38] in footer[6] data-cy=\"osboFooter\" class=\"v-footer v-footer--absolute v-footer--inset theme--light\" style=\"height:auto;margin-bottom:56px;border-radius:10px;\" data-v-3645c51c",
  "[0.06] in button[8] type=\"button\" medium=\"\" class=\"v-btn v-btn--bottom v-btn--floating v-btn--fixed v-btn--right v-btn--small theme--dark secondary fab-style\" style=\"display:none;\" data-v-045da490"
]} 
```

我们应用程序的“主”部分占了不到 60%的节点，而“div[2] / layout”元素占了将近 40%。此时，我在 OsboPerfHelper 中添加了一个额外的 log 语句，深入到正确的节点。这当然可以用一种更好的方式来完成，如果我必须更频繁地使用它，也许我会添加一些更好的“深入”工具——但是在这一点上，这是一个大约半小时的“快速而肮脏”的工作——并且做得足够好:

```
printJson(osboDomNode.getNodesWithHighestNumberOfChildren().get(0)
                .getNodesWithHighestNumberOfChildren().get(0)
                .getNodesWithHighestNumberOfChildren().get(0)
                .getNodesWithHighestNumberOfChildren().get(0)
                .getChildNodes().get(0)); 
```

结果是:

```
{
"description": " class=\"flex offset-md1 md10 xs12\" data-v-3a808de6",
"type": "div[0]",
"allChildNodesCount": 1327,
"childNodesSummary": [
   "[0.45] in div[0] class=\"layout\" data-v-0c4978b8 data-v-3a808de6",
   "[65.49] in aside[2] data-cy=\"mobileNavBar\" class=\"offWhite1 v-navigation-drawer v-navigation-drawer--clipped v-navigation-drawer--close v-navigation-drawer--fixed v-navigation-drawer--temporary theme--light\" style=\"height:100%;margin-top:0px;transform:translateX(-375px);width:375px;\" data-v-c332d172 data-v-3a808de6",
    "[33.84] in nav[4] id=\"attachMenu\" data-cy=\"osboToolBar\" class=\" text-xs-center px-0 toolbarStyle v-toolbar elevation-0 v-toolbar--dense v-toolbar--extended theme--light\" style=\"margin-top:0px;padding-right:0px;padding-left:0px;transform:translateY(0px);\" data-v-3a808de6"
]} 
```

这让我看到我的移动导航栏中有近 900 个节点。有趣的是，我甚至不需要在桌面版本的页面上使用 mobileNavbar(带有移动版本的菜单),这是我目前正在测试的。因此，我们去做了一些简单的清理，以减少移动菜单的大小(900 个节点听起来太多了，即使是在需要的时候)，并确保它不是在桌面上生成的(因为这是一种浪费，而且永远不会显示)。

这仅仅是削减 DOM 树的开始(我们现在在 localhost 上有大约 1700 个节点，这是一个巨大的削减，还会有更多的削减)，但是关键是要知道要削减哪些 DOM“分支”。

# 还有比这更好的吗？

如果你知道更好的工具，请在下面的评论中留言。我发现很难相信这样一个简单的问题没有已经存在的东西——但是快速的 google 搜索给我的主要结果是许多描述为什么大 DOM 不好的文章——而不是如何在 DOM 树中找到你的最坏的违法者。否则，如果这个“微型工具”有任何帮助，请随时报告。