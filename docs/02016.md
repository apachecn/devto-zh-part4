# mais performance e insight rápidos no Aggregation Framework-$ facet

> 原文：<https://dev.to/delbussoweb/mais-performance-e-insight-rapidos-no-aggregation-framework-facet-3ebm>

我是个人，blz？今天，我将向大家展示聚合管道中非常棒的功能，更具体地说，这是一个非常有用的阶段，它可以为我们提供数据的多维视图，甚至可以提高我们聚合的性能。

## esta gios do 聚合管道

对于不熟悉聚合框架的用户，组件之一是管道阶段。阶段包含从最简单的操作(如筛选器)到复杂的数据分组和“分离”操作(如“组”或“[存储桶”](https://docs.mongodb.com/manual/reference/operator/aggregation/bucket/))。今天我们将看到‘T6’$ facet’。

## O 数据集

作为我们示例的基础，我将使用一个包含巴西选民人口数据的数据库。此基础最初是通过一组. csv 文件提供的，我已经导入到了“[mongob atlas](https://www.mongodb.com/cloud/atlas)的一个实例中。文件结构下:

```
{  "_id"  :  ObjectId("5d532411541c5b6d0cfe90d3"),  "ano"  :  2018,  "uf"  :  "SP",  "codigoCidade"  :  64777,  "nomeCidade"  :  "GUARULHOS",  "codigoSitBiometrica"  :  1,  "sitBiometrica"  :  "Biométrico",  "zona"  :  393,  "codigoGenero"  :  2,  "genero"  :  "MASCULINO",  "codigoEstadoCivil"  :  7,  "estadoCivil"  :  "SEPARADO JUDICIALMENTE",  "codigoFaixaEtaria"  :  6064,  "faixaEtaria"  :  "60 a 64 anos",  "codigoEscolaridade"  :  8,  "escolaridade"  :  "SUPERIOR COMPLETO",  "qtdPerfil"  :  13,  "qtdBiometria"  :  13,  "qtdDeficiencia"  :  1,  "qtdNomeSocial"  :  0  } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，数据是按配置文件排序的，也就是说，我的集合中的每个文档都有一个符合特定配置文件的总人数。在上面的例子中，我们可以说我们有 13 个人在下面的情况:

*   城市:瓜拉尼
*   透明带:393
*   杰内罗:马斯库里诺
*   民事诉讼:司法分离
*   年龄范围:60 至 64 岁
*   学历:完全高等教育

如果我们想知道按性别分列的选民总数，我们可以做一个简单的$ group:

```
db.eleitorado.aggregate([
    {
        $group: {
            _id: "$genero",
            total: {
                $sum: "$qtdPerfil"
            }
        }
     }
]) 
```

Enter fullscreen mode Exit fullscreen mode

```
/*  1  */  {  "_id"  :  "MASCULINO",  "total"  :  69902977  }  /*  2  */  {  "_id"  :  "FEMININO",  "total"  :  77339897  }  /*  3  */  {  "_id"  :  "NÃO INFORMADO",  "total"  :  63401  } 
```

Enter fullscreen mode Exit fullscreen mode

这使我们能够按其中一个属性(如性别)进行分组，如果这样做，我们将需要多次执行聚合，交换属性，并获得单独的结果。使用这种方法，我们会失去很多性能和效率，因此我们可以使用$facet！

## 多维度

$facet 运算符使我们能够并行执行多个阶段，并一次获得每个阶段的结果。每次运行$facet 时，可以使用多个运算符，例如$group、$unwind、$sort 等。

下面的示例通过放置 UF 和 city 过滤器来增加我们的管道，并为文档中的每个属性生成一个分组:

```
db.eleitorado.aggregate([
    {
        $match: {
            "uf": "SP",
            "nomeCidade": "GUARULHOS"
        }
    }, 
    {
        $facet: {
            "genero": [ 
                {
                    $group: {
                        _id:"$genero",
                        total: {
                            $sum: "$qtdPerfil"
                        }
                    }
                },
                {
                    $sort: {
                        total: -1
                    }
                }
            ],
            "estadoCivil": [
                {
                        $group: {
                        _id: "$estadoCivil",
                        total: {
                            $sum: "$qtdPerfil"
                        }
                    }  
                },
                {
                    $sort: {
                        _id: 1
                    }
                }
            ],
            "escolaridade": [
                {
                    $group: {
                        _id: "$escolaridade",
                        total: {
                            $sum: "$qtdPerfil"
                        }
                    }
                },
                {
                    $sort: {
                        _id: 1
                    }
                }
            ],
            "faixaEtaria": [
                {
                    $group: {
                        _id: "$faixaEtaria",
                        total: {
                            $sum: "$qtdPerfil"
                        }
                    }
                },
                {
                    $sort: {
                        _id: 1
                    }
                }
            ]
        }
    }
]) 
```

Enter fullscreen mode Exit fullscreen mode

```
/*  1  */  {  "genero"  :  [  {  "_id"  :  "FEMININO",  "total"  :  439778  },  {  "_id"  :  "MASCULINO",  "total"  :  374563  },  {  "_id"  :  "NÃO INFORMADO",  "total"  :  1  }  ],  "estadoCivil"  :  [  {  "_id"  :  "CASADO",  "total"  :  335326  },  {  "_id"  :  "DIVORCIADO",  "total"  :  42696  },  {  "_id"  :  "NÃO INFORMADO",  "total"  :  2  },  {  "_id"  :  "SEPARADO JUDICIALMENTE",  "total"  :  8363  },  {  "_id"  :  "SOLTEIRO",  "total"  :  403840  },  {  "_id"  :  "VIÚVO",  "total"  :  24115  }  ],  "escolaridade"  :  [  {  "_id"  :  "ANALFABETO",  "total"  :  13341  },  {  "_id"  :  "ENSINO FUNDAMENTAL COMPLETO",  "total"  :  57995  },  {  "_id"  :  "ENSINO FUNDAMENTAL INCOMPLETO",  "total"  :  140105  },  {  "_id"  :  "ENSINO MÉDIO COMPLETO",  "total"  :  321555  },  {  "_id"  :  "ENSINO MÉDIO INCOMPLETO",  "total"  :  94857  },  {  "_id"  :  "LÊ E ESCREVE",  "total"  :  18788  },  {  "_id"  :  "SUPERIOR COMPLETO",  "total"  :  122057  },  {  "_id"  :  "SUPERIOR INCOMPLETO",  "total"  :  45644  }  ],  "faixaEtaria"  :  [  {  "_id"  :  "100 anos ou mais",  "total"  :  4  },  {  "_id"  :  "16 anos",  "total"  :  1464  },  {  "_id"  :  "17 anos",  "total"  :  5595  },  {  "_id"  :  "18 anos",  "total"  :  16434  },  {  "_id"  :  "19 anos",  "total"  :  20556  },  {  "_id"  :  "20 anos",  "total"  :  21905  },  {  "_id"  :  "21 a 24 anos",  "total"  :  82067  },  {  "_id"  :  "25 a 29 anos",  "total"  :  83577  },  {  "_id"  :  "30 a 34 anos",  "total"  :  88977  },  {  "_id"  :  "35 a 39 anos",  "total"  :  91908  },  {  "_id"  :  "40 a 44 anos",  "total"  :  83734  },  {  "_id"  :  "45 a 49 anos",  "total"  :  77979  },  {  "_id"  :  "50 a 54 anos",  "total"  :  71406  },  {  "_id"  :  "55 a 59 anos",  "total"  :  59686  },  {  "_id"  :  "60 a 64 anos",  "total"  :  49362  },  {  "_id"  :  "65 a 69 anos",  "total"  :  34511  },  {  "_id"  :  "70 a 74 anos",  "total"  :  16506  },  {  "_id"  :  "75 a 79 anos",  "total"  :  5951  },  {  "_id"  :  "80 a 84 anos",  "total"  :  2005  },  {  "_id"  :  "85 a 89 anos",  "total"  :  578  },  {  "_id"  :  "90 a 94 anos",  "total"  :  117  },  {  "_id"  :  "95 a 99 anos",  "total"  :  13  },  {  "_id"  :  "Inválido",  "total"  :  7  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，对于性别分组，我使用$sort 阶段按降序显示合计，而对于其他分组，我使用配置文件名称按字母顺序排序。这表明阶段是独立运行的，它们之间可能包含不同的“子阶段”。

好吧，我们的想法是要证明我们可以在使用聚合框架对 mongodb 的集合中的数据进行分类和分组时，有更高的性能和效率。

希望你们喜欢分享和评论！

下次见！