# Java 的“构建器模式”生成器

> 原文：<https://dev.to/riversun/builder-pattern-generator-for-java-31ij>

# **简介**

我想介绍我的 web 应用程序，它可以为 Java 生成“生成器模式”源代码。有了这个工具，你可以立即生成构建器模式:“有效的 Java”、“GoF”、“可伸缩的构造器”、“Lombok”、“强制属性”

[https://riversun.github.io/java-builder/](https://riversun.github.io/java-builder/)

(仅用 HTML / CSS / JS 制作，无服务器端)

如下图，如果在定义中输入“[类型][变量]”，生成的代码会实时反映在右边。

[![animation.gif](img/a2d69c8413927cfc32769a2a68815751.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MBA5BoEi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/170905/4291ed54-28db-52ff-0efb-26e27e62d684.gif)

输入完代码后，按“复制到剪贴板”复制代码，并将其粘贴到 IDE 或编辑器中。

# **生成代码的例子**

## **定义**

只需键入如下内容，您就可以获得构建器模式的完整源代码。

```
package com.example
class Person
@Required String name
@Required Integer age
@Required String gender
@Required Integer height
String eyeColor
String hairColor
String hobby 
```

当 **@NotNull** 注释被添加到变量定义的开头时，它就成为一个**必需的**参数，并在构建时生成一个代码来检查 Null。

## **生成代码示例【有效 Java】**

**Person.java**

```
package com.example;

public class Person {

  private String name;
  private Integer age;
  private String gender;
  private Integer height;
  private String eyeColor;
  private String hairColor;
  private String hobby;

  public static class Builder {

    private String name;
    private Integer age;
    private String gender;
    private Integer height;
    private String eyeColor;
    private String hairColor;
    private String hobby;

    public Builder() {    
    }

    Builder(String name, Integer age, String gender, Integer height, String eyeColor, String hairColor, String hobby) {    
      this.name = name; 
      this.age = age; 
      this.gender = gender; 
      this.height = height; 
      this.eyeColor = eyeColor; 
      this.hairColor = hairColor; 
      this.hobby = hobby;             
    }

    public Builder name(String name){
      this.name = name;
      return Builder.this;
    }

    public Builder age(Integer age){
      this.age = age;
      return Builder.this;
    }

    public Builder gender(String gender){
      this.gender = gender;
      return Builder.this;
    }

    public Builder height(Integer height){
      this.height = height;
      return Builder.this;
    }

    public Builder eyeColor(String eyeColor){
      this.eyeColor = eyeColor;
      return Builder.this;
    }

    public Builder hairColor(String hairColor){
      this.hairColor = hairColor;
      return Builder.this;
    }

    public Builder hobby(String hobby){
      this.hobby = hobby;
      return Builder.this;
    }

    public Person build() {
        if(this.name == null){
          throw new NullPointerException("The property \"name\" is null. "
              + "Please set the value by \"name()\". "
              + "The properties \"name\", \"age\", \"gender\" and \"height\" are required.");
        }
        if(this.age == null){
          throw new NullPointerException("The property \"age\" is null. "
              + "Please set the value by \"age()\". "
              + "The properties \"name\", \"age\", \"gender\" and \"height\" are required.");
        }
        if(this.gender == null){
          throw new NullPointerException("The property \"gender\" is null. "
              + "Please set the value by \"gender()\". "
              + "The properties \"name\", \"age\", \"gender\" and \"height\" are required.");
        }
        if(this.height == null){
          throw new NullPointerException("The property \"height\" is null. "
              + "Please set the value by \"height()\". "
              + "The properties \"name\", \"age\", \"gender\" and \"height\" are required.");
        }

        return new Person(this);
    }
  }

  private Person(Builder builder) {
    this.name = builder.name; 
    this.age = builder.age; 
    this.gender = builder.gender; 
    this.height = builder.height; 
    this.eyeColor = builder.eyeColor; 
    this.hairColor = builder.hairColor; 
    this.hobby = builder.hobby;     
  }

  public void doSomething() {
      // do something
  }
} 
```

你你要用这个生成器 cod，
就这样写。

**AppMain.java**

```
public static void main(String[] args) {
        new Person.Builder().name("riversun").age(12).gender("male").height(200).build().doSomething();

    } 
```

## **生成“GoF”的代码示例**

**Person.java**

```
package org.example;
import java.util.ArrayList;
import java.util.List;

public class Person {

  private String name;
  private int age;
  private String sex;
  private List<String> hobby = new ArrayList<String>();

  public Person() {

  }

  public Person(String name, int age, String sex, List<String> hobby) {
    this.name = name; 
    this.age = age; 
    this.sex = sex; 
    this.hobby = hobby; 
  }

  public Person setName(String name){
    this.name = name;
    return Person.this;
  }

  public String getName(){
    return this.name;
  }

  public Person setAge(int age){
    this.age = age;
    return Person.this;
  }

  public int getAge(){
    return this.age;
  }

  public Person setSex(String sex){
    this.sex = sex;
    return Person.this;
  }

  public String getSex(){
    return this.sex;
  }

  public Person setHobby(List<String> hobby){
    this.hobby = hobby;
    return Person.this;
  }

  public Person addHobby(String hobby){
    this.hobby.add(hobby);
    return Person.this;
  }

  public List<String> getHobby(){
    return this.hobby;
  }

  public void doSomething(){
    System.out.println("Person's properties");
    System.out.println("name="+name);
    System.out.println("age="+age);
    System.out.println("sex="+sex);
    System.out.println("hobby="+hobby);  
  }
} 
```

**Builder.java**

```
package org.example;
import java.util.List;

public interface Builder {

  public void name(String name);
  public void age(int age);
  public void sex(String sex);
  public void hobby(List<String> hobby);

  Person getResult();
} 
```

**PersonBuilder.java**

```
package org.example;
import java.util.List;

public class PersonBuilder implements Builder {
  private Person person;

  public PersonBuilder() {
    this.person = new Person();
  }

  @Override
  public void name(String name) {
    this.person.setName(name);
  }

  @Override
  public void age(int age) {
    this.person.setAge(age);
  }

  @Override
  public void sex(String sex) {
    this.person.setSex(sex);
  }

  @Override
  public void hobby(List<String> hobby) {
    this.person.setHobby(hobby);
  }

  @Override
  public Person getResult() {

    if(this.person.getName() == null){
      throw new NullPointerException("The property \"name\" is null. "
          + "Please set the value by \"builder.name()\" at Director class. "
          + "The property \"name\" is required.");
    }

    return this.person;
  }
} 
```

**Director.java**

```
package org.example;

public class Director {

  private Builder builder;

  public Director(Builder builder) {
    this.builder = builder;
  }

  public void construct() {
    builder.name("something"); // required property
    builder.age(0); // optional property
    builder.sex("something"); // optional property
    //builder.hobby(new ArrayList<>());
  }
} 
```

**AppMain.java**

```
package org.example;

public class AppMain {

  public static void main(String[] args) {

    Builder builder = new PersonBuilder();
    Director director = new Director(builder);
    director.construct();
    Person person = builder.getResult();
    person.doSomething();

  }
} 
```

## **生成“伸缩式构造器”的源代码**

**Person.java**

```
package org.example;
import java.util.ArrayList;
import java.util.List;

public class Person {

  private String name;
  private int age;
  private String sex;
  private List<String> hobby = new ArrayList<String>();

  public Person() {

  }

  public Person(String name, int age, String sex, List<String> hobby) {
    this.name = name; 
    this.age = age; 
    this.sex = sex; 
    this.hobby = hobby; 
  }

  public Person setName(String name) {
    this.name = name;
    return Person.this;
  }

  public String getName() {
    return this.name;
  }

  public Person setAge(int age) {
    this.age = age;
    return Person.this;
  }

  public int getAge() {
    return this.age;
  }

  public Person setSex(String sex) {
    this.sex = sex;
    return Person.this;
  }

  public String getSex() {
    return this.sex;
  }

  public Person setHobby(List<String> hobby) {
    this.hobby = hobby;
    return Person.this;
  }

  public Person addHobby(String hobby){
    this.hobby.add(hobby);
    return Person.this;
  }

  public List<String> getHobby() {
    return this.hobby;
  }

} 
```

## **生成“龙目岛”的源代码**

**Person.java**

```
package org.example;
import java.util.ArrayList;
import java.util.List;

public class Person {

  private String name;
  private int age;
  private String sex;
  private List<String> hobby;

  Person(String name, int age, String sex, List<String> hobby) {

    if(name == null){
      throw new NullPointerException("The property \"name\" is null. "
          + "Please set the value by \"name()\". "
          + "The property \"name\" is required.");
    }

    this.name = name; 
    this.age = age; 
    this.sex = sex; 
    this.hobby = hobby; 
  }

  public static PersonBuilder builder(){
    return new PersonBuilder();
  }  
  public static class PersonBuilder {

    private String name;
    private int age;
    private String sex;
    private List<String> hobby = new ArrayList<String>();

    PersonBuilder() {    
    }

    public PersonBuilder name(String name){
      this.name = name;
      return PersonBuilder.this;
    }

    public PersonBuilder age(int age){
      this.age = age;
      return PersonBuilder.this;
    }

    public PersonBuilder sex(String sex){
      this.sex = sex;
      return PersonBuilder.this;
    }

    public PersonBuilder hobby(List<String> hobby){
      this.hobby = hobby;
      return PersonBuilder.this;
    }

    public PersonBuilder addHobby(String hobby){
      this.hobby.add(hobby);
      return PersonBuilder.this;
    }

    public Person build() {
      return new Person(this.name, this.age, this.sex, this.hobby);
    }
    @Override
    public String toString() {
      return "Person.PersonBuilder(name=" + this.name + ", age=" + this.age + ", sex=" + this.sex + ", hobby=" + this.hobby + ")";
    }
  }

  @Override
  public String toString() {
    return "Person(name=" + this.name + ", age=" + this.age + ", sex=" + this.sex + ", hobby=" + this.hobby + ")";
  }

  public void doSomething() {
      // do something
  }
} 
```

你你要用这个生成器 cod，
就这样写。

**AppMain.java**

```
public class AppMain {
    public static void main(String[] args) {
        System.out.println(Person.builder().name("Tom").age(12).sex("male").build());
    }
} 
```

## 生成源代码的"强制属性"

它是一种构建器模式，可以按照正确的顺序设置强制属性。

**Person.java**

```
package com.example;
import java.util.Optional;

public class Person {

  private final String name;// Required
  private final Integer age;// Required
  private final String gender;// Required
  private final Integer height;// Required
  private final Optional<String> eyeColor;// Optional(use Optional<String>)
  private final Optional<String> hairColor;// Optional(use Optional<String>)
  private final Optional<String> hobby;// Optional(use Optional<String>)

  Person(Builder.Builder1 builder) {
    this.name = builder.name; 
    this.age = builder.age; 
    this.gender = builder.gender; 
    this.height = builder.height; 
    this.eyeColor = builder.eyeColor; 
    this.hairColor = builder.hairColor; 
    this.hobby = builder.hobby; 
  }
  public static Builder builder() {
    return new Builder();
  }

  public static final class Builder {
    public Builder1 name(String name) {
      return new Builder1(name);
    }
    public static final class Builder1 {
      final String name;
      Integer age;
      String gender;
      Integer height;
      Optional<String> eyeColor;
      Optional<String> hairColor;
      Optional<String> hobby;

      private Builder1(String name) {
        this.name = name;
      }
      public Builder2 age(Integer age) {
        this.age = age;
        return new Builder2(Builder1.this);
      }
    }
    public static final class Builder2 {
      final Builder1 builder;

      private Builder2(Builder1 builder) {
        this.builder = builder;
      }
      public Builder3 gender(String gender) {
        this.builder.gender = gender;
        return new Builder3(this.builder);
      }
    }
    public static final class Builder3 {
      final Builder1 builder;

      private Builder3(Builder1 builder) {
        this.builder = builder;
      }
      public Builder4 height(Integer height) {
        this.builder.height = height;
        return new Builder4(this.builder);
      }
    }
    public static final class Builder4 {
      final Builder1 builder;

      private Builder4(Builder1 builder) {
        this.builder = builder;
      }
      public Builder4 eyeColor(String eyeColor){
        this.builder.eyeColor = Optional.of(eyeColor);
        return this;
      }
      public Builder4 hairColor(String hairColor){
        this.builder.hairColor = Optional.of(hairColor);
        return this;
      }
      public Builder4 hobby(String hobby){
        this.builder.hobby = Optional.of(hobby);
        return this;
      }
      public Person build() {
        return new Person(this.builder);
      }
    }
  }

  public String name() {
    return this.name;
  }
  public Integer age() {
    return this.age;
  }
  public String gender() {
    return this.gender;
  }
  public Integer height() {
    return this.height;
  }
  public Optional<String> eyeColor() {
    return this.eyeColor;
  }
  public Optional<String> hairColor() {
    return this.hairColor;
  }
  public Optional<String> hobby() {
    return this.hobby;
  }

  @Override
  public String toString() {
    return "Person(name=" + this.name + ", age=" + this.age + ", gender=" + this.gender + ", height=" + this.height + ", eyeColor=" + this.eyeColor + ", hairColor=" + this.hairColor + ", hobby=" + this.hobby + ")";
  }

  public void doSomething() {
      // do something
  }
} 
```

## **发电机提示**

下面的想法是针对典型代码输出的，生成的代码可以在不设置细节的情况下尽可能地使用。

### **有效的 Java 模板**

*   对于**列表中的< >** 类型如`List<String> hobby;`，生成用`new ArrayList();`初始化的代码
*   同样，在`List`的情况下，生成一个类似于`addHobby(String hobby)`的方法，以便可以单独添加项目。

### **GoF 模板**

*   GoF 的 Builder 模式在构造上比其他模式更复杂，生成名为`AppMain.java`的主类。
*   给**Director.java**的`construct()`方法添加临时代码。

```
public void construct() {
    builder.name("something");
    builder.age(0);
    builder.sex("something");
    //builder.hobby(new ArrayList<>());
  } 
```

希望有帮助。