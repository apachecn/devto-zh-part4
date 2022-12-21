# React PDF 使用 React-pdf 在 React 中生成 PDF 文档

> 原文：<https://dev.to/finallynero/generating-pdf-documents-in-react-using-react-pdf-4ka7>

> 这篇文章最初发表在我的[博客](https://finallynero.dev/blogs)上，查看更多内容。

### 简介

我最近在做一个项目，我得到了一个独特的(对我来说)需求，需要我从浏览器中的一组值生成 pdf 文件，通常在我的软件开发经验中，Pdf 文件是在后端使用[木偶师](https://github.com/GoogleChrome/puppeteer)为 node js 和 [FPDF](http://www.fpdf.org/) 为 PHP e.t.c 生成的。所以我必须寻找一个可以为我的用例工作的 React 库，幸运的是我找到了 [React-pdf](https://react-pdf.org/) 。我找到了其他像[@ progress/kendo-React-pdf](https://www.npmjs.com/package/@progress/kendo-react-pdf)这样的库，但是我决定用 [React-pdf](https://react-pdf.org/) ，因为它的文档对开发者友好。这座图书馆是由迭戈·穆拉乔莱建造并维护的。
因此，在教程/博客文章中，我将尝试简要解释 react-pdf 的工作原理，并向您介绍如何从来自 [Moviedb Api](https://developers.themoviedb.org/3) 的一系列对象中生成 pdf。

### 特性

当我浏览文档试图为我的用例选择合适的库时， [React-pdf](https://react-pdf.org/) 的一些特性说服了我使用它，我将简要地谈谈它们:

##### 组件

React-Pdf 使用 [React-Primitives](https://github.com/lelandrichardson/react-primitives) 规范来创建定制组件，您可以使用这些组件来创建和组织您的 Pdf 文档。
这些组件包括:

*   文件
*   页
*   视角
*   图像
*   文本
*   环
*   注意
*   帆布
*   pdf 查看器
*   PDFDownloadLink
*   blob 提供程序

你可以查看文档，了解上面每个组件的详细功能，基本上这些组件可以帮助你使用 JSXesques 语法创建 pdf。

##### 造型

既然我们已经知道了如何创建 PDF 文档，那么我们该如何设计它的样式呢？React-pdf 使用样式表 API 提供强大的样式解决方案，帮助您使用 CSS、媒体查询和 Flexbox 来设计文档样式。查看文档以了解它们支持的 CSS 属性。
如果你是 CSS-in-JS 的忠实粉丝怎么办？嗯，他们也支持整个[样式组件](https://www.styled-components.com) API。

##### Fonts

React-Pdf 有一个`Font` API，可以帮助你从不同的来源加载字体，并在你的 Pdf 文档中使用。

这些是让我选择 React-pdf 的一些特征。另外，当我检查 Github 库时，维护者 T2 非常活跃，并试图对大多数公开的问题做出回应。

### 演示

因此，我将简要介绍一个从 MoviesDB API 生成 pdf 的简单示例。这个演示将演示如何制作年度最佳电影。

#### 文件夹结构

```
project
│   package.json
│
│
└───Public
│   │   150.png
│   │   index.html
│   │   star.png
│
│
│
└───src
    │   Movie.jsx
    │   MovieList.jsx
    |   constant.js
    |   index.js
    |   styles.css 
```

Enter fullscreen mode Exit fullscreen mode

index.js(条目)

```
import React from "react";
import ReactDOM from "react-dom";
import MovieList from "./MovieList";

import "./styles.css";

function App() {
    return (
        <div className="App">
              <MovieList />
        </div>
    );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

index.js 是应用程序的入口点。它呈现了我们应用程序的父组件`<MovieList/>`。

MovieList.jsx

```
import React, { useState } from "react";
import Axios from "axios";
import { PDFDownloadLink } from "@react-pdf/renderer";
import { API_KEY } from "./constants";
import { PdfDocument } from "./Movie";

const years = [
  { value: "2010", text: "2010" },
  { value: "2011", text: "2011" },
  { value: "2012", text: "2012" },
  { value: "2013", text: "2013" },
  { value: "2014", text: "2014" },
  { value: "2015", text: "2015" },
  { value: "2016", text: "2016" },
  { value: "2017", text: "2017" },
  { value: "2018", text: "2018" },
  { value: "2019", text: "2019" }
];

export default function MovieList() {
  const [year, setYear] = useState("");
  const [movieDetails, setDetails] = useState([]);
  const [show, setHide] = useState(false)

  const fetchMovie = async e => {
    setYear(e.target.value);
    try {
      let res = await Axios(
        `https://api.themoviedb.org/3/discover/movie?api_key=${API_KEY}&primary_release_year=${year}&sort_by=vote_average.desc`
      );
      setDetails(res.data.results);
      setHide(true)
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <div className="container">
      <h2>Best movies of the year</h2>
      <label htmlFor="movies">Select Year</label>
      <select id="movies" className="select" onChange={fetchMovie}>
        <option defaultValue="" disabled>
          Select your option
        </option>
        {years.map((year, index) => {
          return (
            <option key={index} value={year.value}>
              {year.text}
            </option>
          );
        })}
      </select>
      {show &&<PDFDownloadLink
        document={<PdfDocument data={movieDetails} />}
        fileName="movielist.pdf"
        style={{
          textDecoration: "none",
          padding: "10px",
          color: "#4a4a4a",
          backgroundColor: "#f2f2f2",
          border: "1px solid #4a4a4a"
        }}
      >
        {({ blob, url, loading, error }) =>
          loading ? "Loading document..." : "Download Pdf"
        }
      </PDFDownloadLink>}
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

组件包含了这个应用程序中的大部分逻辑。我们从`@react-pdf/renderer`导入`PDFDownloadLink`，这基本上是一个锚标签，使我们能够生成和下载 PDF 文档。`PDFDownloadLink`接受一个`document`道具，这是我们将很快使用本文前面列出的一些 React-primitives 创建的 PDF 模板。它还接受一个用于定义 PDF 文档文件名的`filename`属性，一个用于向链接标签添加内联样式的`style`属性，一个如果您喜欢使用类来样式化的`className`属性和一个作为锚标签内容的`children`属性。

Movie.jsx

```
import React from "react";
import {
    Page,
    Text,
    View,
    Document,
    StyleSheet,
    Image
} from "@react-pdf/renderer";
import moment from "moment";

const POSTER_PATH = "https://image.tmdb.org/t/p/w154";

const styles = StyleSheet.create({
    page: {
        backgroundColor: "#ffffff"
    },
    section: {
        margin: 10,
        padding: 10,
        flexGrow: 1
    },
    movieContainer: {
        backgroundColor: "#f6f6f5",
        display: "flex",
        flexDirection: "row",
        padding: 5
    },
    movieDetails: {
        display: "flex",
        marginLeft: 5
    },
    movieTitle: {
        fontSize: 15,
        marginBottom: 10
    },
    movieOverview: {
        fontSize: 10
    },

    image: {
        height: 200,
        width: 150
    },
    subtitle: {
        display: "flex",
        justifyContent: "space-between",
        flexDirection: "row",
        width: 150,
        alignItems: "center",
        marginBottom: 12
    },
    vote: {
        display: "flex",
        flexDirection: "row"
    },
    rating: {
        height: 10,
        width: 10
    },
    vote_text: {
        fontSize: 10
    },
    vote_pop: {
        fontSize: 10,
        padding: 2,
        backgroundColor: "#61C74F",
        color: "#fff"
    },
    vote_pop_text: {
        fontSize: 10,
        marginLeft: 4
    },
    overviewContainer: {
        minHeight: 110
    },
    detailsFooter: {
        display: "flex",
        flexDirection: "row"
    },
    lang: {
        fontSize: 8,
        fontWeight: 700
    },
    vote_average: {
        fontSize: 8,
        marginLeft: 4,
        fontWeight: "bold"
    }
});

export function PdfDocument(props) {
    console.log("pdf props", props.data);
    return (
        <Document>
            <Page style={styles.page}>
                {props.data
                    ? props.data.map((a, index) => {
                            return (
                                <View key={index} style={styles.movieContainer}>
                                    <Image
                                        style={styles.image}
                                        source={
                                            a.poster_path !== null
                                                ? `${POSTER_PATH}${a.poster_path}`
                                                : "150.jpg"
                                        }
                                    />
                                    <View style={styles.movieDetails}>
                                        <Text style={styles.movieTitle}>{a.title}</Text>
                                        <View style={styles.subtitle}>
                                            <View style={styles.vote}>
                                                <Image source="star.png" style={styles.rating} />
                                                <Text style={styles.vote_text}>{a.vote_count}</Text>
                                            </View>
                                            <View style={styles.vote}>
                                                <Text style={styles.vote_pop}>{a.popularity}</Text>
                                                <Text style={styles.vote_pop_text}>Popularity</Text>
                                            </View>
                                        </View>
                                        <View style={styles.overviewContainer}>
                                            <Text style={styles.movieOverview}>{a.overview}</Text>
                                        </View>
                                        <View style={styles.detailsFooter}>
                                            <Text style={styles.lang}>
                                                Language: {a.original_language.toUpperCase()}
                                            </Text>
                                            <Text style={styles.vote_average}>
                                                Average Votes: {a.vote_average}
                                            </Text>
                                            <Text style={styles.vote_average}>
                                                Release Date:{"  "}
                                                {moment(a.release_date, "YYYY-MM-DD").format(
                                                    " MMMM D Y"
                                                )}
                                            </Text>
                                        </View>
                    </View>
                    </View>
                );
                })
            : ""}
            </Page>
        </Document>
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

这个`Movie.jsx`组件是我们正在生成的 PDF 的模板，在这里我们使用 React-primitives(视图、文档)和样式定义 PDF 的结构。因此，我将简要谈谈我在这里使用的一些 React-pdf API。

*   `StyleSheet.create()`:它帮助你定义你想要在文档中使用的样式，它接受一个包含你想要在文档中使用的所有 CSS 的对象，它返回一个你可以通过`style` prop 应用到任何 PDF 元素的对象。

*   `Document`:`PDFDownloadLink``document`属性只接受类型`Document`的组件，所以当创建 PDF 模板时，这必须是你的组件的根，并且只接受类型`Page`的子元素，`Document`只是你的 PDF 模板的包装器，它接受一些可选的[属性](https://react-pdf.org/components#document)

*   `Page`:表示文档中的一页，一个文档中可以有多个`Pages`。它接受一些道具来定义页面的`size`、`orientation`或者如果你想要页面换行的话`wrap`。[道具](https://react-pdf.org/components#page)

*   `View`:我想把这个组件和 HTML `div`进行比较，它可以帮助你对文档进行分段或分割。[道具](https://react-pdf.org/components#view)

*   `Text`:该组件用于在文档上显示文本，并对其应用样式。[道具](https://react-pdf.org/components#text)

*   `Image`:该组件用于在文档上显示图像(网络或本地)，这些图像可以是 PNG、JPG 或 base64。

### 演示应用程序

[https://codesandbox.io/embed/react-pdf-demo-i1ted](https://codesandbox.io/embed/react-pdf-demo-i1ted)

## 结论

在我使用这个库之前，我从来没有想过可以在客户端生成 pdf，react-pdf 不仅允许您这样做，而且可以使用 JSXesque 语法来构造和设计 PDF 文档。我知道这个演示很琐碎，但我认为这个库在一些用例中可能是有用的。