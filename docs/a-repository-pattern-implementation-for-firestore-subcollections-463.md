# firestorm 子集合的存储库模式实现

> 原文：<https://dev.to/giodiblasi/a-repository-pattern-implementation-for-firestore-subcollections-463>

如果你已经使用过 [Google Cloud Firestore](https://firebase.google.com/docs/firestore) ，你可能知道它的[数据模型](https://firebase.google.com/docs/firestore/data-model)，并且你会注意到**子集合**是最有趣的特性之一。我第一次见到他们是在做我个人最喜欢的项目时(在这个项目中，我可以尝试新的技术和方法)。

因此，我想知道是否有一种方法可以调整**存储库模式**来处理 Firestore 子集合。
要解决的主要问题是，在存储库模式中，每个存储库通常与一个特定的模型相关，换句话说，它与一个类型相关。但是从[谷歌文档页面](https://firebase.google.com/docs/firestore/data-model#subcollections)阅读:

> 子集合是与特定文档相关联的集合。

这意味着，对于一个子集合，一个存储库与一个类型无关，而是与一个特定的*文档*相关。

让我们看看这个实验结果...🔬

## 场景

为了说明这一点，想象一下这个实际的例子:我们必须处理“房子”，每个房子都有一些“房间”(一个浴室，一个厨房，...).每个房间都有“家具”(一张床、两把椅子等等)。
为了对这个场景建模，我们可以设想实现这些实体:

*   家具项目
*   房间
*   房子

它们之间的关系是这样的:

```
Houses   <--- a collection
 |
 |--- My-House <---a Document of Houses
        |--Address <---- a field of "My-House" document
        |--Rooms   <----- a Subcollection of "My-House"
            |
            |--Kitchen
            |--Bathroom
            |--Bedroom <--- a document of Rooms
                    |
                    |--FurnitureItems  <--- a Subcollection of Bedroom document
                            |
                            |-- Bed
                            |-- Wardrobe 
```

## 储存库

为了简单起见，让我们集中讨论这两种方法

*   “添加”将文档添加到集合中
*   “GetById”通过 Id 返回文档

就界面而言，我们有:

```
public interface IRepository<T>{
    Either<String,String> Add(T data);
    Either<String, T> GetById(String id);
} 
```

暂且忽略子集合，实现类可以是:

```
public  class  FirestoreRepository<T> implements IRepository<T>{

    private CollectionReference collection;
    private Class<T> classType;

    public FirestoreRepository(Firestore firestore, String collectionPath, Class<T> classType){
        this.collection = firestore.collection(collectionPath);
        this.classType = classType;
    }

    public Either<String,String> Save(T data){ 
        try{
            ApiFuture<DocumentSnapshot> futureResult = collection.add(data).get().get();
            DocumentSnapshot result = futureResult.get();
            return Either.right(result.getId());
        }catch(Exception ex){
            return Either.left(ex.getMessage());
        }
    }

    public Either<String, T> GetById(String id) {
        try{
            ApiFuture<DocumentSnapshot> futureEntry = collection.document(id).get();
            DocumentSnapshot entry = futureEntry.get();
            return entry.exists()
                ? Either.right(entry.toObject(classType))
                : Either.left("Data not found for id: "+id);
        }catch(Exception ex){
            return Either.left(ex.getMessage());
        }
    }
} 
```

* * *

**注意:**
检索*documents sphot*的 GoogleApi 方法是异步的，它们确实返回了一个[API Future](https://googleapis.github.io/api-common-java/1.1.0/apidocs/com/google/api/core/ApiFuture.html)(Java Future 的 GoogleAPI 实现)。

为了简单起见，我选择避免异步方法阻塞执行，直到将来完成。是的，我知道，在实际项目中这样做不是个好主意🤓

* * *

上面的实现假设所有的集合都在根级别，在构造函数中集合引用确实是从 firestore 对象创建的:

```
this.collection = firestore.collection(collectionPath); 
```

当我们使用 firestore 子集合时，这个假设是不正确的。一个子集合与一个特定的文档相关，所以我们需要
一个 *DocumentReference* 来获取它的一个集合。
比如:

```
CollectionReference furnitureCollection = firestore
        .collection("Houses")
        .document("My-House")
        .collection("Rooms")
        .document("bathroom")
        .collection("FurnitureItems"); 
```

我们注意到我们需要链接*集合引用*和*文档引用*来访问右边的子集合。

因此，首先，我们需要修改 FirestoreRepository 的构造函数来接受一个 *CollectionReference* 。

```
public FirestoreRepository(CollectionReference collection, Class<T> classType){
        this.collection = collection;
        this.classType = classType;
    } 
```

这样，我们就消除了每个集合都在根级别的假设。

## 仓库工厂

现在让我介绍一下**仓库工厂**，其职责是:

*   链*集合引用*和*文档引用*
*   为特定集合(或子集合)创建 IRepository 实例。

例如，如果我们想要获得相对于“我的房子”的文档“浴室”的“furniture items”
的存储库，我们期望得到类似于:
的内容

```
 IRepository<FurnitureItem> furnitureRepo = repoFactory
        .FromDocument(House.class, "My-House")
        .FromDocument(Room.class, "bathroom")
        .GetRepository(FurnitureItem.class);

    furnitureRepo.Add(new FurnitureItem("chair")); 
```

否则，如果我们想通过*房子*集合进行查询，我们应该有:

```
 IRepository<House> houseRepo = repoFactory.GetRepository(FurnitureItem.class);
  houseRepo.Add(new House()); 
```

我们注意到来自文档的方法*应该返回一个新的 RepositoryFactory 实例。这允许我们保存一个 *RepositoryFactory* 引用来访问它的所有子集合。
例如考虑一个“房子”，除了“房间”集合之外，还有一个“房客”集合。在这个场景中我们可以写:* 

```
RepositoryFactory myHouseRepoFactory = repoFactory.FromDocument(House.class, "MyHouse");

myHouseRepoFactory
    .FromDocument(Room.class, "kitchen") //this do not modify the collection root of myHouseRepoFactory
    .GetRepository(FurnitureItem.class)
    .Add(new FurnitureItem());

myHouseRepoFactory
    .GetRepository(Tenant.class)
    .Add(new Tenant()); 
```

## 设计仓库工厂📐 ✏️

牢记一切，我们的*仓库工厂*需要:

1.  将模型类类型与集合名称链接起来的映射
2.  创建存储库的指针:如果存储库在根级别，那么它应该是 firestore 实例，否则应该是 *DocumentReference* 。
3.  将指针移动到下一个 *DocumentReference* 的方法，返回一个新的 *RepositoryFactory* 实例。
4.  获取指定集合(或*子集合*)的 *IRepository* 实例的方法

#### 模型-集合图

为了将每个模型类类型与相对集合名称联系起来，我们使用了这样一个映射:

```
private final static Map<Class<?>, String> collectionMap = Map.of(
        House.class, "Houses",
        Room.class, "Rooms",
        FurnitureItem.class, "Furniture"
    ); 
```

#### 指针

请记住，我们有两种指针:

*   如果集合位于根级别，则为 *Firestore* 实例
*   一个*文档引用*如果集合相对于一个特定的文档(也称为子集合)

我们可以保留一个对 *Firestore* 的引用和一个对 *DocumentReference* 的引用，然后我们应该有一些逻辑来选择使用哪一个。

```
public class RepositoryFactory{
    ....
    private Firestore firestore;
    private DocumentRefernce documentReference;
    private Boolean isRootLevel;

    public RepositoryFactory(Firestore firestore){
        this.firestore = firestore;
        isRootLevel = true;
    }

    private RepositoryFactory(DocumentReference documentReference){
        this.documentReference = documentReference;
        isRootLevel = false;
    }

    ....

    private CollectionReference getCollectionReference(String collectionName){
        return isRootLevel
            ? firestore.collection(collectionName)
            : documentReference.collection(collectionName);
    }
    ....
} 
```

但是当将指针从根级别移动到文档级别时，firestore 引用不再有用。有没有办法只保留我们需要的指针？

💡救援的功能方法！

我们实际上可以只保留对一个函数的引用，该函数给定一个集合名称，检索一个*集合引用* :

```
public class RepositoryFactory{
    ....
    private Function<String, CollectionReference> getCollectionReference;
    ....

    public RepositoryFactory(Firestore firestore){
        getCollectionReference = collectionPath -> firestore.collection(collectionPath);
    }

    private RepositoryFactory(DocumentReference document){
        getCollectionReference = collectionPath -> document.collection(collectionPath);
    }
    ....
} 
```

这样，RepositoryFactory 的每个实例只保留对右指针的引用。

#### 将指针移动到特定的文档

要在*文档引用*中移动，我们不想修改现有的*仓库工厂*实例，但是我们必须创建一个新的实例，以新的*文档引用*作为指针:

```
 public  RepositoryFactory FromDocument(Class<?> classType, String documentId){
        String collectionName = collectionMap.get(classType);
        DocumentReference documentReference = getCollectionReference.apply(collectionName).document(documentId);
        return new RepositoryFactory(documentReference);
    } 
```

#### 为给定的模型创建一个存储库

此时，我们需要一个方法，给定一个模型类类型，返回一个 *IRepository < >* 实例:

```
 public <T> IRepository<T> GetRepository(Class<T> classType){
        String collectionName = collectionMap.get(classType);
        return new FirestoreRepository<T>(getCollectionReference.apply(collectionName), classType);
    } 
```

## 收拾残局

最后，我们的存储库模式实现应该是这样的:

```
 //IRepository.java
public interface IRepository<T>{
    Either<String,String> Add(T data);
    Either<String, T> GetById(String id);
}

//FirestoreRepository.java
public  class  FirestoreRepository<T> implements IRepository<T>{

    private CollectionReference collection;
    private Class<T> classType;

    public FirestoreRepository(CollectionReference collection, Class<T> classType){
        this.collection = collection;
        this.classType = classType;
    }

    public Either<String,String> Add(T data){ 
        try{
            DocumentSnapshot result = collection.add(data).get().get().get();
            return Either.right(result.getId());
        }catch(Exception ex){
            return Either.left(ex.getMessage());
        }
    }

    public Either<String, T> GetById(String id) {
        try{
            DocumentSnapshot entry = collection.document(id).get().get();
        return entry.exists()
                ? Either.right(entry.toObject(classType))
                : Either.left("Data not found for id: "+id);
        }catch(Exception ex){
            return Either.left(ex.getMessage());
        }
    }
}

//RepositoryFactory.java
public class RepositoryFactory{

    private Function<String, CollectionReference> getCollectionReference;

    private final static Map<Class<?>, String> collectionMap = Map.of(
        House.class, "Houses",
        Room.class, "Rooms",
        FurnitureItem.class, "Furniture"
    );   

    public RepositoryFactory(Firestore firestore){
        getCollectionReference = collectionPath -> firestore.collection(collectionPath);
    }

    private RepositoryFactory(DocumentReference document){
        getCollectionReference = collectionPath -> document.collection(collectionPath);
    }

    public  RepositoryFactory FromDocument(Class<?> classType, String documentId){
        String collectionName = collectionMap.get(classType);
        DocumentReference documentReference = getCollectionReference.apply(collectionName).document(documentId);
        return new RepositoryFactory(documentReference);
    }

    public <T> IRepository<T> GetRepository(Class<T> classType){
        String collectionName = collectionMap.get(classType);
        return new FirestoreRepository<T>(getCollectionReference.apply(collectionName), classType);
    }
} 
```

## 总而言之

这就是我如何实现存储库模式来处理 Firestore 子集合的。对于这个例子，我选择了使用 Java，但是它应该很容易移植到其他语言。

## 开分

在这篇文章中，我忽略了一些可以在单独的主题中解决的方面:

*   如何应对 Google *ApiFuture* 和 Java *CompletableFuture* 。
*   如何处理特定于模型的查询？

## 技术

*   Java 语言(一种计算机语言，尤用于创建网站)
*   大爆发
*   子集合
*   知识库模式

让我知道您使用子集合的方式！