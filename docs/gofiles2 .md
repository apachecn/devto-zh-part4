# How to turn your golang programs into UML Class diagrams

> 原文：[https://dev.to/jfeliu007/how-to-turn-your-golang-programs-into-uml-class-diagrams-1e5o](https://dev.to/jfeliu007/how-to-turn-your-golang-programs-into-uml-class-diagrams-1e5o)

Sometimes it helps to see a class diagram of your projects to understand the big picture. I have always been a fan of PlantUML. When it comes to drawing UML diagrams, plantUML is one of my favorites since drawings are generated out of a human-readable text file, which can be easily committed to git.

For that reason, I decided to write a Golang library to generate class diagrams from a Golang project.

Goplantuml is a Golang library with an added command that you can install if you run go already. It basically turns code like this

```
package goisawesome

import "strings"

type Foo struct {
    field       int
    PublicField bool
    *strings.Builder
}
type MyInterface interface {
    GetValue() bool
}

func (f *Foo) myPrivateFunction() int {
    return 3
}

func (f *Foo) GetValue() bool {
    return true
}

func (f *Foo) MyPublicFunction() bool {
    return true
} 
```

into this

```
@startuml
namespace goisawesome {
    class Foo {
        - field int
        + PublicField bool
        - myPrivateFunction() 
        + GetValue() 
        + MyPublicFunction() 
    }
    interface MyInterface {
        + GetValue() 
    }
}
strings.Builder *-- goisawesome.Foo
goisawesome.MyInterface <|-- goisawesome.Foo
@enduml 
```

which can be used with plantuml to generate a nice class diagram.

see example [here](http://www.plantuml.com/plantuml/uml/VK_BIi0m4BpdA_ReW_OBF1GF5Gz2JzwtwROiR1B99agf_NTZAIMAE8UGcSnCc4uJnfIT68-Ez0qjmH9OyOCqE8BDG84LL8K-X6z-ee6PIIPWdoxj7eOy2jl-U1b3aChkrY7oEoRgixU9Wx-vXIhvHEaL9TDFzMKz6tzbzkCiVrESqLBnFL_txP_MtUn6KsI_QFkOMIQASDSqzUQsx3GrhxiVFlymT-IdxEGB)

If you are interested, swing by the goplantuml repo.

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ jfeliu007 ](https://github.com/jfeliu007) / [goplantuml](https://github.com/jfeliu007/goplantuml) 

### PlantUML Class Diagram Generator for golang projects

<article class="markdown-body entry-content container-lg" itemprop="text">

[![godoc reference](img/6db996233e5b827b61928bdd3abb1558.png)](https://godoc.org/github.com/jfeliu007/goplantuml/parser) [![Go Report Card](img/6ecebf22edf1dbdada95d824886b7f6a.png)](https://goreportcard.com/report/github.com/jfeliu007/goplantuml) [![codecov](img/d61d1a37ad8200ed8ead3c80db80ed5a.png)](https://codecov.io/gh/jfeliu007/goplantuml) [![License: MIT](img/20c071d968747ff7712d12e224536260.png)](https://opensource.org/licenses/MIT) [![GitHub release](img/58184640f03929b0eb1bfbae180e506c.png)](https://github.com/jfeliu007/goplantuml/releases/) [![Build Status](img/6f03335a3280fe0090bcae8799cf8ce6.png)](https://travis-ci.org/jfeliu007/goplantuml) [![Mentioned in Awesome Go](img/8c75483ef421b87d5e612b3be17435e6.png)](https://github.com/avelino/awesome-go) [![DUMELS Diagram](img/3594e1b762ba90ab04c9f2d2d876ca11.png)](https://www.dumels.com/diagram/23ff0222-e93b-4e9f-a4ef-4d5d9b7a5c7d)

# GoPlantUML

PlantUML Class Diagram Generator for golang projects. Generates class diagram text compatible with plantuml with the information of all structures and interfaces as well as the relationship among them.

## Want to try it on your code?

Take a look at [www.dumels.com](https://www.dumels.com). We have created dumels using this library.

## Code of Conduct

Please, review the code of conduct [here](https://github.com/jfeliu007/goplantuml/blob/master/CODE_OF_CONDUCT.md "here").

### Prerequisites

golang 1.10 or above

### Installing

```
go get github.com/jfeliu007/goplantuml/parser
go get github.com/jfeliu007/goplantuml/cmd/goplantuml
cd $GOPATH/src/github.com/jfeliu007/goplantuml
go install ./ 
```

This will install the command goplantuml in your GOPATH bin folder.

### Usage

```
goplantuml [-recursive] path/to/gofiles path/to/gofiles2 
```

```
goplantuml [-recursive] path/to/gofiles path/to/gofiles2 > diagram_file_name.puml 
```

```
Usage of goplantuml
  -aggregate-private-members
        Show aggregations for private members. Ignored if -show-aggregations is not used
  -hide-connections
        hides all connections in the diagram
  -hide-fields
        hides fields
  -hide-methods
        hides methods
  -ignore string
        comma separated list of folders to ignore
  -notes string
        Comma separated list of notes to be added to the diagram
```

…</article>

[View on GitHub](https://github.com/jfeliu007/goplantuml)

I would love to hear you opinions. I also wrote [an article](https://www.jazzcoding.com/effortless-class-diagrams-for-all-your-golang-needs/) about the program and how it is used if you would like more information.